## 프록시 

### em.find() vs em.getReference()
- em.find() : 실제 엔티티를 find
- em.getReference() : 참조한다. 즉, 실제 대상이 아닌 가짜(프록시)를 조회

### 특징
- 한번만 초기화
- 초기화 되면 프록시가 실제 엔티티로 바뀌는 것이 아님
- 타입체크 유의 (instanceof)
- find()하고 getReference하면 실제 엔티티가 이미있으므로 프록시안하고 그냥 실제엔티티 사용
- 준영속(detach()) 상태일때 프록시를 초기화(ex. getId()) 하면 문제발생

### 초기화 과정
- 프록시 객체에서 getId()
- 영속성 컨텍스트한테 물어봄 => 있니?
- 있으면 그거 가져옴
- 없으면 영속성 컨텍스트가 디비에서 조회해서 프록시에 실제 엔티티를 끼워넣음

### 프록시 확인
- 프록시 인스턴스의 초기화 여부 확인
```java
emf.getPersistenceUnitUtil.isLoaded(Object entity);
```
- 프록시 클래스 확인방법
```java
sout(entity.getClass().getName());
```
- 프록시 강제 초기화
```java
Hibernate.initialize(entity);
```