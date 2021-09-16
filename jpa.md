<details><summary>영속성 컨텍스트는 무엇인가요? ★</summary>

- 엔티티를 저장 및 관리하는 저장소 및 매니저
- 엔티티는 DB로부터 조회된 데이터들을 의미하고, 객체 형태로 영속성 컨텍스트에 저장됨
- 영속성 컨텍스트 장점
    - 내부적으로 1차 캐시를 가지고 있습니다. - 엔티티간의 동일성을 보장합니다.
    - 쓰기 지연 저장소를 가지고 있습니다. - 트랜잭션이 끝난 이후에 DML 쿼리를 DB로 보냄
        - 영속성 내 엔티티의 변경을 감지합니다. (Dirty Checking)
    - 지연로딩을 지원합니다. (프록시를 사용)
- 엔티티의 상태에 대해 설명해주세요.
    - 비영속: 영속성 컨텍스트와 전혀 관계없는 상태
    - 영속: 영속성 컨텍스트에 의해 관리되는 상태
    - 준영속: 영속성 컨텍스트에 저장되었다가 분리된 상태
</details>
<details><summary>OneToMany를 외래키로 안쓰는 이유?</summary>

- OneToMany를 안쓰는 이유
    - 데이터베이스 테이블 매커니즘 상, **Many**인 쪽에 외래키가 존재한다.
    - `@OneToMany`는 **One**쪽에서 외래키를 관리하게 된다.
        - 테이블에선 사실상 **Many**에서 관리하지만, 코드상에선 **One**이 관리하는 것처럼 보인다.
    - 그 덕에 **Many**인 테이블에서 insert 쿼리가 발생하면, insert 쿼리와 함께 update 쿼리(외래키 삽입)도 같이 발생하게 된다. 그 덕에 2번 쿼리가 나가게 된다.
    - 추가적으로 외래키에 not null을 걸어두면 오류가 발생하게 된다.
        - insert 시점에 외래키값이 null이기 때문에
</details>
<details><summary>JPA에서 @Transactional 동작 원리 ★</summary>

- EntityManagerFactory에서 persistence.xml를 기반으로 EntityManager를 만듭니다.
- EntityManager으로부터 EntityTransaction을 생성합니다.
- EntityTransaction.begin 함수를 실행하여 트랜잭션이 실행됩니다.
- 트랜잭션 내에서 영속성에 엔티티 쿼리 버퍼를 쌓아둡니다.
- 트랜잭션이 끝나면 커밋(플러시)를 합니다.
    - 플러시가 일어나면 Dirty Checking을 수행합니다.
    - 수정된 엔티티를 쓰기 지연 SQL 저장소에 등록합니다.
    - 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송합니다.
    - 플러시가 전부 일어난 후에 커밋이 수행합니다.
- 트랜잭션 전파 속성
    1. REQUIRED : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 새로운 트랜잭션을 생성
        1. 쓰레드별로 트랜잭션이 저장되기 때문에 걱정 ㄴㄴ
    2. REQUIRES_NEW : 부모 트랜잭션은 대기하고 새로운 트랜잭션이 생성
    3. SUPPORT : 부모 트랜잭션 내에서 실행하며 부모 트랜잭션이 없을 경우 nontransactionally로 실행
    4. MANDATORY : 부모 트랜잭션 내에서 실행되며 부모 트랜잭션이 없을 경우 예외가 발생
    5. NOT_SUPPORT : nontransactionally로 실행하며 부모 트랜잭션 내에서 실행될 경우 일시 정지
    6. NEVER : nontransactionally로 실행되며 부모 트랜잭션이 존재한다면 예외가 발생
    7. NESTED : 해당 메서드가 부모 트랜잭션에서 진행될 경우 별개로 커밋되거나 롤백될 수 있음. 둘러싼 트랜잭션이 없을 경우 REQUIRED와 동일하게 작동
</details>
<details><summary>Hibernate란 무엇인가요?</summary>

- JPA 구현체중 하나
- Hibernate 내부에서으로 JDBC API가 동작하고 있고, 개발자는 직접 SQL을 작성하지 않을뿐
- HQL(Hibernate Query Language)이라는 강력한 쿼리 언어를 포함
    - HQL는 완전 객체지향적이며 상속, 다형성, 관계등의 객체지향의 강점을 누릴 수 있음
    - 쿼리 결과로 객체를 반환하며 프로그래머에 의해 생성되고, 직접 접근할 수 있음
    - 페이지네이션이나 동적 프로파일링과 같은 향상된 기능을 제공
    - 명시적인 join을 요구하지 않는다.
- 장점
    - 객체지향적으로 데이터를 관리. 비지니스 로직에 집중. 객체지향 개발이 가능
    - 테이블 생성, 변경, 관리가 쉬움
    - 로직을 쿼리보다 객체자체에 집중할 수 있음
    - 빠른 개발 가능
- 단점
    - 러닝커브가 심함(많은 내용이 감싸져있기 때문에 알아야할 것이 많음)
    - 잘 이해하고 사용하지 않으면 데이터 손실이 있을 수 있음
    - 성능상 문제가 있을 수 있음
</details>
<details><summary>OSIV(Open-Session-In View) ★★</summary>

- 영속성 컨텍스트를 트랜잭션 레이어 넘어서 살릴 지 결정한다.
- 기본적으로 스프링에선 트랜잭션 레이어까지 영속성을 유지시킨다.
- true라고 세팅하면 Api라면 클라이언트에게 응답될 때까지, View이면 View가 렌더링될 때까지 영속성 컨텍스트가 살아있게 된다.
- 만약 false로 지정하면 트랜잭션 레이어 내에서만 영속성 컨텍스트를 유지한다.
    - 트랜잭션 외부에서 프록시 객체 Loading을 시도하면 에러가 발생하게 된다.
- 하지만 OSIV를 계속 켜두게 되면 커넥션 유지 시간이 늘어남을 의미
    - 트래픽이 많아지면 OSIV를 사용하면 영속성 컨텍스트를 유지하는 영역이 트랜잭션 이상으로 넓어지게 됨. 이는 커넥션 맺는 시간이 길어지게 됨
    - 성능상 이슈(커넥션 풀 개수 부족)가 발생 → service 단에서 DTO로 내려주는게 좋음
</details>
<details><summary>1+N 문제</summary>

- @OneToMany 등에서 하위 엔티티들을 Lazy Loading으로 가져올때마다 자식 조회 쿼리가 추가로 발생하는 이슈
- 원인
    - JpaRepository에 정의한 인터페이스 메서드를 실행하면 JPA는 메서드 이름은 분석해 JPQL을 생성
    - JPQL은 SQL을 추상화한 객체지향 쿼리 언어로 특정 SQL에 종속되지 않고 엔티티 객체와 필드이름을 가지고 쿼리합니다.
- 해결방법
    - 페치조인을 사용하여, 연관관계를 가진 객체를 전부 조회해온다.
    - 1번의 쿼리로 연관관계가 있는 객체를 조회할 수 있음(단, XToOne 관계 한정)
    - 컬렉션 페치 조인을 하게 되면 데이터가 중복되어서 반환되게 됩니다.
        - 이 경우에 대해서는 지연로딩을 사용하고 default_batch_fetch_size를 통해 쿼리문을 줄일 수 있음
        - 두개 이상의 컬렉션에 대해서 페치 조인을 할 수 없습니다.
            - MultipleBagFetchException 예외 발생
            - Hibernate에선 default_batch_fetch_size 값을 지정하고 지연 로딩 사용.
                - 지정된 수만큼 in Query에 부모 키를 사용하게 해줍니다.
        - 페이징 API를 사용할 수 없습니다.
            - 일대다 상황에서 페치 조인에 페이징 API를 사용하면 페이징된 엔티티들을 보여주고 나머지는 메모리 상에 남김 → 실제로는 전부 조회하고 메모리 상에 남김.
</details>
<details><summary>HikariCP 옵션에는 무엇이 있나요?</summary>

- database와의 connection을 관리해주는 connection pool
- database와의 connection을 맺는 과정을 복잡할 뿐더러 컴퓨터 리소스를 많이 소모함
- HikariCP는 미리 정해놓은만큼 connection을 pool에 담아 놓고, 요청이 들어오면 Thread가 connection을 요청하고 HikariCP는 Pool 내에 있는 connection을 연결

    ![](/images/hikari.png)

- HikariCP 옵션
    - auto-commit (default : true)
        - **connection**이 종료되거나 **pool**에 반환될 때, **connection**에 속해있는 **transaction**을 **commit** 할지를 결정
    - connection-timeout(default: 30000 - 30 seconds)
        - **pool**에서 커넥션을 얻어오기전까지 기다리는 최대 시간, 허용가능한 **wait time**을 초과하면 `SQLException`을 던짐
        - 설정가능한 가장 작은 시간은 **250ms** (**default: 30000 (30s)**)
    - idle-timeout(default : 600000 - 10 minutes)
        - **pool**에 일을 안하는 커넥션을 유지하는 시간이 옵션은 **minimum-idle**이 **maximum-pool-size**보다 작게 설정되어 있을 때만 설정
        - **pool**에서 유지하는 최소 커넥션 수는 **minimum-idle**(A connection will never be retired as idle before this timeout)
        - 최솟값은 **10000ms** (**default: 600000 (10minutes)**)
    - **max-lifetime(default : 1800000 - 30 minutes)**
        - 커넥션 풀에서 살아있을 수 있는 커넥션의 최대 수명시간
        - 사용중인 커넥션은 **max-lifetime**에 상관없이 제거되지않음 사용중이지 않을 때만 제거됨
        - **pool** 전체가아닌 커넥션 별로 적용이되는데 그 이유는 풀에서 대량으로 커넥션들이 제거되는 것을 방지하기 위함
        - 강력하게 설정해야하는 설정 값으로 데이터베이스나 인프라의 적용된 **connection time limit**보다 작아야함
        - 0으로 설정하면 **infinite lifetime**이 적용됨(**idle-timeout**설정 값에 따라 적용 **idle-timeout**값이 설정되어 있을 경우 0으로 설정해도 무한 **lifetime** 적용 안됨)
    - **minimum-idle (default : maximum-pool-size)**
        - 아무런 일을 하지않아도 적어도 이 옵션에 설정 값 **size**로 커넥션들을 유지해주는 설정
        - 최적의 성능과 응답성을 요구한다면 이 값은 설정하지 않는게 좋음
        - **default**값을 보면 이해할 수 있음
        - **HikariCP**에서는 최고의 **performance**를 위해 **maximum-pool-size**와 **minimum-idle**값을 같은 값으로 지정해서 **connection Pool**의 크기를 **fix**하는 것을 강력하게 권장한다
    - **maximum-pool-size (default: 10)**
        - **pool**에 유지시킬 수 있는 최대 커넥션 수
        - **pool**의 커넥션 수가 옵션 값에 도달하게 되면 **idle**인 상태는 존재하지 않음.
    - pool-name (default : auto-generated)
        - 이 옵션은 사용자가 **pool**의 이름을 지정함
        - **logging**이나 **JMX management console**에 표시되는 이름
    - initialization-fail-timeout
        - **pool**에서 커넥션을 초기화할 때 성공적으로 수행할 수 없을 경우 빠르게 실패하도록 해준다
        - 상세 내용은 한국말보다 원문이 더 직관적이라 생각되어 다음 글을 인용함
    - validation-timeout
        - **valid** 쿼리를 통해 커넥션이 유효한지 검사할 때 사용되는 **timeout**
        - 최소 250ms 이상부터 설정가능(**default: 5000ms**)
    - connection-test-query (default : none)
        - **JDBC4 드라이버**를 지원한다면 이 옵션은 설정하지 않는 것을 추천
        - 이 옵션은 **JDBC4**를 지원안하는 드라이버를 위한 옵션임(`Connection.isValid()` **API**)
        - 커넥션 **pool**에서 커넥션을 획득하기전에 살아있는 커넥션인지 확인하기 위해 **valid** 쿼리를 던지는데 사용되는 쿼리(보통 **SELECT 1** 로 설정)
        - **JDBC4드라이버**를 지원하지않는 환경에서 이 값을 설정하지 않는다면 **error**레벨 로그를 뱉어냄
    - read-only (default : false)
        - **pool**에서 커넥션을 획득할 때 `read-only` 모드로 가져옴
        - 몇몇의 **database**는 `read-only`모드를 지원하지 않음
        - 커넥션이 `read-only`로 설정되어있으면 몇몇의 쿼리들이 최적화 됨
        - 이 설정은 **database**에서 지원하지 않는다면 **readOnly**가 아닌 상태로 **open**되기 때문에, 지원되는 **database** 목록을 확인해보고 사용해야 된다
    - transaction-isolation (default : none)
        - `java.sql.Connection` 에 지정된 **Transaction Isolation**을 지정한다지정된 **Transaction Isoluation**은 다음과 같다
            - `Connection.TRANSACTION_NONE` : **transaction**을 지원하지 않는다
            - `Connection.TRANSACTION_READ_UNCOMMITTED` : **transaction**이 끝나지 않았을 때, 다른 **transaction**에서 값을 읽는 경우 **commit**되지 않은 값(**dirty value**)를 읽는다
            - `Connection.TRANSACTION_READ_COMMITTED` : **transaction**이 끝나지 않았을 때, 다른 **transaction**에서 값을 읽는 경우 변경되지 않은 값을 읽는다
            - `Connection.TRANSACTION_REPEATABLE_READ` : 같은 **transaction**내에서 값을 또다시 읽을 때, 변경되기 전의 값을 읽는다. `TRANSACTION_READ_UNCOMMITTED` 와 같이 사용될 수 없다
            - `Connection.TRANSACTION_SERIALIZABLE` : **dirty read**를 지원하고, **non-repeatable read**를 지원한다
                - 기본값을 각 **Driver vendor**의 **JDBCDriver**에서 지원하는 **Transaction Isoluation**을 따라간다. (**none**으로 설정시)
    - category (default : none)
        - **connection**에서 연결할 **category**를 결정한다
        - 값이 설정되지 않는 경우, **JDBC Driver**에서 설정된 기본 **category**를 지정하게 된다
    - driver-class-name (default : none)
        - **HikariCP**는 **jdbcUrl**을 참조하여 자동으로 **driver**를 설정하려고 시도함
        - 하지만 몇몇의 오래된 **driver**들은 **driver-class-name**을 명시화 해야함
        - 어떤 에러 메시지가 명백하게 표시 되지않는다면 생략해도 됨
    - leak-detection-threshold (default : 0)
        - 커넥션이 누수 로그메시지가 나오기 전에 커넥션을 검사하여 **pool**에서 커넥션을 내보낼 수 있는 시간
        - 0으로 설정하면 **leak detection**을 이용하지않음
        - 최솟값 **2000ms** (**default: 0**)

    Statement Cache

    많은 **connection pool**(**including dbcp**, **vibur**, **c3p0**)라이브러리들은 **PreparedStatement caching**을 지원하지만 **HikariCP**는 지원하지 않는다**connection pool layer**에서 **PreparedStatements**는 각 커넥션 마다 캐싱된다애플리케이션에서 **250**개의 공통적인 쿼리를 캐싱하고 있고 커넥션 **pool size**가 **20**이라면 **database**에 **5000** 쿼리 실행계획을 **hold**하고 있게된다대부분의 **major database**의 **jdbc driver**들은 이미 설정을 통해 **Statement**를 캐싱할 수 있다(**PostgreSQL**, **Oracle**, **Derby**, **MySQL**, **DB2** 등등) 즉 우리가 원하는대로 **250**개의 쿼리 실행계획만 데이터베이스에 캐싱할 수 있음을 의미한다(**connection pool**에 설정하는 것이 아닌 **driver**에 설정함으로써)

    - jdbc4ConnectionTest (default : true) -- Deprecated 됨
        - **connection**을 맺은다음, `Connection.isValid()` **method**를 호출해서 connection이 정상적인지를 확인한다
        - 이 **property**는 다음에 나올 **connectionTestQuery**에 매우 밀접한 영향을 받는다
    - dataSource (default : none)
        - 사용자가 만든 **dataSource**를 **Pool**에 의하여 **wrapped**하는 것을 원하는 경우, 이 값을 지정하여 사용한다
        - **HikariCP**는 이 문자열을 이용해서 **reflection**을 통해 **dataSource**를 생성한다이 값이 설정되는 경우, **dataSourceClassName**, **driverClassName** 과 같은 값들은 모두 무시 된다
    - driver-class-name
        - **HikariCP**에서 사용할 **DriverClass**를 지정한다이 값이 지정되는 경우, **jdbcUrl**이 반드시 설정되어야 한다
    - jdbcUrl
        - **jdbcUrl**을 지정한다**driverClassName**이 지정된 경우, **jdbcUrl**을 반드시 지정해야 한다
    - registerMbeans (default : false)
        - **JMX management Beans**에 등록되는 될지 여부를 지정한다
    - username
        - **Connection**을 얻어내기 위해서 사용되는 인증 이름을 넣는다
    - password
        - **username**과 쌍이 되는 비밀번호를 지정한다
</details>
<details><summary>JPA 격리수준</summary>

- 트랜잭션에서 **일관성이 없는 데이터를 허용하도록 하는 수준**을 말한다.
- JPA(Hibernate)에서는 4가지 잠금(Lock)을 지원한다.
    - 낙관적 잠금: 데이터 갱신 시 경합이 발생하지 않을 거라고 낙관적으로 보고 잠금을 겁니다.
        - ex. 회원정보(해당회원 만이 수정할 수 있기 때문에)
        - 동시 수정이 일어나면 예외를 발생시킵니다.(충돌 감지)
    - 비관적 잠금: 데이터 갱신 시 경합이 발생할거라고 비관적으로 보고 잠금을 겁니다.
        - ex. 상품의 재고(상품이 여러군데에서 수정이 이루어지기 때문에)
        - 예외를 발생시키지 않고 정합성을 보장해줍니다.
        - 단, 성능적인 측면은 손실을 감수할 수 있습니다. DB에선 배타잠금을 사용
    - 암시적 잠금
        - 프로그램 코드상에서 명시적으로 지정하지 않아도 잠금이 발생하는 것을 의미
        - 엔티티에 `@Version` 혹은 `@OptimisticLocking` 어노테이션이 설정되었을 때 자동적으로 충돌감지를 위한 잠금이 시작됨
    - 명시적 잠금
        - 의도적으로 잠금을 실행합니다.
        - JPA에서 엔티티를 조회할 때, LockMode를 지정하거나 `select for update` 쿼리를 통해 직접 잠금을 지정할 수 있음

    [https://reiphiel.tistory.com/entry/understanding-jpa-lock](https://reiphiel.tistory.com/entry/understanding-jpa-lock)

</details>
<details><summary>Eager Loading vs Lazy Loading</summary>
</details>
<details><summary>JPA Indexing</summary>
</details>

