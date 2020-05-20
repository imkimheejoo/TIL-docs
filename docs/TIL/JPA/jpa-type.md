## 값 타입

### 기본값 타입
- 엔티티 타입
- 값 타입
    - 기본값 타입
        - 자바 기본 타입(int, double)
            - 생명주기를 엔티티에 의존
            - 값 타입은 공유하면 안됨(기본타입은 원래 공유 안됨)
            ```java
            int a = 10;
            int b = a;
            b = 20;
          // a : 10, b : 20  
          ```
        - 래퍼 클래스(Integer,String)
            - 래퍼는 공유가 가능해서 변경 가능(하지만 변경할 방법이 없음)
    - 임베디드 타입
        - 좌표
    - 컬렉션 값 타입
    
### 임베디드 타입(복합 값 타입)
- 엔티티의 값일 뿐
- 임베디드 타입을 사용하기 **전과 후에 매핑하는 테이블은 같다.**
- 새로운 값 타입을 직접 정의할 수 있음
- 주로 기본 값 타입을 모아서 만들어서 복합 값 타입이라고도 함
- int,String 과 같은 값 타입
- `@Embedded`: 값타입을 사용하는 곳에 표시(ex. Article 클래스의 content 필드)
- `@Embeddable` : 값타입을 정의하는 곳에 표시(ex. Content 클래스)
- 재사용이 가능
- 높은 응집도
- 임베디드 타입을 포함한 모든 값 타입은, 값 타입을 소유한 엔티티에 생명주기를 의존

- 한 엔티티에 같은 임베디드 값이 두개 이상이라면?
```java
@AttributeOverrides({
    @AttributeOverride(name="city", column=@Column("WORK_CITY"),...)
```

### 값 타입과 불변 객체
>값 타입은 복잡한 객체 세상을 조금이라도 단순화 하려고 만든 개념이다. 띠라서 값 타입은 단순하고 안전하게 다룰 수 있어야 한다.

- 값 타입 공유 참조
    - 임베디드 타입 같은 값 타입을 여러 엔티티에서 공유하면 위험 (임베디드 타입을 바꾸면 그것과 연관된 엔티티에 영향이 감)
    - 같이 값을 공유하고 싶을 때는 그 값을 엔티티로 두어야함
    - 아니면 복사해서 써야함 (new Address(addr.getCity(),....))
    
- 객체 타입의 한계
    - 참조값을 직접 대입하는 것을 막을 방법이 없기 때문에 객체의 공유참조는 피할 수 있는 방법이 없다.
    - 불변객체 : setter가 없는 객체 = 생성 이후에 값 변경 절대 못함(ex. String, Integer)
    
### 값 타입의 비교
> 값타입은 인스턴스가 달라도 그 안에 값이 같으면 같은 것으로 봐야함 (동등성)
```java
int a = 10;
int b = 10;
a == b true

Address a = new Address("서울");
Address b = new Address("서울");
a == b  false
```
### 값 타입 컬렉션
> 값타입 컬렉션: 엔티티가 아닌, 값 타입을 컬렉션에 담아서 쓰는 것, 값타입을 하나 이상 저장할 때 사용
- 모든 필드가 pk (안그러면 엔티티처럼 되니까..)
- `@ElementCollection`, `@CollectionTable(name= "테이블이름", joinColumn(name="외래키 컬럼 이름"))`
- 같은 테이블에 넣을 수 있는 방법이 없음 (테이블 새로 팜)

- 저장
    - 값타입은 엔티티가 persist 하면 알아서 persist 됨
        - 즉 값 타입 컬렉션은 해당 엔티티의 라이프 사이클에 의존하므로 `Cascade all + orphanremovel = true` 와 같은 효과

- 조회
    - 값 타입 컬렉션들은 기본적으로 **지연로딩**
- 수정
    - setter X
    - 통으로 바꿔줘야함
        ```java
        setHomeAddress(new Address("new City", getZipcode(),....));
      ```
    - 컬렉션 수정
    ```java
        remove("치킨");
        add("한식");
    ```
    - 테이블을 다 날리고 다시 처음부터 넣음
        - 값 타입은 엔티티와 다르게 식별자 개념이 없음
        - 값은 변경하면 추적 어렵
        - 값 타입 컬렉션에 변경 사항이 발생하면, 주인 엔티티와 연관된 모든 데이터를 삭제하고, 값 타입 컬렉션에 있는 현재 값을 모두 다시 저장 
            -  따라서 이 떄는 `ManyToOne , OneToMany 양방향` 또는 `OneToManey 단방형` 으로 풀어야함 + `Cascade all + orphanremovel = true` 적용 (실무)
            - 값타입 컬렉션은 진짜 단순할 때 사용 (ex. select box -> 치킨 체크, 피자 체크...)
            ```java
                class AddresssEntity {
          @Id @GeneratedValue
          private Long id;
          @Embedded
          private Address address;  
          }
            ```