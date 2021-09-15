## 문제
```
'setUp()' in 'com.postsquad.scoup.web.group.GroupAcceptanceTest' clashes with 'setUp()' in 'com.postsquad.scoup.web.AcceptanceTestBase'; attempting to assign weaker access privileges ('package-private'); was 'public'
```
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class AcceptanceTestBase {

    protected static final String BASE_URL = "http://localhost";

    @LocalServerPort
    protected int port;

...

    @Autowired
    private DatabaseCleanup databaseCleanup;

    @BeforeEach
    public void setUp() {
        if (RestAssured.port == RestAssured.UNDEFINED_PORT) {
            RestAssured.port = port;
            databaseCleanup.afterPropertiesSet();
        }

        databaseCleanup.execute();
    }
}
```
위의 상황에서 setUp메서드가 UserAcceptanceTest 내부의 setUp 메서드와 충돌해서 발생
```java
class UserAcceptanceTest extends AcceptanceTestBase {

    @Autowired
    UserRepository userRepository;

    @BeforeEach
    void setUp() {
        userRepository.save(User.builder()
                                .nickname("existing")
                                .username("username")
                                .email("existing@email.com")
                                .avatarUrl("avatarUrl")
                                .password("password")
                                .build());
    }
}
```
더 약한 접근권한을 가진 메서드로 더 강한 접근 권한을 가진 메서드를 오버라이딩하려 했기 때문에 발생했다.

## 해결
현재 상황은 두 가지 다 실행되어 하고 메서드가 Override되면 안되기 때문에 AcceptanceTest 내부 메서드 이름을 변경해주었다.
```java
    @BeforeEach
    void cleanUpDatabase() {
        if (RestAssured.port == RestAssured.UNDEFINED_PORT) {
            RestAssured.port = port;
            databaseCleanup.afterPropertiesSet();
        }

        databaseCleanup.execute();
    }
```
