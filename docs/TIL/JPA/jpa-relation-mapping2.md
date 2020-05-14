## 고급매핑

### 상속관계 매핑
- 객체는 상속관계 O, 데이터베이스는 상속관계 X
- 디비에는 슈퍼타입, 서브타입이 객체의 상속과 유사함
- 상속관계 매핑 : 객체의 상속 구조와 DB의 슈퍼타입 서브타입 관계를 매핑
- 슈퍼타입 서브타입? => 논리적인 모델

***슈퍼타입 서브타입을 물리적 모델로 만드는 방법***

1. 조인 전략
![](/img/joined.png)
    - `DTYPE` : 자식 구분을 위한 구분 컬럼
    - 데이터가 정규화가 되어있다 (저장공간 효율성)
    - 제약조건을 다 통일 가능(Item에 적용하면 가능)
    - 정산시에는 Item 만 보면 됨 
    - 데이터를 가져올때 item과 join해서 가져온다.
    - 데이터의 중복이 존재하지 않는다.
    - 쿼리를 많이 날린다. -> 큰 단점은 아님
        - insert 시 item에 쿼리 1번 자식 디비에 쿼리 1번
        
2. 단일 테이블 전략
![](/img/single_table.png)
    - 모든 데이터를 하나의 엔티티에 두는 방법
    - 쿼리날리기 편함(성능 GOOD)
    - 테이블에 데이터가 많이 들어있으면 조회성능이 오히려 느려질 수 있음
    - 객체지향스럽지 않음, 
    - null이 무조건 존재함
    
3. 구현 클래스마다 테이블 전략 (지양하는 방법)
![](/img/table_per_class.png)
    - 각각 테아블에 데이터를 가지고 있는 방식
    - 쿼리날리기 편함(성능 GOOD)
    - 속성이 중복
    - 테이블 구조 생각하지 않고 (디비에는 Item 테이블이 없다는것을 인지하지 못하고) movie 데이터를 Item 타입으로 가져오려고 할 때 union 쿼리를 날림 
        - 다 뒤져봐야하는 복잡한 쿼리가 날라감 
    - 자식 엔티티 통합해서해야하는 기능 구현이 힘듬

### 주요 어노테이션
- `@Inheritance(strategy=InheritanceType.XXX)` 
    - JOINED : 조인 전략 
    - SINGLE_TABLE : 단일 테이블 전략
    - TABLE_PER_CLASS : 구현 클래스마다 테이블 전략
- 사용법
```java
@Entity
@Inheritance(strategy = InheritanceType.JOINED or SINGLE_TABLE or TABLE_PER_CLASS)
@DiscriminatorColumn
public abstract class Item {
...
}
@Entity
@DiscriminatorValue(value = "A")
public class Album {
...
}
```
  - `@Inheritance` : 상속구조일때 테이블 전략을 설정하는 어노테이션
  - `@DiscriminatorColumn` : DTYPE에 자식 분류를 넣는 어노테이션
  - DTYPE을 넣는 이유? (TABLE_PER_CLASS 제외)
      - Item 테이블에 들어온 데이터가 어떤 자식데이터인지 알기 위해! 
          - SINGLE_TABLE 전략에는 꼭 필요 => 이거 없으면 단일테이블에 데이터가 어디서 온 데이턴지 모름
  - `@DiscriminatorValue` : 기본으로 DTYPE에 테이블 이름이 들어가는데, 이를 바꾸려면 사용하는 어노테이션
    
### @MappedSuperclass
- **공통 속성 정보**가 필요할 때 사용됨(id, name, 등록날짜, 수정날짜,,,) => BaseEntity
- **엔티티가 아님!!**
- 부모 클래스를 상속 받는 자식 클래스에 매핑정보만 제공
- 조회, 검색 불가
- 직접 생성해서 사용할 일이 없으므로 추상 클래스를 권장함


