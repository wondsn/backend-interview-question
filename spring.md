- 스프링 프레임워크
    - 스프링 프레임워크 정의
        - 자바 플랫폼을 위한 경량 오픈소스 애플리케이션 프레임워크
        - 특징 자세히
            - 경량 컨테이너로서 자바 객체를 직접 관리.

                각각의 객체 생성, 소멸과 같은 라이프 사이클을 관리하며 스프링으로부터 필요한 객체를 얻어올 수 있다.

            - 스프링은 POJO(Plain Old Java Object) 방식의 프레임워크.

                일반적인 J2EE 프레임워크에 비해 구현을 위해 특정한 인터페이스를 구현하거나 상속을 받을 필요가 없어 기존에 존재하는 라이브러리 등을 지원하기에 용이하고 객체가 가볍다.

            - 스프링은 제어의 역행(IoC : Inversion of Control)을 지원.

                컨트롤의 제어권이 사용자가 아니라 프레임워크에 있어서 필요에 따라 스프링에서 사용자의 코드를 호출한다.

            - 스프링은 의존성 주입(DI : Dependency Injection)을 지원

                각각의 계층이나 서비스들 간에 의존성이 존재할 경우 프레임워크가 서로 연결시켜준다.

            - 스프링은 관점 지향 프로그래밍(AOP : Aspect-Oriented Programming)을 지원

                따라서 트랜잭션이나 로깅, 보안과 같이 여러 모듈에서 공통적으로 사용하는 기능의 경우 해당 기능을 분리하여 관리할 수 있다.

            - 스프링은 영속성과 관련된 다양한 서비스를 지원

                iBatis나 Hibernate 등 이미 완성도가 높은 데이터베이스 처리 라이브러리와 연결할 수 있는 인터페이스를 제공한다.

            - 스프링은 확장성이 높음.

                스프링 프레임워크에 통합하기 위해 간단하게 기존 라이브러리를 감싸는 정도로 스프링에서 사용이 가능하기 때문에 수많은 라이브러리가 이미 스프링에서 지원되고 있고 스프링에서 사용되는 라이브러리를 별도로 분리하기도 용이하다.

- Servlet vs. JSP
    - Servlet vs JSP
        - Servlet: 자바 언어로 웹 개발을 위해 만들어진 것으로, Container가 이해할 수 있게 구성된 순수 자바코드로만 이루어져있습니다.
            - 순수 자바코드를 가지고 html를 구성해야하기 때문에, 개발자가 html 작성 시 실수할 염려가 있다.
        - JSP: html 기반에 JAVA 코드를 블록화하여 삽입한 것으로, Servlet을 좀 더 쉽게 접근할 수 있도록 만들어졌습니다.
            - html 내에서 JAVA 코드를 사용하여 html를 구성할 수 있지만, 비지니스 로직과 뷰 로직이 하나의 소스파일에 있어서 보기가 불편하다.
            - JSP 처리 과정(톰캣이 요청시)
                1. 웹 브라우저가 jsp 파일 요청
                2. 웹 서버는 요청된 jsp 파일을 확인 후 웹 컨테이너로 전달
                3. 웹 컨테이너는 JSP 파일을 서블릿으로 변환. 즉 java 파일로 변환
                4. java 파일을 컴파일하여 class 파일 생성
                5. 다시 웹 서버로 전달하고 웹 서버는 웹 브라우저에게 HTML 형태로 전송
        - 이 구조를 개선하기 위해 MVC 패턴을 도입하고, 더 나아가 Spring MVC를 만들게 된다.
- Spring MVC는 무엇인가요?
    - Spring MVC는 무엇인가요?
        - MVC 패턴이 무엇인가요?
            - 애플리케이션을 구성하기 위한 구성요소를 세가지 역할로 구분한 패턴. 데이터를 저장 및 변경하는 Model, 데이터를 보여주는 View, 그리고 Model과 View를 중재해주는 Controller가 있습니다.
        - Spring MVC 동작방식
            - Spring MVC 동작방식

                [https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F1sKnO%2Fbtra6HpyClm%2FXASXdrmeGJqUgIzjLGmUxk%2Fimg.png](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F1sKnO%2Fbtra6HpyClm%2FXASXdrmeGJqUgIzjLGmUxk%2Fimg.png)

                1. DispatcherServlet이 리퀘스트를 받는다.
                2. URI에 다루는 핸들러와 핸들러를 수행해줄 핸들러 어댑터를 찾는다.
                3. 핸들러 어댑터를 통해 핸들러를 실행한다.
                4. 핸들러 내부에서선 비지니스 로직을 수행하고 View에 보여줄 데이터를 ModelAndView에 담는다.
                5. 수행을 마친 핸들러는 데이터를 보여줘야하는 View Name을 DispatcherServlet에게 전달한다.
                    - `@Controller`를 사용했으면 static, template 밑에 html 파일을 탐색
                    - `@RestController`를 사용했으면 View Name을 그대로 Content Body에 넣어서 반환
                        - Bean 객체를 반환하면 JSON 형태로 반환한다.
                6. View Name을 ViewResolver에게 전달하여 View 객체를 얻는다.
                7. View에 ModelAndView를 넣고 포워드를 한다.
                    - 리다이렉트 vs 포워드
                        - 리다이렉트: 리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시 요청한다. 따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다
                        - 포워드: 다른 서블릿이나 JSP로 이동할 수 있는 기능입니다. 서버 내부에서만 호출되기 때문에 클라이언트는 알아채지 못한다.
                8. 포워딩된 View 객체는 해당하는 뷰(JSP, Thymeleaf)를 호출하고, Model 객체에서 화면 표시에 필요한 객체를 가져와 화면 표시를 처리한다.
    - Spring Framework vs. Spring MVC vs. Spring Boot ★
        - Spring Framework
            - 의존성 주입(Dependency Injection)이나 IoC(Inversion of Control) 기능을 지원하는 프레임워크
            - 위의 특징을 이용해 느슨하게 결합된 애플리케이션을 개발할 수 있음
        - Spring MVC
            - Dispatcher Servlet, ModelAndView, View Resolver와 같이 단순 개념을 이용해 웹 개발을 쉽게 해줌
            - MVC 구현을 쉽게 도와주는 스프링 프레임워크
        - Spring Boot
            - 스프링에서 제공하는 많은 라이브러리들을 기본 설정값으로 자동으로 설정할 수 있게 해줌
            - Spring MVC, Jackson, Validation 등 다양한 종속성들을 각각 패키지해 놓은 것을 가져오기 때문에
- DI(Dependency Injection)은 무엇인가요? ★
    - DI(Dependency Injection)은 무엇인가?
        - 객체간에 의존성이 있는 경우, 스프링 프레임워크가 직접 서비스를 주입시켜주는 것을 의미한다.
        - 스프링 프레임워크에서 DI하는 방법은 총 3가지가 있다.
            1.  매개변수 주입
                - 주입 시점을 알 수가 없어서 선호가 되지 않음
                - 주로 테스트시 빈들을 주입받을 때 사용
            2. 생성자 주입 ★
                - 생성자
                - [https://mangkyu.tistory.com/125](https://mangkyu.tistory.com/125)
            3. Setter 생성자 주입
        - IoC 컨테이너, DI 컨테이너
            - 객체를 생성하고 관리하면서 의존관계를 연결해주는 객체
            - @Configuration 어노테이션을 통해 DI 컨테이너를 명시
            - 객체를 생성하는 메서드마다 @Bean를 명시한다. 그러면 스프링 컨테이너에 스프링 빈으로 등록된다.
                - 스프링 컨테이너가 개발자 대신 의존성을 주입하고 객체들을 관리해줄 것이다.
- Spring AOP란? 그리고 AOP와 Interceptor의 차이는 무엇인가요?
    - Aspect Oriented Programming 관점 프로그래밍
    - OOP에서 기능별로 class를 분리했음에도 공통적으로 중복코드가 발생하는 단점을 해결하고자 나온 방식
        - 로그, 트랜잭션, 자원해제, 성능테스트 메서드
    - 공통 코드를 **횡단 관심사**로 표현하며 개발코드에서 비지니스 로직에 집중
    - 비지니스 로직 실행시, 로직 앞, 뒤 등 원하는 지점에 해당 공통 관심사를 수행할 수 있게 함으로써 중복 코드를 줄일 수 있는 방식
    - AOP와 Interceptor의 차이
        - AOP: AOP는 `joinPoint`, `preceedingJoinPoint` 등의 파라미터를 활용하는 방식
        - Interceptor: 인터셉터의 파라미터가 `HttpServletRequest`, `HttpServletResponse`이다.
    - Filter와 Interceptor의 차이
        - 영역 제어 측면에서 차이가 존재
        - Filter는 웹 어플리케이션내에서 동작. 스프링의 context 접근이 어려움
        - Interceptor는 스프링의 context 내에 존재하므로 context내의 모든 객체를 이용 가능
- 스프링 부트 vs 스프링 ★
    - 스프링 부트 vs 스프링
        - 스프링 프레임워크는 많은 모듈을 지원하지만, 모듈을 사용하기 위한 세팅을 하는데 많은 어려움이 있었습니다.
        - 스프링 부트는 자동설정(AutoConfiguration)을 이용하여 개발에 필요한 모든 내부 디펜던시를 관리합니다.
        - 또한, 개발자가 사용하는 jar와 jar의 버전을 함께 연결할 방법이 필요합니다. 스프링에선 이러한 jar들의 서로 호환되는 버전들을 따로 선택을 해줘야 했습니다. 하지만 스프링 부트에선 SpringBoot Starter라고 불리는 것을 도입하였습니다.
            - Starter는 스프링 부트 어플리케이션에 포함될 수 있는 편리한 dependency 세트입니다
            - Spring과 Hibernate를 사용할려면 spring-boot-starter-data-jpa라는 의존성을 포함하면 됩니다.
- Auto Configuration 동작 방식
    - Auto-configuration 동작방식
        - Spring Boot auto-configuration이 classpath를 체크합니다. 예를 들어 thymeleaf가 있으면 Thymelead template resolver, view resolver, 그리고 템플릿 엔진을 자동으로 설정해줍니다.
        - Spring Data JPA가 classpath에 들어있으면, 자동적으로 repository interface들로부터 repository implementation을 만들어줍니다.
        - 관련 어노테이션으로 Configuration, ComponentScan, EnableAutoConfiguration이 있습니다.
            - Configuration: 자바 설정입니다.
            - ComponentScan: Component가 선언된 객체들을 Spring Boot의 Bean으로 등록해줍니다
            - EnableAutoConfiguration: 스프링부트의 meta 파일(spring.factories)을 읽어서, 미리 정의되어 있는 자바 설정파일들을 빈으로 등록해줍니다.
- CORS(Cross-Origin-Resource-Sharing)
    - CORS(Cross-Origin-Resource-Sharing)
        - 도메인이 다른 2개의 사이트가 데이터를 주고 받을 때 발생하는 문제
        - 서버 내에서 요청이 허락된 도메인에게만 데이터를 주기 위해서
            - 요청을 허락하기 위해서 Access-Control-Allow-Origin: {Domain}과 같은 내용을 Response 헤더에 추가해야함
            - 도메인을 *으로 설정하면 모든 도메인에 대해 요청을 허락할 수 있음
            - 그 외에도 Access-Control-Allow-Methods, Access-Control-Max-Age등을 설정할 수 있음
- Spring Boot 트래픽이 많을 시 개선방안 (인프라 지원 없이)
    - Spring Boot 트래픽이 많을 시 개선방안 (인프라 지원 없이)
        - 쓰레드 풀을 늘림
        - Spring Boot WebFlux를 사용해서 적은 쓰레드로도 트래픽에 대응하도록 적용
        - 사용 경험은 없지만 GUI Framework 개발을 통해 논블로킹 I/O 환경을 경험을 했음
- 서블릿이 무엇인가요?
    - 서블릿이 어떤 식으로 동작하는가요??
    - 서블릿 라이프사이클에 대해서 설명해주세요.
    - Spring 없이 WAS를 만든다면 어떻게 만들 수 있을까요??
    - 서블릿이 무엇인가요?
        - 동적 컨텐츠를 만들기 위해 웹서버에 붙여서 동작하는 프로그램
        - 서블릿이 없으면 HTTP 요청을 파싱하고, 통신 규약에 맞추어서 HTTP 응답을 보내야 한다.
            - 하지만 개발자는 가장 중요한 비지니스 로직에 집중할 수 없게 된다.
        - 그래서 서블릿을 통해 HTTP 요청과 응답을 대신 처리해주는 역할을 수행해준다.
        - 서블릿 컨테이너: 서블릿을 담고있는 객체로 서블릿의 라이프사이클을 관리해준다.
- 웹서버와 WAS의 차이는 무엇인가요? ★
    - 웹 서버: **웹 브라우저(클라이언트)**로부터 **HTTP 요청**을 받아 HTML 문서와 같은 **정적 컨텐츠**를 제공하는 서버
        - 정적 컨텐츠: 요청 인자 값에 상관없이 **달라지지 않는 컨텐츠**
        - HTTP 요청을 받을 수 있고, 정적 컨텐츠를 요청 시 정적 컨텐츠를 제공할 수 있음
        - 하지만 **동적 컨텐츠**를 요청 시, **Web Application Server(WAS)로 전달**하여 WAS가 처리한 결과를 클라이언트에게 전달.
    - WAS(Web Application Server): **DB 조회, 비지니스 로직 처리** 등 다양한 로직 처리를 요구하는 **동적 컨텐츠**를 제공하기 위해 만들어진 서버
        - 동적 컨텐츠: 요청 인자에 따라 **바뀔 수 있는 컨텐츠**
        - 클라이언트로부터 HTTP 요청을 받을 수 있다. (대부분 WAS는 웹서버를 내장하고 있음)
        - 요청에 맞는 정적 컨텐츠를 제공할 수 있다.
        - DB 조회나 다양한 로직 처리를 통해 동적 컨텐츠를 제공할 수 있다.
    - 웹 서버를 같이 사용했을 때 장점
        - 책임 분할을 통한 서버 부하 방지
        - 여러 대의 WAS 로드 밸런싱: WAS가 처리해야하는 요청을 여러 WAS가 나누어서 처리할 수 있도록 설정.
        - 여러 대의 WAS Health Check
            - Health check: 서버에 주기적으로 HTTP 요청을 보내 서버의 상태 확인 (특정 URL 요청에 200응답이 오는지?)
        - 리버스 프록시를 통해 실제 서버를 외부에 노출시키지 않을 수 있다.
- Spring 관점에서 Transaction은 어떻게 동작하나요? ★
    - 스프링에선 AOP를 이용한 **선언전 트랜잭션**을 사용할 수 있음
    - 스프링이 `@Transactional` 애노테이션을 발견하면 해당 빈의 **다이나믹 프록시**를 만듦
        - 프록시 객체는 모든 트랜잭션(open, commit, close) 처리를 **트랜잭션 매니저**에게 위임
            - 모든 트랜잭션 매니저 구현체는 `doBegin` 과 `doCommit` 메소드를 구현
        - **[CGlib 라이브러리](https://javacan.tistory.com/entry/114)**를 통해 실제 코드를 넣은 것처럼 동작. 동적으로 프록시를 생성해주는 기능을 제공
    - 스프링에선 트랜잭션 추상화 인터페이스인 `PlatformTransactionManager`를 지원

        ```java
        public interface PlatformTransactionManager extends TransactionManager {
        	TransactinoStatus getTransaction(@Nullable TransactionDefinition definition);
        	void commit(TransactionStatus status);
        	void rollback(TransactionStatus status);
        }
        ```

        - 다양한 DataSource에 맞게 트랜잭션을 관리할 수 있음
        - `getTransaction`은 파라미터로 들어오는 `TransactionDefinition`에 따라 트랜잭션을 실행
        - 정상적으로 성공하면 `commit` 메서드를, 실패하면 `rollback` 메서드를 실행합니다.
    - PlatformTransactionManager의 구현체로 `DataSourceTransactionManager`, `JpaTransactionManager`, `JtaTransactionManager`가 있습니다.
        - `DataSouceTransactionManager`는 JDBC, `JpaTransactionManager`는 JPA에서 사용되는 매니저.
        - 하나의 데이터베이스를 사용하거나 각각의 데이터를 독립적으로 사용하는 로컬 트랜잭션
        - 하나 이상의 데이터베이스가 참여하는 경우에는 글로벌 트랜잭션에 사용되는 `JtaTransactionManager`를 사용
            - 여러 개의 데이터베이스에 대한 작업을 하나의 트랜잭션으로 묶을 수 있음
            - 다른 서버에 분산된 데이터베이스도 묶을 수 있음
    - 그럼 다른 ORM을 사용한다면 어떻게 될까요?
        - JPA의 경우, `JpaTransactionManager`, Hibernate의 경우 `HibernateTransactionManager`을 사용한다.
        - `spring-boot-starter-data-jpa`을 사용한다면 자동적으로 `JpaTransactionManager`을 사용한다.
        - 만약 다수의 ORM을 사용한다면 어떻게 해야할까요?(Mybatis, JPA를 동시에 사용)
            - 둘다 사용한다면 `DatasourceTransactionManager`, `JpaTransactionManager` 둘 다 빈으로 등록된다.
            - 이 때는 `@Transactional` 어노테이션에 `value`에 어떤 걸 쓸 건지 빈 이름을 명시하면 된다.

            ```java
            @Transactional(value="jpaTransactionManager")
            public void multiTxMethod() {
               ...(내용)
            }
            ```

            - 혹은 `ChainedTransactionManager`을 통해 한번에 관리할 수 있다.
            - [https://lemontia.tistory.com/907](https://lemontia.tistory.com/907)
- 트랜잭션 전파 레벨 ★
    - Propagation.REQUIRED (default)
        - 부모 트랜잭션 내에서 실행하며, 부모 트랜잭션이 없을 경우 새로운 트랜잭션 실행
        - 호출한 곳에서 이미 트랜잭션이 설정되어있으면 기존 트랜잭션 내에서 로직을 실행
    - Propagation.REQUIRED_NEW
        - 매번 새로운 트랜잭션을 시작한다. (새로운 연결을 생성하고 실행)
        - 기존에 트랜잭션이 설정되어있으면, 기존 트랜잭션을 잠깐 대기상태로 두고, 자신의 트랜잭션을 실행한다.
        - 새로운 트랜잭션에서 예외가 발생해도 호출한 곳으로는 롤백이 전파되지 않는다.
    - Propagation.NESTED
        - 기존 트랜잭션이 있을 경우, SAVEPOINT를 설정한다.
        - 설정한 SAVEPOINT 지점까지 커밋, 롤백이 가능하다
        - 하지만 DB가 SAVEPOINT를 지원해야지 사용이 가능 (Oracle)
    - Propagation.MANDATORY
        - 부모 트랜잭션 내에서만 실행되며, 부모 트랜잭션이 없을 경우 Exception이 발생
    - Propagation.SUPPORT
        - 부모 트랜잭션이 존재하면 부모 트랜잭션으로 동작하고, 없을 경우 트랜잭션 없이 실행
    - Propagation.NON_SUPPORT
        - 트랜잭션 없이 실행되며, 부모 트랜잭션이 존재하면 일시 정지한다.
    - Propagation.NEVER
        - 트랜잭션 없이 실행되며, 부모 트랜잭션이 존재하면 예외를 발생한다.
- 트랜잭션 격리 수준
    - 일관성이 없는 데이터를 허용하도록 하는 수준을 의미
    - 격리 수준이 높아질수록 동시성을 높아지지만, 속도는 느려진다.
    - [https://postgresql.kr/blog/pg_phantom_read.html](https://postgresql.kr/blog/pg_phantom_read.html)
    - READ_UNCOMMITED (level 0)
        - 트랜잭션 처리중 or 아직 commit되지 않은 데이터를 다른 트랜잭션이 읽는 것을 허용
        - 어떤 사용자가 A라는 데이터를 B로 변경하는 동안, 다른 사용자는 B라는 완료되지 않은(Uncommit 혹은 Dirty) 데이터 B를 읽을 수 있다.
        - Dirty Read 발생
            - 다른 트랜잭션에서 처리한느 작업이 완료되지 않았는데도 다른 트랜잭션에서 읽을 수 있는 현상
            - READ_UNCOMMITED 격리 수준에서만 발생
    - READ_COMMITED (level 1)
        - Dirty Read 방지: 트랜잭션이 commit 되어 확정된 데이터만 읽도록 허용
        - A라는 데이터가 B로 변경되는 동안 다른 사용자는 해당 데이터에 접근할 수 없음
    - REPEATABLE_READ (level 2)
        - 트랜잭션이 완료될 때까지 **SELECT 문장이 사용하는 모든 데이터**에 `shared lock`이 걸림
            - 따라서, 다른 사용자는 해당 영역에 대한 데이터 수정이 불가능하다.
        - 선행 트랜잭션이 읽은 데이터는 트랜잭션이 종료될 때까지 후행 트랜잭션이 갱신하거나 삭제하는 것을 불허함으로써 같은 데이터를 두 번 쿼리했을 때 일관성 있는 결과를 리턴.
        - Phantom READ
            - 트랜잭션이 데이터를 두번 읽는다고 가정할 때, where 절의 조건에 맞는 레코드가 추가로 생성될 때 발생
            - 새로운 유령(Phantom)행이 나오지만 Phantom read를 지원하지 않으면 새로 생긴 행을 볼 수 없다.
    - SERIALIZABLE (level 3)
        - 완벽한 읽기 일관성 모드를 제공
        - 성능 측면에서 동시 처리성능이 가장 낮다. (거의 사용 x)
        - 데이터이 일관성 및 동시성을 위해 MVCC(Multi Version Concurrency Control)를 사용하지 않음
        - 트랜잭션이 완료될 때까지 SELECT 문장이 사용하는 모든 데이터에 shared lock이 걸리므로 다른 사용자는 그 영역에 해당하는 데이터에 대한 수정 및 입력이 불가능하다.
        - MVCC
            - 다중 사용자 DB 성능을 위한 기술
            - 데이터 조회 시, LOCK을 사용하지 않고, **데이터의 버전을 관리**해 **데이터의 일관성 및 동시성을 높이는 기술**
- 웹 3계층이 어떻게 동작하는지 설명해주세요.
    - 웹 3계층이란 프레젠테이션, 비지니스, 데이터베이스 로직으로 구형되어 있습니다.
    - 각 계층은 물리적으로도 독립적이며, 각 계층의 변경이 다른 계층에 영향을 끼치지 않습니다.
    - 프레젠테이션 계층(클라이언트 계층)
        - 사용자 인터페이스를 지원한다.(인터넷 브라우저의 정적인 데이터를 제공)
        - GUI, Front-End라고 불린다.
        - 비지니스로직이나 데이터관리 코드를 포함해서는 안됨
        - 웹서버를 주로 뜻함(ex. Javascript, HTML, CSS, Image)
    - 비지니스 계층 (애플리케이션 계층)
        - 트랜잭션 게층이라고도 하는데, 비지니스 로직은 워크스테이션으로부터 클라이언트 요청에 대해 마치 서버처럼 행동
        - 어떤 데이터가 필요한지 결정하고, 메인프레임 컴퓨터 상에 위치하고 있을 데이터베이스 계층의 프로그램에 대해서는 마치 클라이언트처럼 행동한다.
        - 정보처리 규칙을 가지고 있음(동적인 데이터 처리)
        - middleware, Back-End라고 불림
        - 프레젠테이션 코드나 데이터 관리 코드를 포함해선 안됨
        - 애플리케이션 서버(WAS)를 뜻함 (ex. Spring, PHP, ASP.NET)
    - 데이터 계층
        - 데이터베이스에 엑세스해서 읽거나 쓰는 것을 관리하는 계층
        - 데이터의 생성, 수정, 조회 관점만 관리하기 때문에 애플리케이션보다 덜 복잡
- Maven과 Gradle의 차이점
    - Maven
        - Build라는 동적인 요소를 XML로 정의하기에는 어려운 부분이 많음
        - 설정 내용이 길어지고 가독성이 떨어짐
        - 의존관계가 복잡한 프로젝트 설정하기 부적절
        - 상속구조를 이용한 멀티 모듈 구현
        - 특정 설정을 소수의 모듈에서 공유하기 위해서는 부모 프로젝트를 생성하여 상속하게 해야함
    - Gradle
        - Groovy를 사용하기 때문에 동적인 빌드는 Groovy 스크립트로 플러그인을 호출하거나 직접 짜야한다.
        - Configuration Injection 방식을 사용해서 공통 모듈을 상속해서 사용하는 단점을 커버
        - 설정 주입 시 프로젝트 조건을 체크할 수 잇어서 프로젝트별로 주입되는 설정을 다리게 할 수 있다.
- JUnit5 가이드

    [https://donghyeon.dev/junit/2021/04/11/JUnit5-완벽-가이드/](https://donghyeon.dev/junit/2021/04/11/JUnit5-%EC%99%84%EB%B2%BD-%EA%B0%80%EC%9D%B4%EB%93%9C/)

- JaCoCo
    - 자바 코드 커버리지를 체크하는데 사용되는 오픈소스 라이브러리
    - Line, Branch Converage를 제공
    - 커버리지 결과를 파일형태(html, xml, csv) 형태로 제공
- 메시지 컨버터는 어디서 동작하나요?
    - 핸들러 어댑터가 컨트롤러를 호출하기 전에, ArgumentResolver가 컨트롤러의 파라미터, 어노테이션 정보를 기반으로 전달할 데이터를 생성
    - 이때 ArgumentResolver에서 supportsParameter()을 호출해서 파라미터를 지원하는지 체크
    - 지원하면 resolveArgument()를 호출해서 실제 객체를 생성한다.

        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f39d1bb9-c556-4b3a-aef7-4a10720853b4/Untitled.png)

    - 

[https://kim6394.tistory.com/161](https://kim6394.tistory.com/161)