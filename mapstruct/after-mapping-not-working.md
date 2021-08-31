## 문제

```java
    @AfterMapping
    default void addOAuthInfo(@MappingTarget User user, SignUpRequest signUpRequest) {
        ...
    }
```
- 위 코드에서 `@AfterMapping` 어노테이션이 작동하지 않음.
```java
@Mapper
public interface CarMapper {
  void updateCarFromDto(CarDto carDto, @MappingTarget Car car);
}
```
- `@MappingTarget`을 업데이트하기 위해 공식 문서에는 위와 같이 나와있는데 왜 안되는 건지 모르겠음.





## 해결
> MapStruct also supports mapping of immutable types via builders. When performing a mapping
MapStruct checks if there is a builder for the type being mapped. This is done via the
BuilderProvider SPI. If a Builder exists for a certain type, then that builder will be used for the
mappings.

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
- https://mapstruct.org/documentation/dev/reference/pdf/mapstruct-reference-guide.pdf
