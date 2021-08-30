## 문제
```
org.hibernate.LazyInitializationException: failed to lazily initialize a collection of role
```
영속성 컨텍스트가 종료되어 버려 지연 로딩을 할 수 없을 때 발생하는 에러
보통 트랜잭션 밖에서 엔티티를 조회할 때 발생
즉, 프록시 객체를 통해 데이터베이스에서 lazy-loading된 객체를 가져오려고 하지만 하이버네이트 세션은 이미 종료되었을 때 발생하는 에러이다.

### Session
- 어플리케이션과 데이터베이스를 이어주는 영속성 컨텍스트
### Lazy Loading
- 코드에 의해 접근되기 전까지 객체가 세션 컨텍스트에 로딩되지 않을 것이라는 의미
### Proxy Object
- 실제 클래스를 상속받아 만들어짐
- 처음 사용될 때 한 번만 초기화 됨

## 해결
FetchType을 Eager로 변경  
`@Transactional` 어노테이션 붙이기


---
- https://eocoding.tistory.com/31
- https://www.baeldung.com/hibernate-initialize-proxy-exception
