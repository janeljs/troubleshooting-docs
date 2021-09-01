## 문제 상황
```
javax.validation.UnexpectedTypeException: HV000030: No validator could be found for constraint 'javax.validation.constraints.NotEmpty' validating type 'com.postsquad.scoup.web.auth.OAuthType'. Check configuration for 'oAuthType'
	at org.hibernate.validator.internal.engine.constraintvalidation.ConstraintTree.getExceptionForNullValidator(ConstraintTree.java:116) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.constraintvalidation.ConstraintTree.getInitializedConstraintValidator(ConstraintTree.java:162) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.constraintvalidation.SimpleConstraintTree.validateConstraints(SimpleConstraintTree.java:54) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.constraintvalidation.ConstraintTree.validateConstraints(ConstraintTree.java:75) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.metadata.core.MetaConstraint.doValidateConstraint(MetaConstraint.java:130) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.metadata.core.MetaConstraint.validateConstraint(MetaConstraint.java:123) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.ValidatorImpl.validateMetaConstraint(ValidatorImpl.java:555) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.ValidatorImpl.validateConstraintsForSingleDefaultGroupElement(ValidatorImpl.java:518) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.ValidatorImpl.validateConstraintsForDefaultGroup(ValidatorImpl.java:488) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.ValidatorImpl.validateConstraintsForCurrentGroup(ValidatorImpl.java:450) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.ValidatorImpl.validateInContext(ValidatorImpl.java:400) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.hibernate.validator.internal.engine.ValidatorImpl.validate(ValidatorImpl.java:172) ~[hibernate-validator-6.2.0.Final.jar:6.2.0.Final]
	at org.springframework.validation.beanvalidation.SpringValidatorAdapter.validate(SpringValidatorAdapter.java:109) ~[spring-context-5.3.9.jar:5.3.9]
```
hibernate validator annotation을 적합하지 않은 빈 속성에 사용하려고 해서 발생

```java
    @NotEmpty
    private OAuthType oAuthType;
```

## 해결
- enum은 @NotNull 또는 @Null을 써야 한다.
```java
    @NotNull
    private OAuthType oAuthType;
```

---
https://www.baeldung.com/javax-validations-enums
https://howtodoinjava.com/hibernate/unexpectedtypeexception-error/
