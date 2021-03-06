<details><summary>데이터베이스 특징</summary>

- 독립성
- 무결성
- 보안성
- 일관성
- 중복 최소화
</details>
<details><summary>데이터베이스 성능</summary>

- 성능 이슈는 **디스크 I/O를 어떻게 줄이느냐**에서 시작함
    - 디스크 드라이브의 플래터를 돌려서 읽어야 할 데이터가 저장된 위치로 디스크 헤더를 이동시킨 다음 데이터를 읽게 된다.
- 데이터를 읽는데 걸리는 시간은 **디스크 헤더를 움직여서 읽고 쓸 위치로 옮기는 단계**에서 결정됨
- 결국에 디스크 헤더의 이동없이 많은 데이터를 한 번에 기록하는 것이 성능을 향샹시킴
    - 그래서 순차 I/O가 렌덤 I/O보다 성능이 좋음 → 하지만 현실은 랜덤 I/O
    - 랜덤 I/O를 순차 I/O로 바꿔서 실행할 수 없을까? → 데이터베이스 쿼리 튜닝 → 랜덤 I/O 자체를 줄여주는게 목적
</details>
<details><summary>인덱스 ★</summary>

- 데이터베이스 테이블의 검색 속도를 향상시키기 위한 자료구조
- 레코드 내 칼럼의 값과 레코드가 저장된 주소를 키와 값의 쌍으로 인덱스를 구성한다.
- 인덱스는 항상 정렬된 상태를 유지하기 때문에 원하는 값을 탐색하는데 빠름
    - 하지만 새로운 값(인덱스)을 추가하거나 삭제, 수정하는 경우에는 쿼리문 실행 속도가 느려진다
    - 즉, 저장 성능을 희생하고 읽기 속도를 높이는 기능이다.
- 인덱스 자료구조
    - B+Tree 인덱스 알고리즘

        ![](/images/index0.png)

        InnoDB에서 사용되는 B+Tree 구조

        - DB 인덱스를 위해 자식 노드가 2개 이상인 B-Tree를 개선시킨 자료구조
        - 모든 노드에 데이터를 저장했던 BTree와 다른 특성을 지님
            - 리프토만 인덱스와 함께 데이터를 가지고 있음. 나머지 노드들은 데이터를 위한 인덱스(Key)만을 갖음
            - 리프노드들은 LinkedList로 연결
            - 데이터 노드 크기는 인덱스 노드의 크기와 같지 않아도 됨
            - 단점은 Value를 얻기 위해서 리프노드까지 내려가야함.
        - 탐색은 $O(logN)$만에 수행 가능
    - Hash 인덱스 알고리즘
        - 칼럼의 값으로 해시 값을 계산해서 인덱싱하는 알고리즘으로 매우 빠른 검색을 지원
        - 하지만 값을 변형해서 인덱싱 → 값의 일부를 검색하는 쿼리문에서는 인덱스를 사용할 수 없음
        - 메모리 기반 데이터베이스에서 많이 사용
- Primary Index vs Secondary Index
    - 클러스터란 여러 개를 하나로 묵는다는 의미
    - 클러스터드 인덱스는 프라이머리 키 값이 비슷한 레코드기리 묶어서 저장하는 것
        - 주로 비슷한 값들을 동시에 조회하는 경우가 많다는 점에서 착안
        - 물리적으로 인접한 장소에 저장되어 있는 데이터들
        - 때문에 프라이머리 키를 신중하게 결정하고 클러스터드 인덱스를 사용해야함
- 인덱스의 성능과 고려해야할 사항
    - 모든 칼럼에 대해서 Index를 생성해두면 빨라지지 않을까?
    - Index를 생성하게 되면 Insert, Delete, Update 쿼리문을 실행할 때도 별도의 과정이 추가된다
        - insert의 경우 인덱스에 대한 데이터도 추가해야함
        - delete의 경우 해당 인덱스에 존재하는 값을 삭제하지 않고 사용 안한다는 표시를 남김 → 즉 인덱스의 row수는 그대로
        - 결국 실제 데이터는 10만건인데 인덱스에 의해 100만건이 있는 결과를 초래할 수 있음
        - 심지어, update의 경우 기존 인덱스를 그대로 냅두고 새 인덱스가 추가된다.
    - 데이터 형식이 인덱스 성능에 악영향을 미칠 수 있음
        - 이름, 나이, 성별 세가지 필드를 갖고 있는 테이블이 있다고 가정
        - 이름 → 온갖 경우의 수, 나이 → Int 타입, 성별 → 남/녀로 구성
        - 이름에 대한 걸로 인덱스를 만들면 효율적인 성능 → 나이랑 성별은?
        - 성별로 인덱스를 구성할 시, 1만건 데이터 중 5천건이 성별이 남자일 때, 인덱스를 통해 얻는 데이터가 5천건일 것이고, 5천건을 가지고 다시 탐색을 해야한다.
        - 디스크 I/O가 또 발생하게 됨 → 비효율적이다.
        - 유니크하거나 범위가 넓고 NULL 값이 많이 없는 것을 칼럼으로 지정
</details>
<details><summary>정규화</summary>

- [https://mangkyu.tistory.com/110](https://mangkyu.tistory.com/110)
- 중복 저장 시, 갱신 이상 발생 → 이를 해결하기 위해 정규화 과정 거침
- X의 값이 Y값을 유일하게 결정하면, "X는 Y를 함수적으로 결정"
- 갱신 이상 종류
    - 삽입 이상: 원하지 않는 자료가 삽입된다든지, 삽입하는데 자료가 부족해 삽입이 되지 않는 문제
    - 삭제 이상: 하나의 자료만 삭제하고 싶지만, 그 자료가 포함된 튜플 전체가 삭제됨으로 원하지 않는 정보 손실이 발생하는 문제
    - 수정 이상: 정확하지 않거나 일부의 튜플만 갱신되어 정보가 모호해지거나 일관성이 없어져 정확한 정보 파악이 되지 않는 문제
- 정규화: RDB에서 중복을 최소화하기 위해 데이터를 논리적으로 구조화하는 작업
    - 1NF: 레코드네 컬럼은 원자값을 가진다
    - 2NF: 완전 함수적 종속. 특정 컬럼에 종속적인 컬럼이 존재해서는 안됨
    - 3NF: 이행적으로 종속되지 않는다.
    - BCNF
- 단점: JOIN 연산이 많아짐, 자주 JOIN 연산이 일어나는 경우 운영의 단순화를 위해 반정규화 작업을 실행
- 반정규화: 정규화된 엔티티, 속성, 관계를 시스템의 성능 향상 및 개발과 운영의 단순화를 위해 중복 통합, 분리 등을 수행하는 데이터 모델링 기법.
    - 디스크 I/O량이 많아서 조회 시 성능이 저하되거나, 테이블끼리의 경로가 너무 멀어 조인으로 인한 성능 저하가 예상되거나, 컬럼을 계산하여 조회할 때 성능이 저하될 것이 예장되는 경우 반정규화를 실행.
    - 조회 처리 시, 성능이 중요하다고 판단될 때 부분적으로 반정규화를 고려
    - 무엇이 반정규화 대상??
        - 자주 사용되는 테이블에 엑세스하는 프로세스의 수가 많고, 일정한 범위만을 조회하는 경우
        - 테이블에 대량 데이터가 있고 대량의 범위를 자주 처리하는 경우, 성능상 이슈가 있을 경우
        - 테이블에 지나치게 조인을 많이 사용하게 되어 데이터를 조회하는 것이 기술적으로 어려울 경우.
    - 반정규화를 과도하게 적용하다 보면 데이터의 무결성이 깨질 수 있음
</details>
<details><summary>트랜잭션</summary>

- 작업의 완전성을 보장해주는 것이다.
- 논리적인 작업 셋을 모두 완벽하게 처리 or 처리 못할 경우 원상태로 복구
    - **작업의 일부만 적용되는 현상이 발생하지 않게 만들어주는 기능**
- 트랜잭션 vs Lock
    - Lock은 동시성을 제어하기 위한 기능
        - 여러 커넥션이 동일한 레코드를 요청했을 때 순서대로 한 시점에는 하나의 커넥션만 변경할 수 있게 해주는 역할
    - 트랜잭션은 데이터의 정합성을 보장하기 위한 기능
- 트랜잭션의 특성
    - 원자성: 트랜잭션 중간에 어떠한 문제가 발생한다면 트랜잭션에 해당하는 어떠한 작업 내용도 수행되어서는 안되며 아무런 문제가 발생되지 않았을 때만 모든 작업이 수행되어야한다
    - 일관성: 트랜잭션이 완료된 다음의 상태에서도 트랜잭션이 일어나기 전의 상황과 동일하게 데이터의 일관성을 보장해야한다.
    - 고립성: 각각의 트랜잭션은 서로 간섭없이 독립적으로 수행되어야 한다.
    - 지속성: 트랜잭션이 정상적으로 종료된 다음에는 영구적으로 데이터베이스에 작업의 결과가 저장되어야한다.
- 트랜잭션의 상태

    ![](/images/tx0.png)

    - Active: 트랜잭션의 활동 상태
    - Failed: 트랜잭션 실패 상태
    - Partially Committed: 트랜잭션의 Commit 명령이 도착한 상태, SQL문이 수행되고 Commit만 남은 상태
    - Committed: 트랜잭션 완료 상태
    - Aborted: 트랜잭션이 취소된 상태
    - Committed과 Partially Committed의 차이
        - Commit 요청이 들어오면 Partially Committed 상태가 되고, SQL문이 정상적으로 실행되기 전까지 Partially Committed 상태를 유지한다. SQL문이 실패하게 되면 Failed 상태가 된다.
- 트랜잭션 주의할 점
    - 필요한 최소의 코드에만 적용하는 것이 좋다. 즉 트랜잭션의 범위를 최소화하라는 의미이다.
    - 일반적으로 데이터베이스 커넥션의 개수가 제한적이다.
        - 그런데 단위 프로그램이 커넥션을 소유하는 시간이 길어진다면 사용 가능한 여유 커넥션의 개수도 줄어들게 된다.
        - 그러다 보면 단위 프로그램에서 커넥션을 가져가기 위해 기다려야하는 상황(교착상태)이 발생할 수 있음
- 교착상태
    - 두 개 이상의 트랜잭션이 특정 자원(테이블 또는 행)의 잠금을 획득한 채 다른 트랜잭션이 소유하고 있는 잠금은 요구하는 상황
    - 두 트랜잭션끼리 서로 잠금을 요구하는 상황이 발생하므로 이걸 교착상태라 부른다

        ![](/images/tx1.png)

    ```sql
    Transaction 1> create table B (i1 int not null primary key) engine = innodb;
    Transaction 2> create table A (i1 int not null primary key) engine = innodb;

    Transaction 1> start transaction; insert into B values(1);
    Transaction 2> start transaction; insert into A values(1);

    Transaction 1> insert into A values(1);
    Transaction 2> insert into B values(1);
    ERROR 1213 (40001): Deadlock found when trying to get lock; try restarting transaction
    ```

    - 교착 상태의 빈도를 낮추는 방법
        - 자주 커밋하자
        - 정해진 순서로 테이블에 접근하자
        - 읽기 잠금 획득(Select ~ For Update)의 사용을 피한다.
        - 테이블 단위 잠금을 획득해 갱신을 직렬화 → 동시성을 떨어지지만 교착상태는 피할 수 있음
</details>
<details><summary>NoSQL</summary>

- SQL이 아닌 다른 방법으로 데이터를 저장하는 것을 의미
    - 관계형 데이터베이스적이지 않는 모든 데이터베이스의 데이터 모델과 제품을 아우른다.
- 기존에 정형화된 데이터뿐만 아니라 메신저 텍스트, 음성 등 반정형화, 비정형화된 데이터도 저장하고 다루어야하는 수요가 생김. → 데이터 처리하는데 필요한 비용이 증가
    - 한 대에서 실행되도록 설계된 관계형 데이터베이스를 사용하는 것은 하드웨어적으로 큰 비용이 듦
    - 결국 Scale-up 비용이 기하급수적으로 증가하게됨.
- NoSQL은 데이터의 일관성을 트레이드 오프하여 데이터를 분산하여 저장하는 것에 초점을 맞추어서 등장하게 됨.
    - 값싼 장비를 여러대로 대량의 데이터와 컴퓨팅 부하를 처리하는 것이 가능하게 되었다.
- 특징
    - 일관성과 확장성 사이의 Trade-off
        - 일관성이 데이터베이스의 절대적인 요소가 아니고, 비일관성이 두 가지 이유로 감수할만함
            - 다수가 동시에 읽고 쓰는 상황에서의 성능 향상을 위해서
            - 분산 환경에서 노드들이 잘 작동하고 있을에도, 시스템의 일부가 고장나면 데이터베이스를 사용할 수 없게 되는 문제를 해결하기 위해서
    - 분산 저장
        - 분산 저장을 지원하는 NoSQL의 경우 집합-지향 모델을 사용하여 이러한 문제를 해결한다.
        - 연관된 데이터들이 함께 분산되므로 관계형 모델에서처럼 복잡한 제어가 필요하지 않다.
    - 데이터 일치
        - RDBMS에선 튜플 안의 값은 단순해야 해서 중첩된 레코드나 리스트 등의 다른 구조를 포함할 수 없음
        - NoSQL에서는 하나의 집합으로 취급해서 저장하기 때문에 튜플안에 중첩된 레코드나 리스트를 저장할 수 있다.
    - Schema-less
        - 스키마 없이 동작하기 때문에 데이터 구조를 미리 정의할 필요가 없음
        - 시간이 짖나더라도 언제든지 바꿀 수 있기 때문에 비형식적인 데이터를 저장하는데 용이
            - 하지만 데이터 타입에 땨른 암묵적인 스키마는 여전히 존재
            - 단알값에 대한 데이터 타입에서 불일치가 발생할 수 있음.
- 의의
    - 구매 내역이나 게임의 로그같은 데이터들은 매 초마다 엄창남 양이 생성되지만 한번 저장되고 난 뒤에는 수정될 일이 거의 없다.
    - 이런 데이터들을 저장하는데 데이터의 일관성을 보장하기 위해 ACID 트랜잭션을 지원할 필요가 없음
    - 거기가 생성되는 데이터의 양도 많기 때문에 장비 성능에도 영향을 미침
    - NoSQL은 이러한 데이터들을 효율적으로 저장할 수 잇다.
    - 각종 검색 엔지에도 사용되는 것이 NoSQL.
        - 웹 페이지 내의 텍스트들을 형태소 단위의 토큰으로 분리하여 토큰과 해당 토큰이 포함된 페이지들의 URL을 맵핑하는 Inverted Index(역 인덱스) 구조를 NoSQL을 통해 구현한다
</details>