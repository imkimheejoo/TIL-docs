## JPQL(Java Persistance Query Language)
> 객체지향 쿼리언어<br>
> 따라서 **엔티티를 대상으로 쿼리문을 작성** 
- JPQL은 SQL 을 추상화한 언어이기 때문에 **다른 데이터베이스에 의존 X**
- JPQL은 실행 시 **SQL로 변환**

### JPQL 문법
```text
select_문 ::=
    select 절
    from 절
    [where] 절
    [group by] 절
    [having] 절
    [orderby] 절

update_문 ::= 
    update 절 [where] 절

delete_문 ::= 
    delete 절 [where] 절
```
- 벌크 연산시 사용
- 엔티티와 속성은 대소문자 구분
- 엔티티이름을 사용
- **별칭 필수**(as 생략 가능)

### TypeQuery, Query
> TypeQuery: 반환타입 명확할 때, <br>
> Query: 반환타입 불명확할 때

- TypeQuery
```java
TypeQuery<String> names = em.createQuery(select m.username from Member m where m.age > 18", String.class);
```
- Query
```java
//String, Integer 중 어떤게 반환타입인지 알 수 없음
Query result = em.createQuery(select m.username, m.age from Member m");
```

### 결과조회 API
- `getResultList()` : 결과가 **하나 이상** (없으면 빈 리스트)
- `getSingleResult()` : 결과가 **무조건 하나**
    - 업으면 `NoResultException` 예외 -> Spring Data Jpa 에서 null 또는 Optional로 반
    - 둘 이상이면 `NonUniqueResultException`환
    
### 파라미터 바인딩
> 이름기준
```sql
query = em.createQeury("select m from Member m where m.username=:username");
query.setParameter("username", "member1");
```
> 위치기준 (지양)
```sql
query = em.createQuery(select m from Member m where m.username=?1);
query.setParameter(1,"member1);
```
- 중간에 무언가 추가되면 숫자가 바뀌면서 오류가 날 수 있음

### Projection(Select)
> select 절에 **조회할 대상을 지정**하는 것
- 프로젝션 대상: `엔티티`,`임베디드 타입`, `스칼라타입`(숫자,문자,,,기본 데이터 타입)
    - 엔티티 프로젝션
        ```sql
        select m from Member m;
      select m.team from Member m;  //team 엔티티 조회
      ```
    - 임베디드 타입 프로젝션
        ```sql
        select m.address from Member m;  //Address 값 타입 조회
      ```
    - 스칼라 타입 프로젝션
        ```sql
        select m.username, m.age from Member m  
      ```
    - `distinct`로 중복제거 가능
    
### 프로젝션 여러깂 조회
 ```sql
        select m.username, m.age from Member m  
 ```
1. Query 타입으로 조회
2. Object[] 타입으로 조회
```java
Object o = result.getResultList().get(0);
Object[] ol = (Object[]) o;
sout(ol[0]); //username
sout(ol[1]); //age
```
3. **new 명령어로 조회**
    - 단순한 경우 DTO로 바로 조회
        `select new jpa.book.jpql.UserDTO(m.username, m.age) from Member m`
    - **패키지 명을 포함한 전체 클래스 명 입력**
    - 순서와 타입이 일치하는 **생성자 필요** 
    
    
### 페이징
- JPA는 페이징을 다음 두 API로 추상화
- setFirstResult(int startPosition) : 조회 시작 위치
- setMaxResults(int maxResult) : 최대 몇개 가져올가 것인가
```java
List<Member> resilt = em.createQuery("select m from Member m order by m.age desc", Member.class)
                        .setFirstResult(0)
                        .setMaxResults(10)
                        .getResultList();
```

### JOIN
- 내부 조인
`select m from Member m [inner] join m.team t`
- 외부 조인
`select m from Member m left [outer] join m.team t`
- 세타 조인(카르테시안 곱)
    - 연관관계가 없는 엔티티들에 대한 조인
`select count(m) from Member m, Team t where m.username = t.name`
- ON 절을 활용한 JOIN(SQL 문법의 on과 다름)
    - 조인 대상 **필터링**
    - **연관관계 없는 엔티티 필드로 외부 조인**
    
### SubQuery
- JPA는 **where, having, select 절에만 서브쿼리 가능**
- 즉 from 절의 서브쿼리는 불가능
    - JOIN 으로 풀어서 해결하거나 애플리케이션에서 필터링하거나 쿼리를 두번 보내거나 정말 방법이 없을때 nativeSQL 사용
    
### JPQL 타입표현
- 문자 : 'Hello', 'She"s'
- 숫자 : 10L, 10D(double), 10F(float)
- ENUM : 패키지명 포함,setType
- Boolean: TRUE, FALSE (대소문자 상관X)
- 엔티티 타입 : where type(i) = Book 

### 조건식
> 기본 case 식
```sql
select 
    case when m.age <= 10 then '학생요금'
        when m.age >= 60 then '경로요금'
        else '일반요금'
    end
from Member m
    자
```
> 단순 case 식
```sql
select 
    case t.name
        when '팀A' then '인센110%'
        when '팀B' then '인센120%'
        else '인센티브105%'
    end
from Team t
```
> coalesce : null 아니면 반환
```sql
// null 이면 '이름없는회원' 반환
select coalesce(m.username, '이름없는회원') from Member m
```
> nullif : 두 값이 같으면 null, 다르면 첫번째 값 반환
```sql
// 이름이 '관리자' 이면 null 반환, 아니면 자기이름 반환
select nullif(m.username, '관리') from Member m
```

### JPQL 함수
> JPQL 기본함수
- CONCAT
- SUBSTRING
- TRIM
- LOWER, UPPER
- LENGTH
- LOCATE
- ABS, SQRT, MOD
- SIZE: 컬렉션의 크기
- INDEX

> 사용자 정의함수
- group_concat이라는 함수를 등록하려고 할 때
    1. Dialect를 만들어야함
        - 내가 쓰는 Dialect 상속
        - dialect 등록해야함
    2.`select function('group_concat', i.name) from Item i`