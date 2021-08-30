## 문제
```
org.springframework.dao.DataIntegrityViolationException: could not execute statement; SQL [n/a]; constraint ["FKso8s55pr608xtav5r54if9qwg: SCOUP.o_auth_info FOREIGN KEY(user_id) REFERENCES SCOUP.user(id) (2)"; SQL statement:
delete from "user" where "id"=? [23503-200]]; nested exception is org.hibernate.exception.ConstraintViolationException: could not execute statement
```
데이터베이스 테이블에서 null이 될 수 없는 속성을 null로 하이버네이트에 저장하려고 할 때 발생
즉 테이블의 제약조건을 지키지 않고 저장하려고 하면 발생

---

- https://stackoverflow.com/questions/21727573/how-to-resolve-could-not-execute-statement-sql-n-a-constraint-numbering
- https://www.baeldung.com/spring-dataIntegrityviolationexception
