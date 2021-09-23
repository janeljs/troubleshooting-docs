## 문제

매핑의 대상이 되는 클래스가 Entity가 아닐 때 발생합니다. 

```
One To One' attribute type should not be 'Schedule'
```

```java
    @OneToOne(mappedBy = "confirmedSchedule")
    private Schedule schedule;
```

## 해결

Schedule 클래스에 `@Entity` 어노테이션을 붙이니 해결되었습니다. 

```java
@Entity
public class Schedule extends BaseEntity { ... }
```
