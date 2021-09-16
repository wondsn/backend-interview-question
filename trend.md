<details><summary>Docker</summary>

- 컨테이너 기반의 가상화 도구.
- 이전의 가상머신은 하드웨어를 소프트웨어로 가상화하는 방식을 사용 → 리소스를 많이 잡아먹음
- 도커는 커널을 공유하면서 프로세스 격리에 기반한 가상화 방법 → 커널을 공유하기 때문에 도커에서 프로세스에 올라가는 리소스만 관리. 커널을 호스트 커널을 사용
- 도커 레이어 계층을 설명해주세요
    - 도커 이미지는 컨테이너를 실행하기 위한 모든 정보를 가지고 있기 때문에 용량이 매우 큼
    - 이런 문제를 해결하기 위해 이미지를 레이어라는 개념으로 나누어 저장
    - 레이어는 읽기 전용으로 수정이 일어나지 않습니다.
- 도커를 사용하는 이유는 무엇이죠?
    - 도커 파일로 이미지를 만들어 두면, 서버가 구성되는 시점이 이미지를 만든 시점으로 고정됨
    - 이미지를 실행시키는 시점이 언제나 같아짐
        - 항상 같은 상태의 서버를 만들 수 있음
- 이미지와 컨테이너의 차이점
    - 이미지는 컨테이너 실행에 필요한 파일과 설정 값등을 포함하고 있는 것. 상태 값을 가지지 않고 변하지 않음
    - 컨테이너는 애플리케이션을 환경에 구애받지 않고 실행하는 기술
- Docker Compose 파일은 무엇인가요?
    - Docker Container 오케스트레이션 툴
    - 컨테이너 사양을 yaml 파일에 명시적으로 선언하여 여러 개의 컨테이너의 실행을 한번에 관리할 수 있게 해줍니다.
</details>
<details><summary>k8s 서비스</summary>

- [https://blog.leocat.kr/notes/2019/08/22/translation-kubernetes-nodeport-vs-loadbalancer-vs-ingress](https://blog.leocat.kr/notes/2019/08/22/translation-kubernetes-nodeport-vs-loadbalancer-vs-ingress)
</details>
<details><summary>k8s 환경에서 같은 서버 이미지인데 환경별로 다르게 해야할 때 어떤걸 써야하나요? ★</summary>

- 컨피그맵을 이용하여 운영환경별로 환경변수를 다르게 세팅하면 됩니다.
</details>
<details><summary>k8s 배포방식에는 어떤 것이 있을까요? 아는대로 설명해주세요 ★</summary>

- Rolling Update, Blue-Green, Canary Update 등이 있습니다.
- Rolling Update
- Blue-Green Update
- Canary Update
</details>

<details><summary>DDD(Domain-Driven Design)이 무엇인가요??</summary>
</details>

<details><summary>MSA 아키텍처 설명</summary>

[https://woongsin94.tistory.com/383](https://woongsin94.tistory.com/383)
</details>

<details><summary>RabbitMQ와 Kafka가 어떻게 다른가요?</summary>

- RabbitMQ는 메시지 브로커 방식이고, Kafka는 Pub/Sub 방식입니다.
- 메시지 브로커
    - 응용프로그램, 서비스 및 시스템이 정보를 통신하고 교환할 수 있도록 하는 소프트웨어 모듈
    - 지정된 수신인에게 메시지를 확인, 라우팅, 저장 및 배달
    - 브로커는 다른 응용 프로그램간의 중개자로 작동
    - 소비자의 위치, 활성 여부, 또는 다수의 수신인에게 메시지를 보낼 수 잇음
- Pub/Sub 방식
    - 하나의 토픽에 대해 Producer가 Publish하고, Consumer가 Subscribe하는 방식
    - Producer가 이벤트(메시지)를 만들어서 브로커내 특정 Topic으로 전달한다.
    - Consumer는 Subscribe 하고있는 Topic에 이벤트가 들어오면 이벤트를 가져온다.
</details>
<details><summary>메시지큐의 장점 ★</summary>

- 비동기: Queue에 메시지를 넣기 때문에 나중에 처리할 수 있음(실제로 파일에 적음)
- 비동조: Application과 분리할 수 있음
- 탄력성: 일부가 실패해도 전체에 영향을 받지 않습니다.
- 과잉: 실패할 경우 재실행이 가능
- 보증: 작업이 처리된 걸 확인할 수 있음
- 확장성: 다수의 프로세스들이 큐에 메시지를 보낼 수 있습니다.
</details>
<details><summary>Kafka vs RabbitMQ ★★</summary>

- Kafka

    복잡한 라우팅에 의존하지 않고, 최대 처리량(10k/sec)으로 스트리밍하는데 적합. 이벤트 소싱, 스트림 처리 및 일련의 이벤트로 시스템에 대한 모델링 변경을 수행하는데 이상적. 메시지를 영구적으로 저장하기 때문에 이상적임

- RabbitMQ

    복잡한 라우팅을 필요로 할 때 적합. 그리고 신속한 요쳥-응답이 필요한 웹 서버에 적합. 부하가 높은 작업자(20k/sec) 간에 부하를 공유합니다. RabbitMQ는 백그라운드 작업이나 PDF 변환, 파일 검색 또는 이미지 확장과 같이 장기 실행 작업도 처리할 수 있습니다.

    장시간 실행되는 테스크, 안정적인 백그라운드 작업 실행, 애플리케이션 간 내부 통신/통합이 필요할 때 적합

</details>
<details><summary>카프카가 뭔가요? 구조를 자세히 설명해주세요. ★</summary>

- 대용량의 실시간 로그처리에 특화된 아키텍쳐 설계를 통해 기존 메시징 시스템보다 우수한 TPS를 보여줌
- Message Queuing Model과 Pub/Sub Model의 특징을 겸비
    - Message Queuing Model: 컨슈머가 분산 처리로 메시지를 소비하는 모델
    - Pub/Sub Model: 여러 Subscriber들에게 동일한 메시지를 전달하고 토픽 기반으로 전달 내용을 변경하는 모델은 컨슈머 그룹으로 묶습니다.
- publish-subscribe 모델을 기반으로 동작. 크게 producer(메시지 생산자) - broker(메시지 수집/전달) - consumer(메시지 소비자)로 구성
![alt ><>](/images/kafka_img0.jpg)

- Broker: 데이터를 수신, 전달하는 서비스
- Message: 카프카에서 다루는 데이터의 최소단위. 메시지는 Key와 Value를 갖게되며 나중에 언급할 메시지 전송할 때 파티셔닝에 이용
- Producer: 데이터의 생산자이며, Broker에게 메시지를 보내는 애플리케이션
- Consumer: Broker에서 Message를 취득하는 애플리케이션
- Topic
    - 메시지를 종류별로 관리하는 스토리지
    - 브로커에 배치되어 관리됨
    - 프로듀서와 컨슈머는 특정 토픽을 지정하여 메시지를 송수신함으로써 단일 카프카 클러스터에서 여러 종류의 메시지를 중계함
![alt ><](/images/kafka_img1.jpg)

- Broker

    브로커는 하나의 서버당 하나의 데몬 프로세스로 동작하여 메시지 수신/전달 요청을 받아들입니다. 이것을 여러 대의 클러스터로 구성할 수 있으며 브로커를 추가함으로써 수신/전달의 처리량 향상, 스케일 아웃이 가능합니다. 브로커에서 받은 데이터는 모두 디스크로 쓰여짐(영속화)으로써 디스크의 총 용량에 따라 장기간 데이터를 보존할 수 있습니다.

- Partition

    토픽에 대한 대량의 메시지 입출력을 지원하기 위해, 브로커 상에 데이터를 읽고 쓰는 것을 파티션이라는 단위로 분할합니다. 토픽을 구성하는 파티션은 브로커 클러스터 안에 분산 배치되며 프로듀서로부터 메시지 수신, 컨슈머에게 배달을 분산 실시함으로써 하나의 토픽에 대한 대규모 데이터 수신과 전달을 지원하게 됩니다.

- Producer

    프로듀서는 프로듀서 API를 이용하여 브로커에 데이터를 송신하기 위해 구현된 애플리케이션입니다. 각종 로그 전송 및 미들웨어와 연동하여 동작하기 때문에 프로듀서 API를 내포한 도구, 미들웨어를 통해 이용하는 형태 등으로 다양합니다.

    - 프로듀서가 토픽의 파티션에 메시지를 송신할 때 버퍼 기능처럼 프로듀서의 메모리를 이용하여 일정량을 축적 후에 송신
    - 데이터의 송신에 대해서는 지정한 크기까지 메시지가 축적되거나(batch.size) 지정한 대기시간에 도달하는 것(linger.ms) 중 하나를 트리거로 전송
    - 토픽에 메시지 전송 시 파티셔닝
        - Key의 해시값을 사용한 송신
            - Key를 이용해 송신처 파티션을 결정
        - 라운드 로빈에 의한 송신
            - Key를 null로 세팅한 경우 여러 파티션으로 메시지 송신을 라운드 로빈 방식으로 실행
    - callback을 통해 브로커로 메시지 송신 결과를 비동기 처리할 수 있음
        - future.get()을 하면 브로커 상태와 설정에 따라 쓰레드가 무한 점유될 수 있으므로 매우 위험
        - CompletableFuture를 이용
- Consumer

    컨슈머 API를 이용해 브로커에서 메시지를 취득하도록 구현된 애플리케이션입니다. 브로커는 메시지를 디스크에 영속화하기 위해 브로커에 **도달하는 즉시 컨슈머에서 취득해야하는 제약이 없어** 디스크에 보관된 동안은 메시지 취득이 가능합니다. 일정 기간 데이터를 축적한 스토리지에서 데이터 추출 및 시간 처리를 위한 애플리케이션의 데이터 입력 등으로 이용됩니다.

- Offset
![alt ><](/images/kafka_img2.jpg)

각 파티션에 수산한 메시지에 일련번호를 부여합니다. 파티션 단위로 메시지 위치를 나타내는 오프셋이라는 관리 정보를 이용해 컨슈머가 취득하는 메시지의 범위 및 재시도를 제어합니다.

- [https://baek.dev/post/20/](https://baek.dev/post/20/)
</details>
<details><summary>RabbitMQ에 대해 설명해주세요. ★</summary>

Producer - Broker - Consumer로 구성되어 있습니다.

![alt ><](/images/rabbitmq_img0.png)

RabbitMQ 메시지 브로커는 Publisher로부터 메시지를 받아서 이를 Consumer에게 라우팅해준다. Publisher가 발행한 메시지는 먼저 Exchange에 도착하게 되고 이 Exchange는 Binding(Routing Rule)이라고 불리는 규칙을 이용해 특정 Queue에 메시지를 복사해 넣는다. 그럼 이 Queue를 수신하고 있는 Consumer가 메시지를 당겨(fetch/pull)갈 수 있게 된다.

RabbitMQ는 Erlang으로 AMQP를 구현한 메시지 브로커 시스템입니다. AMQP는 클라이언트가 메시지 미들웨어 브로커와 통신할 수 있게 해주는 메시징 프로토콜입니다. 위에서 말한 동작원리가 AMQP 동작 원리라고 볼 수 있습니다.

- 여러가지 Exchange Type 알아보기

        **Direct Exchange**

![alt ><](/images/rabbitmq_img1.png)

Exhange X로 전달된 메시지의 routing key가 orange인 경우 Q1으로 전달되고, block, green인 경우 Q2로 전달된다. 그밖에 다른 메시지는 무시된다.

Default Exchange는 이름이 없는 Direct Exchange한 형태이다. Default Exchange는 전달될 목적지 Queue 이름과 동일한 routing key를 부여합니다.

**Fanout Exchange**

routing key와 관계없이 연결된 모든 Queue에 동일한 메시지를 전달합니다.

**Topic Exchange**

routing key 전체가 일치하거나 일부 패턴과 일치하는 모든 Queue로 메시지가 전달됩니다.

여러 Consumer에서 메시지 형태에 따라 선택적으로 수신해야하는 경우 같이 다양한 pub/sub 패턴 구현에 활용될 수 있습니다.

Topic Exchange에서 사용하는 binding key는 점(.)으로 구분된 단어를 조합해서 정의한다.

`*`와 `#`을 이용해 와일드 카드를 표현할 수 있습니다. `*`는 단어 하나 일치 `#`은 0 또는 1개 이상의 단어 일치를 의미합니다.

![alt ><](/images/rabbitmq_img2.png)

다음과 같이 binding key를 정의한 경우 메시지의 `routing key`가 `quick.orange.rabbit` 또는 `lazy.orange.elephant`이면 Q1, Q2 둘다 전달된다. lazy.pink.rabbit는 binding key 2개와 일치하더라도 1번만 전달된다.

`quick.brown.fox`, `quick.orange.male.rabbit`는 일치하는 binding key가 없기 때문에 무시된다.

- `*.orange.*` ←`quick.orange.rabbit`, `lazy.orange.elephant`
- `*.*.rabbit` ← `quick.orange.rabbit`, `lazy.pink.rabbit`
- `lazy.#` ←`lazy.orange.elephant`, `lazy.pink.rabbit`

**Headers Exchange**

메시지 헤더를 통해 binding key만을 사용하는 것보다 더 다양한 속성을 사용할 수 있다.

Header exchange를 사용하면 binding key는 무시되고, 헤더 값이 바인딩 시 지정된 값과 같은 경우에만 일치하는 것으로 간주한다.

</details>
<details><summary>각 회사 장애 상황 블로그글</summary>

RIDI: [https://ridicorp.com/story/idc-outage/](https://ridicorp.com/story/idc-outage/)

배민 장애대응법: [https://techblog.woowahan.com/4886/](https://techblog.woowahan.com/4886/)

라인 장애 보고 및 후속절차: [https://engineering.linecorp.com/ko/blog/line-failure-reporting-and-follow-up-process-culture/](https://engineering.linecorp.com/ko/blog/line-failure-reporting-and-follow-up-process-culture/)

외부 서비스 장애: [https://mjspring.medium.com/why-시리즈-1-e-010-외부-서비스-장애-611ac6f2c192](https://mjspring.medium.com/why-%EC%8B%9C%EB%A6%AC%EC%A6%88-1-e-010-%EC%99%B8%EB%B6%80-%EC%84%9C%EB%B9%84%EC%8A%A4-%EC%9E%A5%EC%95%A0-611ac6f2c192)
</details>