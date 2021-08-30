## 문제
```
org.hibernate.LazyInitializationException: failed to lazily initialize a collection of role
```
영속성 컨텍스트가 종료되어 버려 지연 로딩을 할 수 없을 때 발생하는 오류
보통 트랜잭션 밖에서 엔티티를 조회할 때 발생
