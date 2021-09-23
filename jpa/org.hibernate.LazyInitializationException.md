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

*FetchType을 Eager로 변경  

---
- https://eocoding.tistory.com/31
- https://www.baeldung.com/hibernate-initialize-proxy-exception

