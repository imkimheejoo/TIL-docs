## 연관관계 매핑 - 양방향

### 양방향
- 반대방향으로 객체 그래프 탐색하기 위해 등장
- mappedBy: 상대방의 mappedBy 값으로 연결되어 있는 값이다
```java
@OneToManey(mapperdBy = "team")
private List<Member> memvers = new ArrayList<>();
``` 
### 변경이 일어날 때 어디를 변경해야하는가?
```text
Member (N) ------------ Team (1)
A 사원이 팀을 바꾸려고 할때 Member 객체를 변경해야 하는가? Team 객체를 바꿔야하는가?
```
=> Member 객체만 바꾸면 `team.getMembers();` 힐 때 그대로 A사원이 등장<br>
=> Team 객체만 바꾸면 `member.getTeam();` 할 때 예전 팀이 등장 <br>
**둘 중 하나로 외래키를 관리해야한다!**

### 연관관계 주인(둘 중 하나로 외래키를 관리해야한다!)
- 관리? : == 등록, 수정
- 주인이 아닌쪽은 read 만 가능
- mappedBy는 ~에 의해 매핑이 되었다. == 나는 주인이 아니다.
    - 주인이 아닌쪽에 mappedBy 설정!
- 누구를 주인으로? **외래키가 있는 쪽**
    - Team 엔티티를 바꿨는데 실제 쿼리는 Member 에 반영이 됨..!(앞뒤가 다른느낌,,)

### 양방향 매핑시 주의점1  = 연관관계의 주인에 값 입력X
- 양방향일 때 양쪽모두에 값을 넣어줘야 하는거 아닌가요? (addMember, setTeam)
```text
NO!
Member 엔티티에 setTeam만 해주면 된다! = 연관관계의 주인 엔티티에만 신경쓰자!
현재 team은 members가 지연로딩이 발생되므로 proxy값이 나옵니다. 
team.getMembers()하는 순간 이때 db에서 실제 members를 가져옵니다. 그렇기 때문에 addMembers안해도 나중에 DB에서 값 잘 찾아옴!
(현재 team은 member, team을 insert하고 em.flush, em.clear된 상태에서 em.find로 가져온 상태)
가정 : 꼭 em.flush가 되어야 함!!!
```
- 그러면 순수한 객체를 고려하는 경우는 양쪽모두에 넣어야 되는 건가요?
```text
YES!
순수한 객체상태는 em.flush를 안한, 즉 디비와 연관이 되지 않은 순수 객체를 의미함으로 직접 add, set해줘야 한다.
이런경우가 Test 작성할 때가 있음
```
**=> 순수 객체 상태를 고려해서 항상 양쪽에 값을 설정하자!**

### 연관관계 편의 메서드 = 양쪽에 값을 설정하자!
```java
//Before -> 엔티티를 사용하는 쪽에 addMembers();
team.getMembers().add(멤버엔티티);

//After -> 엔티티에 addMembers()
public void changeTeam(Team team) {
    this.team = team;
    team.getMembers().add(this);
}
```
- 어디에 연관관계 편의메서드를 둘 지는 자기 마음! (한 엔티티에서만 넣으면 됨)

### 양방향 매핑시 주의점2  = 무한루프
- 롬복이나 JSON 생성 라이브러리에서 문제 발생

***Example 1***

엔티티를 컨트롤러에서 response로 보내버리면 JSON으로 바뀌면서 무한루프

***Example 2***
```java
//Team
public String toString() {
    ... + "members=" + members + ...
}
```
- member를 호출 (member의 toString을 호출)

```java
//Member
public String toString() {
    ... + "team=" + team + ...
}
```
- team을 호출 (team의 toString을 호출) => **무한반복**

### 양방향 지양..
- 단방향 매핑을 지향
- 양방향 매핑은 반대방향으로 조회기능만 추가된것 뿐
- JPQL에서 역방향으로 탐색할 일이 많음
- 단방향 매핑을 잘하고 양방향은 그 기능이 필요할 때에 추가하자!