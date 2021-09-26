## 문제
```
java.lang.IllegalStateException: org.hibernate.TransientPropertyValueException: 
object references an unsaved transient instance - save the transient instance before flushing : 
com.postsquad.scoup.web.schedule.domain.Schedule.confirmedSchedule -> com.postsquad.scoup.web.schedule.domain.ConfirmedSchedule
```
위의 에러는 

```java
schedule.setConfirmedSchedule(confirmedSchedule);
entityManager.persist(group);
```
확정 일정을 persist하지 않고 스케줄에 확정 일정을 세팅하려고 하니 `TransientPropertyValueException`이 발생했습니다. 
설명에도 나와있듯이 데이터베이스에 저장되지 않고 영속화되지도 않은 ConfirmedSchedule 객체와 연관관계가 있는 객체를 persist하기 때문에 발생하는 문제입니다.

## 해결
```java
@Entity
public class Schedule extends BaseEntity {
    @OneToOne(cascade = CascadeType.PERSIST)
    @JoinColumn
    private ConfirmedSchedule confirmedSchedule;
}
```

```java
@Entity
public class ConfirmedSchedule extends BaseEntity {
    @OneToOne(mappedBy = "confirmedSchedule")
    @Setter
    private Schedule schedule;
}
```

연관관계의 주인이 되는 쪽에 Cascade 타입을 Persist로 변경하여 해결하였습니다.
