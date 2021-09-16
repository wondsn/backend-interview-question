<details><summary>REST API가 무엇인가요? ★</summary>

- REST 아키텍처의 제약조건을 준수하는 API를 의미
- 리소스(URI) 상태에 대한 행위(HTTP METHOD)를 표현(Representations)하는 방식
- REST 특징
    - 유니폼 인터페이스
    - 무상태성
    - 캐시 기능
    - 자체 표현 구조
    - Client - Server 구조
    - 계층형 구조
- 디자인 가이드
    - URI는 정보의 자원(리소스)를 표현해야한다.
    - 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE)로 표현한다.
    - 슬래시 구분자는 리소스간의 계층관계를 표현한다.
        - URI 마지막 문자로 슬래시를 포함하지 않는다.

  [https://meetup.toast.com/posts/92](https://meetup.toast.com/posts/92)

- POST와 PUT의 차이점은 무엇인가요?
    - POST는 요청 데이터를 처리하고, 해당 리소스가 없으면 생성.
        - 요청 데이터 처리: 단순히 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야하는 경우 (컨트롤 URI)
    - PUT은 해당 리소스가 없으면 생성하고, **있으면 새로운 걸로 대체**
        - 클라이언트가 리소스 위치를 알고 URI를 지정
        - 기존 데이터가 사라질 우려가 있음
</details>
<details><summary>Keep-alive는 무엇인가요? (지속 연결과 이어짐)</summary>

- HTTP/1.1부터 도입. socket에 IN/OUT의 access가 마지막으로 종료된 시점부터 정의된 시간까지 access가 없더라도 대기하는 구조
    - 정의된 시간내에 access가 이루어진다면 계속 연결된 상태를 유지할 수 있음
    - HTTP는 connection less 방식이라 매번 socket(port)를 열어야 하는 비효율적인 구조
        - keep-alive의 timeout 내에 client가 request를 재요청하면 socket(port)를 새로 여는게 아닌 이미 열려있는 socket(port)에 전송하는 구조
    - HTTP/2에선 무시됨. 연결 관리는 해당 프로토콜 내 다른 메커니즘에 의해 처리됨

> Keep-Alive: *parameters*

- parameter: 쉼표로 구분된 파라미터 목록으로, 각각 등호로 구분되는 키와 값으로 구성됨
    - `Keep-Alive: timeout=5, max=99`
    - timeout: 유휴 연결이 계속 열려 있어야 하는 최소 시간을 가르킴
    - max: 연결이 닫히기 이전에 전송될 수 있는 최대 요청 수를 가리킴.
- [https://goodgid.github.io/HTTP-Keep-Alive/](https://goodgid.github.io/HTTP-Keep-Alive/)
</details>
<details><summary>HTTP 정의 ★</summary>

- 클라이언트와 서버 모델에서 데이터를 주고 받기 위한 프로토콜
- 애플리케이션 레벨의 프로토콜로 TCP/IP 위에서 동작
- Stateless한 프로토콜이다.
- Start Line, Headers, empty line, Body로 구성

    ![](/images/http_struct.png)

    - start-line = request-line / status-line
        - request-line: **method** SP(공백) **request-target** SP **HTTP-version** CRLF(엔터)
            - ex. GET(method) /search?q=hello&hl=ko(request-target) HTTP/1.1(HTTP-version)
        - status-line: **HTTP-version** SP **status-code** SP **reason-phrase** CRLF
            - ex. HTTP/1.1(HTTP-version) 200(status-code) OK(reason-phrase)
    - header = field-name ":" OWS field-value OWS (OWS: 띄어쓰기 허용)
        - ex. Content-Type: text/html;charset=UTF-8
    - body: 실제 전송할 데이터. HTML, 문서, 이미지, 영상, JSON 등 byte로 표현할 수 있는 모든 데이터 전송 가능.
- 비연결성이다.(연결을 유지하지 않는다.)
    - 서버 입장에서 서버 자원을 효율적으로 사용할 수 있음.
    - 하지만 연결할 때마다 3-way handshake 시간이 추가됨
    - 지금은 HTTP 지속 연결로 문제 해결
</details>
<details><summary>Stateless한게 뭔가요?</summary>

    - Stateful하면 중간에 요청을 처리하기 위한 컨텍스트를 저장하고 있어야한다.
    - Stateless하면 응답 받는 서버가 바뀌어도 요청을 처리할 수 있다.
    - Stateful을 유지해아하는 경우가 존재(ex. 로그인 세션)
</details>
<details><summary>HTTP 지속 연결</summary>

- HTTP의 비연결성때문에 데이터를 주고받을때마다 연결을 새로 맺어줘야한다.
    - 클라이언트에서 요청해서 응답받을때까지 걸린 시간: RTT, 데이터 다운 시간: TR
- 그걸 방지하기 위해 HTTP 지속 연결을 지원해준다.
    - HTTP 1.1에선 기본적으로 지속 연결을 지원해준다.
    - 요청 헤더에 `connection:keep-alive`을 넣어주면 된다.
- 예시: 서버로부터 3개 파일을 다운받음
    - 클라이언트에서 요청해서 응답받을때까지 걸린 시간: RTT, 데이터 다운 시간: TR
    - 기존: (연결 초기화 + 파일 요청 + 파일 다운로드) * 3
        - 3*(RTT + RTT + TR) = 6RTT + 3TR
    - 지속 연결: 연결 초기화 + 3*(파일 요청 + 파일 다운로드)
        - RTT + 3*(RTT + TR) = 4RTT + 3TR
    - 예제에선 파일 3개만 다운받았지만 실무에선 더 많은 데이터를 요청하게 된다.
- 하지만 지속 연결을 해놓으면 서버 자원이 고갈될 수 있음
- Http/2 버전은 **멀티 플랙싱**기능으로 단일 TCP 연결을 통해 (Persistent Connection in Http1.x) 다수의 Http 요청과 응답(Http pipelining in Http 1.1)이 **클라이언트와 서버 사이에 응답 지연(Hol : Header of line blocking)없이 stream 형태로 주고 받을 수 있는 기술적 토대**를 만들게 되었다.
- 따라서 Http/2를 사용한다면 Persistent Connection에 대해 고민할 필요가 없어지게 된다
</details>
<details><summary>멀티 플렉싱(Multi-Flexing)이 뭔가요?</summary>

- **하나의 통신채널을 통해 둘 이상의 데이터(시그널)를 전송하는 데 사용되는 기술**
- **물리적 장치의 효율을 위해 최소한의 물리적 장치로 최대한의 데이터를 전달하는 기술**
- 입출력 대상을 하나로 묶어서 관리하는 방법. 클라이언트-서버 모델에서 주로 사용되는 기술
    - 클리이언트-서버가 1:1 관계가 맺어지만 서버 자원을 낭비하게 된다.(클라이언트 수만큼 멀티 프로세스를 만들어야 한다.)

        ![](/images/multiplex0.png)

        멀티 프로세스 서버 모델

    - 그래서 클라이언트-서버가 N:1 관계를 맺어서 하나의 서버에서 여러 클라이언트의 요청을 처리한다.

        ![](/images/multiplex1.png)

        멀티 플렉싱 서버 모델

        - 이 때, 서버에선 다수의 클라이언트의 요청을 받기 위해 멀티 플렉싱 기술을 이용한다.
        - 하나의 서버가 여러개의 파일 디스크립터를 감시한다. 이 파일 디스크립터는 클라이언트와 연결되어있음 (리눅스에선 select, poll 함수를 사용한다.)

</details>
<details><summary>HTTP vs HTTPS</summary>

- HTTP는 평문 데이터를 전송하는 프로토콜
    - 비밀번호나 주민번호등을 주고 받으면 제3자에 의해 조회될 수 있음
- HTTPS는 자신의 **공개키**를 갖는 인증서를 발급하여 **보내는 메시지를 공개키로 암호화**함.
    - 공개키로 암호화된 메시지는 **개인키**를 가지고 있어야만 **복호화** 가능
- TLS(Transport Layer Security)를 사용해 암호화된 연결을 지원한다.
- URL schema 부분에 **http**가 아닌 **https**이 붙게됨. 포트도 80이 아닌 443
- TLS(Transport Layer Security): 인터넷에서 정보를 암호화해서 송수신하는 프로토콜
    - TLS는 '보안 계층'이라는 독립적인 프로토콜 계층으로써 응용 계층과 전송 계층 사이에 속하게 됨
    - HTTPS는 TLS위에 HTTP 프로토콜을 얹어 보안된 HTTP 통신을 하는 프로토콜이다.
    - HTTP 뿐만 아니라 FTP, SMTP 프로토콜에도 적용될 수 있다.
  <details><summary>HTTPS 통신 과정</summary>
  
  ![](/images/https.png)
  
  - ClientHello: Server에 연결을 시도하며 전송하는 패킷. 자신이 사용가능한 Cipher Suite 리스트, Session ID, SSL Protocol Version, Random Byte를 전달
  - ServerHello: Client에게 응답하며 보내는 패킷. 서버에서 정한 Cipher Suite를 전달
  - Certificate: Server에서 자신의 SSL 인증서를 Client에게 전달합니다. Server에서 발행한 공개키가 들어있음(비밀키는 서버에서 소유). Client에선 Server가 개인키로 암호화여 보낸 SSL 인증서를 전달받은 공개키로 복호화합니다. 복호화에 성공하면 SSL 인증서는 유효한 인증서가 됩니다.
      - 공개키를 받은 Client는 데이터를 공개키로 암호화하여 데이터를 전송하게 됩니다. 암호화한 데이터는 서버가 가진 비밀키를 통해서만 복호화가 가능하기 때문에 안전한다.
  - Client Key Exchange: 클라이언트는 전달받은 Server의 공개키를 이용하여 대칭키(데이터를 실제로 암호화하는 키)를 암호화하여 Server에게 전달합니다.
      - **이 대칭키가 SSL Handshake의 진짜 목적이다. 클라이언트와 서버는 이 대칭키를 가지고 암호/복호화를 합니다.**
      - 공개키로 데이터를 암호화해도 되잖아?
          - 공개키 방식 암호화는 매우 많은 컴퓨터 자원을 사용
          - 반면 대칭키는 적은 컴퓨터 자원으로 암호/복호화를 수행할 수 있음
  - ChangeCipherSpec / Finished
      - Client, Server 모두가 서로에게 보내는 Packet으로 교환할 정보를 모두 교화한 뒤 통신할 준비가 다 되었음을 알리는 패킷
      - 'Finished' 패킷을 보내서 SSL Handshake를 종료하게 됨
  - [https://aws-hyoh.tistory.com/entry/HTTPS-통신과정-쉽게-이해하기-3SSL-Handshake](https://aws-hyoh.tistory.com/entry/HTTPS-%ED%86%B5%EC%8B%A0%EA%B3%BC%EC%A0%95-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-3SSL-Handshake)
  </details>
</details>
<details><summary>HTTP2 ★</summary>

- SPDY라는 구글의 비표준 개방형 네트워크 프로토콜에 기반한 차기 HTTP 프로토콜
- 기존 HTTP의 method, status codes, sematics 개념들이 호환

#### Frame & Stream & Messages

- 기존에 평문을 사용하며 개행으로 구분되는 HTTP/1.x 프로토콜과 달리 2.0에선 바이너리 포멧으로 인코딩된 Message, Frame으로 구성
![](/images/http2.png)

- Stream: 구성된 연결 내에서 전달되는 바이트의 양방향 흐름, 하나 이상의 메시지가 전달 가능
- Message: 논리적 요청 또는 응답 메시지에 매핑되는 프레임의 전체 시퀀스
- Frame: HTTP/2에서 통신의 최소 단위. 각 최소 단위에는 하나의 프레임 헤더가 포함

#### HTTP 1.x의 HOL(Head-Of-Line) Blocking 문제 해결

- HTTP/1.1까지는 한번에 하나의 파일만 전송 가능
- 여러 파일을 전송할 경우, 선행하는 파일의 전송이 늦어지면, 전체 파일 전송 시간이 늘어나는 문제 발생
- HTTP/2에서는 여러가지 파일을 한번에 병렬 전송하여 해결
</details>

[https://minhamina.tistory.com/234](https://minhamina.tistory.com/234)