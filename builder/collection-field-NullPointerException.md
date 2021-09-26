## 문제
` .schedules(new ArrayList<>())` 부분이 없으면 NullPointerException이 발생합니다.
`private final List<Schedule> schedules = new ArrayList<>();`에서 필드 초기화가 되었는데 왜 NPE가 발생하는 걸까요?
```java
    @OneToMany(mappedBy = "group", cascade = CascadeType.ALL)
    private final List<Schedule> schedules = new ArrayList<>();

    protected Group(String name, String description, User owner) {
        this.name = name;
        this.description = description;
        this.owner = owner;
    }

    @Builder
    public static Group of(String name, String description, User owner, List<Schedule> schedules) {
        Group group = new Group(name, description, owner);
        group.addSchedules(schedules);
        return group;
    }
```
```java
    @Override
    public Stream<? extends Arguments> provideArguments(ExtensionContext context) throws Exception {
        return Stream.of(
                Arguments.of(
                        "성공",
                        GroupCreationRequest.builder()
                                            .name("group name")
                                            .description("description")
                                            .build(),
                        Group.builder()
                             .name("group name")
                             .description("description")
                             .schedules(new ArrayList<>()) // 이 부분
                             .owner(User.builder()
                                        .nickname("nickname")
                                        .email("email@email.com")
                                        .password("password")
                                        .avatarUrl("url")
                                        .username("username")
                                        .oAuthUsers(List.of(OAuthUser.of(OAuthType.NONE, "")))
                                        .build())
                             .build()
                )
        );
    }
```

## 해결

```java
    public static class GroupBuilder {
       ...
        private List<Schedule> schedules;

        GroupBuilder() {
        }
       ...
        public Group.GroupBuilder schedules(List<Schedule> schedules) {
            this.schedules = schedules;
            return this;
        }
}
```
생성된 빌더 클래스를 확인하니 리스트를 통채로 교체하고 있었습니다.

구글링을 해보니 같은 문제를 겪고 있는 사람들이 있었는데요.

https://github.com/projectlombok/lombok/issues/1347

`@Builder.Default` 사용에 관련해서는 아직 문제가 해결되지 않은 것처럼 보였습니다.
https://github.com/projectlombok/lombok/issues/2340


```java
    @Builder
    private SearchRequest(SearchContext context,
                          @Singular List<RequestFilter> filters) { 
        this.searchContext = Optional.ofNullable(context).orElse(this.searchContext);
        this.filters = filters;
    }
```

https://github.com/projectlombok/lombok/issues/1347#issuecomment-455429294 의 커멘트를 참고하여 `@Singular` 어노테이션을 붙이니 잘 작동합니다. 해당 어노테이션을 추가한 뒤 생성된 빌더를 확인해보면 아래와 같습니다. 

```java
        public Group.GroupBuilder schedule(Schedule schedule) {
            if (this.schedules == null) {
                this.schedules = new ArrayList();
            }

            this.schedules.add(schedule);
            return this;
        }

        public Group.GroupBuilder schedules(Collection<? extends Schedule> schedules) {
            if (schedules == null) {
                throw new NullPointerException("schedules cannot be null");
            } else {
                if (this.schedules == null) {
                    this.schedules = new ArrayList();
                }

                this.schedules.addAll(schedules);
                return this;
            }
        }
```

> The singular annotation is used together with @Builder to create single element 'add' methods in the builder for collections.

Schedule 하나를 add할 수 있는 schedule 메서드가 추가되었습니다.
