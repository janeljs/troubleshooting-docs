## 문제
```
Could not transfer artifact opensymphony:sitemesh:pom:2.4.1-p1 from/to maven-default-http-blocker (http://0.0.0.0/): Blocked mirror for repositories: 
```

## 해결
- Preferences > Build, Execution, Deployment > Build Tools > Maven에서 Maven home path를 이전 버전의 Maven으로 설정
- Maven 3.8.1 버전부터 HTTP repository를 블록하는 것이 기본 설정으로 지정되기 때문에 문제 발생
- repository 프로토콜을 HTTPS로 바꾸거나 `maven-default-http-blocker` 설정을 주석처리 해도 된다. 

**출처**    
[링크 1](https://stackoverflow.com/questions/67001968/how-to-disable-maven-blocking-external-http-repositores)  
[링크 2](https://stackoverflow.com/questions/66980047/maven-build-failure-dependencyresolutionexception)
