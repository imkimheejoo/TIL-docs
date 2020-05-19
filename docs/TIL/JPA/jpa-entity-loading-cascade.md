## 즉시로딩 & 지연로딩

### 즉시로딩
> fetch = FetchType.EAGER
- 즉시로딩 쓰면 예상치 못한 SQL 발생
- JPQL 에서 N + 1 문제를 일으킴 (최적화를 지원해주지 않는다. member,team 두번 쿼리)
    - select * from Member
    - select * from Tram where TEAM_ID = ... (teamA)
    - select * from Tram where TEAM_ID = ... (teamB)
    - 해결방법 : `패치조인`, `엔티티그래프`, `배치사이즈`
- `@ManyToOne`, `@OneToOne` 은 기본으로 즉시로딩
### 지연로딩
> fetch = FetchType.LAZY
- `@OneToMany`, `@ManyToMany` 은 기본으로 즉시로딩

---

## Cascade & 고아객체

### Cascade (영속성 전이)
- 연관관계 세팅과 전혀 관계 없음
- 부모를 저장할때(persist) 자식도 다 저장(persist 호출)
- 영속화 편리를 위한 것
- **하나의 부모만(관리하는 엔티티가 하나)** 존재할 때 유용
- **라이프 사이클**이 똑같을 때 유용
- ex) 게시글-첨부파일

***종류***
- ALL
- PERSIST : 영속
- REMOVE : 삭제

### 고아객체
> orphanRemovel= true
- 고아객체 제거: 부모엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제
- **참조하는 곳이 하나일때 사용해야함**
- **특정엔티티가 개인 소유할 때 사용**
- ex) 게시글-첨부파일

### 영속성 전이 & 고아객체 & 생명주기
> CascadeType.All + orphanRemovel = true
- 부모엔티티를 통해 자식엔티티의 생명주기(persist,remove..)를 관리할 수 있음
- DDD의 **Aggregate Root** 개념을 구현할 때 유용