## 문제
```java
    @BeforeEach
    void setUp() {
        Group group = entityManager.find(Group.class, 1L);
        schedule = Schedule.builder()
                           .group(group)
                           .title("schedule title")
                           .description("schedule description")
                           .dueDateTime(LocalDateTime.of(2021, 9, 23, 0, 0))
                           .build();
        group.addSchedule(schedule);
        entityManager.persist(group);
    }
```
```java
    public void addSchedule(Schedule schedule) {
        this.schedules.add(schedule);
        if (schedule.getGroup()!=this) {
            schedule.setGroup(this);
        }
    }
```
위의 코드에서 `group.addSchedule(schedule);`를 실행하는데 **org.hibernate.LazyInitializationException**이 발생했습니다.

```
org.hibernate.LazyInitializationException: failed to lazily initialize a collection of role: com.postsquad.scoup.web.group.domain.Group.schedules, could not initialize proxy - no Session
```
해당 에러는 영속성 컨텍스트가 종료되어 버려 지연 로딩을 할 수 없을 때 발생한다고 합니다.
즉, 프록시 객체를 통해 데이터베이스에서 lazy-loading된 객체를 가져오려고 하지만 하이버네이트 세션은 이미 종료되어 발생하는 에러입니다.
(보통 트랜잭션 밖에서 엔티티를 조회할 때 발생)

### Session
- 어플리케이션과 데이터베이스를 이어주는 영속성 컨텍스트
### Lazy Loading
- 코드에 의해 접근되기 전까지 객체가 세션 컨텍스트에 로딩되지 않을 것이라는 의미
### Proxy Object
- 실제 클래스를 상속받아 만들어짐
- 처음 사용될 때 한 번만 초기화 됨

## 해결
```
@Transactional
class ConfirmedScheduleAcceptanceTest extends AcceptanceTestBase { ... }
```
`@Transactional` 어노테이션을 붙여 해결했습니다. 


## 2차 문제 발생
테스트에 `@Transactional` 어노테이션을 붙이니 내부에서 API를 호출할 때 새로운 트랜잭션이 시작되어 한 테스트에 두 가지의 트랜잭션이 존재하는 문제가 발생했습니다.
#### 내용 추가
`@Transactional` 어노테이션은 같은 쓰레드에서만 동작한다. 그러나 restassured를 이용하여 테스트를 진행할 시 다른 쓰레드에서 동작한다. 
&rarr; JUnit 테스트가 embedded servlet container와 다른 쓰레드에서 실행되기 때문에 발생하는 문제이다. 
&rarr; 2개의 트랜잭션과 데이터베이스 커넥션이 열린다.

+) 스프링 컨테이너는 스레드마다 다른 트랜잭션을 할당하며, 트랜잭션이 다를 경우 다른 영속성 컨텍스트에 접근한다.

## 해결
테스트에서 해당 어노테이션을 제거하고 데이터 초기화를 담당하는 TestDataSetup 클래스를 정의하였습니다.
&rarr; 다른 테스트에서도 자주 쓰일 것 같아 TestEntityManager 클래스로 분리

## 3차 문제 발생
테스트 내부에서 RestAssured로 호출하는 메서드가 Lazy 로딩되는 컬렉션을 저장하려고 하면 트랜잭션이 유지되어야 하는데, 테스트 메서드에 `@Transactional` 어노테이션을 붙이면 `#2차 문제`가 발생합니다. 


## 해결 1
1. 임시 방편으로 EAGER 로딩으로 컬렉션을 변경하였으나 좋은 방법이 아니어 보입니다.
2. EAGER 로딩 시 발생하는 `MultipleBagFetchException`을 막기 위해 하이버네이트에서 제공하는  `@Fetch(value = FetchMode.SUBSELECT)`를 사용하였지만, 다른 구현체를 사용할 경우 작동하지 않습니다.
3. 보다 근본적인 해결책을 찾아야 할 것 같습니다.
```java
    @OneToMany(mappedBy = "group", cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    @Fetch(value = FetchMode.SUBSELECT)
    private final List<Schedule> schedules = new ArrayList<>();
```
## 해결 2 
[TestEntityManager](https://github.com/Sc0up/scoup-backend/blob/main/src/test/java/com/postsquad/scoup/web/TestEntityManager.java)에 findAndConsume() 메서드를 추가하여 임시로 해결 

## 해결 3
Filter를 이용해서 JUnit 테스트에서 열린 트랜잭션을 서블릿 컨테이너까지 전파하는 방법으로 해결할 수 있을 것 같습니다. 
```java
@Transactional
public class MyTest {
    private static final String[] FIELD_NAMES = {"resources", "synchronizations", "currentTransactionName", "currentTransactionReadOnly", "currentTransactionIsolationLevel", "actualTransactionActive"};
    private static final Field[] FIELDS = new Field[FIELD_NAMES.length];
    private static final Object[] FIELD_VALUES = new Object[FIELD_NAMES.length];

    @Before
    public void copyTransactionThreadLocals() {
        for (int i=0; i<FIELD_NAMES.length; i++) {
            Field field = ReflectionUtils.findField(TransactionSynchronizationManager.class,FIELD_NAMES[i]);
            field.setAccessible(true);
            FIELD_VALUES[i] = ((ThreadLocal)ReflectionUtils.getField(field,null)).get();
            FIELDS[i] = field;
        }
    }

    @Configuration
    static class Config {
        @Bean
        public Filter transactionFilter() {
            return new OncePerRequestFilter() {
                @Override
                protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
                    for (int i=0; i<FIELD_NAMES.length; i++) {
                        ((ThreadLocal)ReflectionUtils.getField(FIELDS[i],null)).set(FIELD_VALUES[i]);
                    }
                    filterChain.doFilter(request,response);
                }
            };
        }
    }
}
```



---
- https://eocoding.tistory.com/31
- https://www.baeldung.com/hibernate-initialize-proxy-exception
- https://stackoverflow.com/questions/41763417/api-test-transactional-rollback
