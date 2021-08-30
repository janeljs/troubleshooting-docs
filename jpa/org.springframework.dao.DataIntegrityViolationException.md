## 문제
```
org.springframework.dao.DataIntegrityViolationException: could not execute statement; SQL [n/a]; constraint ["FKso8s55pr608xtav5r54if9qwg: SCOUP.o_auth_info FOREIGN KEY(user_id) REFERENCES SCOUP.user(id) (2)"; SQL statement:
delete from "user" where "id"=? [23503-200]]; nested exception is org.hibernate.exception.ConstraintViolationException: could not execute statement
```
데이터베이스 테이블에서 null이 될 수 없는 속성을 null로 하이버네이트에 저장하려고 할 때 발생
즉 테이블의 제약조건을 지키지 않고 저장하려고 하면 발생
```java
    @AfterEach
    void tearDown() {
        userRepository.deleteAll();
    }
```
나의 경우는 o_auth_info 테이블에 있는 데이터를 먼저 삭제하지 못해 발생하는 에러였다.

## 해결
```
    @ElementCollection(fetch = FetchType.EAGER)
    @OnDelete(action= OnDeleteAction.CASCADE)
    @JoinColumn(name = "user_id")
    private List<OAuthInfo> oAuthInfo = new ArrayList<>();
```
JoinColumn을 정의해주고 OnDelete 어노테이션을 추가하여 해결하였다.


---
- https://stackoverflow.com/questions/7695831/how-can-i-cascade-delete-a-collection-which-is-part-of-a-jpa-entity
- https://pinokio0702.tistory.com/183
- https://github.com/spring-projects/spring-data-jpa/issues/1100
- https://stackoverflow.com/questions/42124030/delete-then-create-records-are-causing-a-duplicate-key-violation-with-spring-dat/42125507#42125507
- https://github.com/micronaut-projects/micronaut-data/issues/684
- https://stackoverflow.com/questions/21727573/how-to-resolve-could-not-execute-statement-sql-n-a-constraint-numbering
- https://www.baeldung.com/spring-dataIntegrityviolationexception
- https://velog.io/@woodyn1002/%EC%82%BD%EC%A7%88-%EB%A1%9C%EA%B7%B8-Hibernate%EC%97%90%EC%84%9C-%EB%B6%80%EB%AA%A8%EA%B0%80-%EB%91%98%EC%9D%B8-Entity%EC%9D%98-%ED%95%9C%EC%AA%BD-%EB%B6%80%EB%AA%A8%EB%A5%BC-%EC%A7%80%EC%9A%B0%EB%A9%B4-%EC%B0%B8%EC%A1%B0-%EB%AC%B4%EA%B2%B0%EC%84%B1-%EC%98%A4%EB%A5%98%EA%B0%80-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94-%EB%AC%B8%EC%A0%9C
- https://stackoverflow.com/questions/46425395/does-elementcollection-imply-orphanremoval
