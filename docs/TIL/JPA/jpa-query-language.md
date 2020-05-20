## 객체지향 쿼리언어

### 쿼리 방법
- **JPQL**
- JPA Criteria
- **QueryDSL**
- native SQL
    - 표준 SQL 에 벗어난 Mysql 만의 문법, Oracle 만의 문법 등등.. 
- JDBC API

### JPQL (객체지향 SQL)
> JPQL은 테이블이 아닌 **엔티티 객체를 대상**으로 쿼리
- 검색을 할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색
    - 모든 디비를 객체로 만들어서 검색하는건 불가능
- JPA는 SQL을 추상화한 JPQL 이라는 객체지향 쿼리언어 제공
    - 특정 DB에 의존하지 않음
- SQL 문법과 유사, SELECT, FROM, WHERE, GROUP BY, HAVING, JOIN 지원
- 나이가 18 살 이상인 회원을 모두 검색하고 싶다면?
```java
String jpql  = "select m from Member m where m.age > 18";
List<Member> result = em.createQuery(jpql, Member.class).getResultList();
```
- jpql 의 쿼리 타입 String이기 때문에 동적 쿼리가 어려움

### JPA Criteria
> 쿼리를 코드로 짜는 쿼리언어
```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Member> query =  cb.creageQuery(Member.class); // member에 관한 쿼리를 짤거야
Root<Member> m = query.from(Member.class); //조회는 member에서 할거야
...
List<Member> resultList = em.createQuery(cq).getResultList();
```
- 복잡한 쿼리를 작성할때 어려움(실용성 낮음)
- 동적쿼리를 짜기에 좋음

### QueryDSL
> 쿼리를 코드로 짜는 오픈 소스 라이브러리
```java
JPAFactoryQuery query = new JPAQueryFactory(em);
QMember m = QMember.member; // 별칭을 m으로 하갰다.

List<Member> list = query.selectFrom(m)
                        .where(m.age.gt(18))
                        .orderBy(m.name.desc())
                        .fetch();
```
- JPA Criteria보다 훨씬 단순하고 쉬움
- 동적 쿼리가능
- 실무사용

### Native SQL
> 실제 SQL 을 쓰는 기능
- JPQL로 해결 못하는 특정 디비 문법에 의존적인 기능
- 쿼리를 하면 강제로 flush됨 (자동 flush)
```java
em.createNativeQuery("쿼리문", (엔티티.class(선택사항))).getResultList();
```

### JDBC 직접 사용, SpringJdbcTemplate emd
> JPA를 사용하면서 (JDBC 커넥션을 직접 사용 || JdbcTemplate, Mybatis 함께 사용 가능)
- 영속성 컨텍스트를 **강제로 flush 해야함** (수동 flush)
    - JPA와 관련이 없기 때문에 수동작업
