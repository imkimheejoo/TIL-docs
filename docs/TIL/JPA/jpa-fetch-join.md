## JPQL - Fetch Join

### Fetch Join
> JPQL에서 성능 최적화흫 ㅟ해 재공하는 JPQL 전용 기능
> `join fetch + 원하는 연관엔티티`
- 연관된 엔티티나 컬렉션은 SQL 한번에 함께 조회하는 기능 -> 쿼리 두방을 한방쿼리로!
- 엔티티를 조회할때 연관된 엔티티를 어디까지 가져올지 정할 수 있음!
***example***
- 회원과 그회원의 팀을 함께 조회 
```sql
//JPQL
select m from Member m join fetch m.team

//SQL
select M.*, T.* 
from Member m inner join team T 
on m.team_id = t.id
```
- ManyToOne에도 N+1이 존재
    
### Collection Fetch Join
- 일대다 관계
***example***
- 팀A에 소속된 멤버들 조회 
```sql
//JPQL
select t from Team t join fetch t.members
where t.name = '팀A'

//SQL
select T.*, M.* 
from Team t inner join Member m 
on t.id = m.team_id
where t.name = '팀A' 
```
- 결과
```text
team = 팀A | members = 2
team = 팀A | members = 2
team = 팀B | members = 1

결과가 뻥튀기 되어서 나온다..(일대다만 그럼 다대일은 아님)
```
- 원인
- 일대 다 조인은 데이터가 무조건 뻥튀기
키노트
- 해결책
    - JPQL의 distinct는 두가지 기능 제공
        1. SQL에 DISTINCT 추가 (키노트)
        2. 애플리케이션에서 엔티티 중복제거

### Fetch Join vs 일반 Join
> 일반 조인
- 일반조인 실행시 **연관된 엔티티를 함께 조회하지 않음**
```sql
select t -> m을 가져오지 않음
from Team t join t.membets m
where t.name = '팀A'

select t.*  -> m을 가져오지 않음
from team t inner join member m 
on t.id = m.team_id
where t.name = '팀A'
```
- members를 가져올 때 계속 쿼리가 발생됨 (N+1)

> 페치조인
- **연관된 엔티티도 함께 조회**(즉시로딩과 같은 개념)
- 페치조인은 객체 그래프를 SQL 한번에 조회하는 개념

### 페치조인의 특징과 한계
- 페치 조인 대상에는 별칭을 줄 수 없다.
- 둘 이상의 컬렉션은 페치조인 X
- 컬렉션을 페치 조인하면 페이징 API(setFirstResult, serMaxResults)를 사용 X
       - 데이터 뻥튀 (키노트)
       - 하이버네이트에서 경고 로그를 남기고 메모리에서 페이징(데이터는 다 긁어온다음 페이징)
 - 연관된 엔티티들을 SQL 한 번으로 조회 - 성능 최적화
 - 엔티티에 직접 적용하는 글로벌 로딩 전략보다 우선순위 높
 ```java
@OneToMany(fetch = FetchType.LAZY) //글로벌 로딩전략
```
- 최적화가 필요한 곳은 페치조인 적용
- **여러테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀다른 결과는 일반조인을 사용하고 DTO로 반환**

### 다형성 쿼리
- 조회 대상을 특정 자식으로 한정
    - Item 중에 Book, Movie를 조회
    ```sql
  //JPQL
  select i from Item i
  where type(i) IN (Book, Movie)
  
  //SQL
  select i from i
  where i.DTYPE in ('B','M')
    ```
- Treat
    - 부모인 Item과 자식 Book이 있다.
      ```sql
      //JPQL
      select i from Item i
      where treat(i as Book).author = 'kim'
      
      //SQL
      select i from i
      where i.DTYPE ='B' and i.author = 'kim'
        ```
      
### 엔티티 직접 사용
- 기본 키 값
    - JPQL에서 **엔티티를 직접 사용하면** SQL에서 해당 엔티티의 **기본 키 값을 사용**
     ```sql
   //JPQL
            select count(m.id) from Member m
            select count(m) from Member m
            
            //SQL
            select count(m.id) as cnt from Member m
     ```
         
- 엔티티 외래키 값
```sql
          //JPQL
          select count(m.id) from Member where m.team =: team
          select count(m) from Member m where m.team.id =: teamId
          
          //SQL
          select count(m.id) as cnt from Member m where m.team_id = ?

```

### Named 쿼리
> 미리 정의해서 이름을 부여(쿼리에 이름을 부여)해두고 사용하는 JPQL (재사용 good)
- 정적쿼리
- 엔티티에 미리 `@NamedQuery` 선언
- 이름으로 쿼리를 불러와 사용 가능
- **애플리케이션 로딩시점에 초기화 후 재사용(caching)**
    - **애플리케이션 로딩 시점에 쿼리를 검증👍**
```java
@Entity
@NamedQuery(name = "Member.findByUsername",
            query = "select m from Member m where m.username = :username")
class Member{
..}

//Main
em.createNamedQuery("Member.findByUsername", Member.class)
    .setParameter("username", member1).getResultList();
```
- spring data jpa 에서 `@Qeury` 가 NamedQuery

### JPQL - 벌크연선
> 여러 데이터를 update,delete 문
- 재고가 10개 미만인 모든 상품의 가격을 10프로 상승
- 더치체킹으로 하려면 너무 많은 SQL 실행(10개가 바꼇으면 10번 update SQL문)
```java
em.createQuery("update Member m set m.age = 20").executeUpdate(); -> 영향을 받은 레코드 수가 반환됨
```
- select 한 값을 insert 가능
- 영속성 컨텍스트를 무시하고 데이터베이스에 직접 쿼리 -> 영속성 컨텍스트랑 꼬임
    - 해결책
        - 1안) (영속성 컨텍스트에 아무것도 없을 경우)벌크연산을 먼저 실행
        - 2안) (영속성 컨텍스트에 무언가가 존재하는 경우)벌크연산수행 후 영속성 컨텍스트 초기화