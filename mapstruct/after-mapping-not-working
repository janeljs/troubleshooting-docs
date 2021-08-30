## 문제

```java
    @AfterMapping
    default void addOAuthInfo(@MappingTarget User user, SignUpRequest signUpRequest) {
        ...
    }
```
- 위 코드에서 `@AfterMapping` 어노테이션이 작동하지 않음.

## 해결
```java
@Mapper
public interface UserMapper {

    UserMapper INSTANCE = Mappers.getMapper(UserMapper.class);

    User map(SignUpRequest signUpRequest);

    @AfterMapping
    default void addOAuthInfo(@MappingTarget User.UserBuilder userBuilder, SignUpRequest signUpRequest) {
        userBuilder.oAuthInfo(List.of(new OAuthInfo(signUpRequest.getOAuthType(), signUpRequest.getSocialServiceId())));
    }
}
```
- Builder를 매개변수로 받아야 한다.

---

- https://github.com/mapstruct/mapstruct/issues/1556
