## 문제
```
org.h2.jdbc.JdbcSQLSyntaxErrorException: 
Syntax error in SQL statement "TRUNCATE TABLE GROUP[*]"; expected "identifier"; SQL statement:
TRUNCATE TABLE group 
```
Group 엔티티가 데이터베이스 예약어/키워드와 충돌하기 때문에 발생

## 해결
```
spring.jpa.properties.hibernate.globally_quoted_identifiers=true
```
위의 설정을 추가 또는 테이블 이름을 `@Table(name = "groups")`와 같이 변경

