## 엔티티 매핑

### 객체와 테이블 매핑
- `@Entity`
    - JPA가 관리하는 엔티티
    - 기본 생성자 필수(public, protected)
    - final 클래스, enum, interface, inner 클래스 X
    - 필드 final X

- `@Table`
    - 엔티티와 테이블의 이름이 다를 경우 사용
    - 속성
        - name, catalog, schema, uniqueConstraints

### 필드와 컬럼 매핑
- `@Column`
    -  insertable, updateable : 등록, 변경 가능 여부
    - columnDefinition : 데이터비에스 컬럼 정보를 직접 할당 가능 -> ex) varchar(100) default 'EMPTY'
    - precision,scale : 아주 큰 숫자, 소수점 쓸 때 쓰임
- `@Enumerated` : Enum 타입
    -   ORDINAL 사용하지 말고 STRING 써야 하는 이유 : 0이 USER고 1이 ADMIN일 때 이넘의 순서를 바뀌고 새로운 ADMIN을 insert하면 0
- `@Temporal` : 시간 필드의 타입을 정의 (DATE,TIME,TIMESTAMP) (java8에서 LocalDate 들어왔기 때문에 쓸 일이...)
- `@Lob` : varchar 의 최대 크기이상의 값을 넣을 때 사용 (cLob) , 나머지 타입 플드는 bLob
- `@Transient` :  디비의 컬럼과 매핑을 하지 않는 필드

### 기본 키 매핑
- `@Id`
- 자동생성(`@GeneratedValue`)
    - **IDENTITY** : 기본 키 셍상을 데이터베이스에 위임(Mysql의 auto_increment)
        - JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL 실행
        - auto_increment 이후에 id 값 알 수 O
        - IDENTITY 전략은 em.persist() 시점에 즉시 INSERT 하고 DB에서 식별자 조회전략
        ***영속성 컨텍스트에 Key 값이 Id 인데 지금 id Null***
        - 이 생성전략일 때만 특별하게 em.persist()를 하면 commit이 아직 되지 않았지만 insert 문을 실행장(쓰기지연이 안됨!)
            - ~~~select로 id를 가져온다~~~ => JDBC Driver 기능 중 insert 를 하면 id 를 반환해주는 기능을 제공

***Before***

![](/img/before-persist.png)<br>
- persist 전에는 id를 설정하지 않았으니 null

***After***

![](/img/after-persist.png)
- persist 후 영속성 컨텍스트는 commit 호출 전에 insert 쿼리를 날리고 반환되는 id값을 영속성 컨텍스트의 1차캐시 key와 엔티티 id값에 저장한다.
    - **SEQUENCE**
        - Sequence Object 로 Id 생성을 관리
        - 테이블 마다 Sequence를 다르게 하고 싶을 경우 : @SequenceGenerator
         ***영속성 컨텍스트에 Key 값이 Id 인데 지금 id Null***
       ```text
         이 생성전략일 때만 특별하게 em.persist()를 하면 Sequence Object에 call next value해서 그 값을 Key값으로 저장
       ```
        - allocationSize : 증가하는 단위, call next value 를 여러번하지 않기 위해 나타남(성능 최적화, 동시성 해결)
    - TABLE : 키 생성 전용 테이블로 데이터베이스 시퀀스를 흉내 (모든 DB 가능)
        - 성능 이슈가 있음(최적화가 안되어있음)
    - **AUTO** : 방언에 따라 기본키 생성전략을 결정 (Oracle: sequence, Mysql: Identity)
- 권장하는 식별자 전략
    - Long + 대체키(UUID) + 키 생성전략 사용

### 데이터베이스 스키마 자동 생성
- 장점
 - 테이블을 생각하지 않고 객체를 기반으로 테이블을 생성해줌
 - 데이터베이스 방언을 활용해서 데이터베이스에 맞는 적절한 DDL 생성 : varchar2(Oracle), varchar(Mysql)...
- 속성
    -   hibernate.hbm2ddl.auto
        - `create` :  테이블 다날리고 다시 생성
        - `create-drop` : 테이블을 서버 끌 때 drop
        - `update` : 변경된 부분만 반영
        - `validate` : 엔티티와 테이블이 정상 매핑되어있는지만 확인
        - `none` : 안쓰겠다

- **운영 장비에는 절대 create, create-drop, update X**
- 운영서버에서는 validate 또는 none (none을 권장) -> alter를 할때는 테스트에 한번해보고 적용해봐야 하기 때문에 X