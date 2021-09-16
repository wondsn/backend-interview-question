<details><summary>프로세스와 쓰레드의 차이</summary>

- 프로세스
    - 실행중인 프로그램을 지칭. 프로그램이 디스크부터 메모리에 적재되어 CPU의 지원을 사용하고 있는 작업.
    - 스택과 데이터, 힙 섹션으로 나뉘어져 있다.
    - PCB: 프로세스에 대한 정보를 담고 있는 자료구조.
        - CPU를 점유하고 있는 도중 컨텍스트 스위칭이 발생하면 해당 자료구조에 저장하게 된다.
        - 대표적으로 PID, 프로세스 상태, 프로그램 카운터 등을 저장.
- 쓰레드
    - 프로세스의 실행 흐름 단위
    - 프로세스내 자원 공유 가능.
    - 다른 스레드 코드 및 데이터 섹션 공유. 스택과 프로그램 카운터는 각 스레드 내에서만 구성
        - Why? 스택은 인자, 리턴주소 및 로컬변수를 저장하기 위한 독립적인 메모리 공간. 독립적인 메모리 공간이라는 것은 독립적인 실행 흐름을 가질 수 있음. 따라서 스레드의 정의에 따라 독립적인 스택을 갖게 되는 것.
        - PC Register는 명령어 수행 주소를 가리킴. 스레드 역시 스케쥴러에 의해 CPU을 선점당함. 따라서 연속적으로 수행되지 않음. 그래서 PC Register를 스레드마다 저장하여 어디까지 수행되는지 알 필요가 있음
</details>
<details><summary>멀티 쓰레딩 장단점</summary>

- 장점
    - 시스템 자원 소모가 줄어듦.
    - 스레드간 통신을 프로세스 내 힙 영역을 통해 통신.
    - 컨텍스트 스위칭 시 캐시 메모리를 비워줄 필요가 없음.
- 단점
    - 공유 자원에 대해 동시에 접근하는 문제가 생김.
    - 동기화 작업을 통해 작업 순서를 컨트롤하고 공유 자원에 대한 접근을 컨트롤 해야함.
    - 병목현상이 발생할 수 있음.
- 멀티스레드 vs 멀티프로세스
    - 멀티스레드 장단점
        - 장점: 멀티 스레드는 컨택스트 스위칭을 빠름
        - 단점: 오류로 인해 스레드가 종료되면 프로세스 내 전 스레드가 종료되는 동기화 문제가 있음.
    - 멀티프로세스 장단점
        - 장점: 임의의 프로세스가 죽는다해도 다른 프로세스에 영향을 끼치지 않음
        - 단점: 대신 많은 메모리와 CPU를 사용.
    - 따라서 대상 시스템의 특징에 따라 유동적으로 사용할 필요가 있음
</details>
<details><summary>고유락과 synchronized 블록</summary>

- 자바의 모든 객체는 락을 갖고 있는데 이걸 고유락(intrinsic lock)이라고 함
- synchronized 블록은 동시성 문제를 해결하는 가장 간편한 방법. 고유락을 이용해 여러 스레드의 접근을 제어

    ```java
    public class Counter {
    	private int count;
    	public int increase() {
    		return ++count;
    	}
    }
    ```

- ++count가 atomic하게 동작할 거 같지만 내부에선
    - 변수를 메모리에서 읽고(CPU의 캐시로 가져온다.)
    - 증가시키고 (CPU 연산)
    - 다시 메모리에 쓴다.
- 동시성 프로그래밍에서 문제가 되는 전형적인 Read-Modify-Write 패턴입니다
- 두 스레드가 동시에 같은 값을 읽고, 값을 증가시켜 저장하면 increase() 호출 횟수와 count값에 차이가 발생 → 동시성 문제 발생. count가 공유 자원
- 이런 동시성 문제를 해결하기 위해 count변수로 접근하는 스레드를 제어할 필요가 있음
- 이 때 고유락, 즉 synchronized 블록을 이용하여 Counter 클래스를 Thread-safe하게 만들 수 있음

    ```java
    public class Counter {
    	private int count;
    	public int increase() {
    		synchronized(this) {
    			return ++count;
    		}
    	}
    }

    public class Counter {
    	private int count;
    	public synchronized int increase() {
    		return ++count;
    	}
    }

    ```

- 재진입 가능성 (Re-entrancy)
    - 자바의 고유락은 재진입이 가능
    - 재진입이 가능 → 락의 획득이 호출 단위가 아닌 스레드 단위로 일어난다.

        ```java
        public class Reentrancy {
          public synchronized void a() {
            System.out.println("a");
            // b가 synchronized로 선언되어 있지만 a진입시 이미 락을 획득하였으므로,
            // b를 호출할 수 있다.
            b();
          }
          public synchronized void b() {
            System.out.println("b");
          }
          public static void main(String[] args) {
            new Reentrancy().a();
          }
        }
        ```
</details>
<details><summary>비가시성(가시성 이슈)</summary>

- 멀티코어를 이용해 병렬 프로그래밍을 수행할 때 발생
- 자바에서 변수를 수정할때 Read-Modify-Write 과정을 수행
- 이때, 변수를 메모리에만 저장하지 않고 CPU Cache에도 저장한다.
- 멀티코어 환경에서 CPU Cache는 공유되지 않는다.

    ![](/images/invisibility0.png)

    ```java
    public classMain {

    	private static booleanstopRequest=false;
    	
    	public static void main(String[] args)throwsInterruptedException {
    		Thread backgroundThread =newThread(() -> {
    			int i = 0;
    			while(!stopRequest) {
    		    i++;
    	    }
    	  });
    	  backgroundThread.start();
    	  TimeUnit.SECONDS.sleep(1);
    		stopRequest=true;
    	}
    }
    ```

    - 메인 스레드가 1초 후에 stopRequest를 true로 설정하면 backgroundThread는 반복문을 빠져나올거 같지만 무한 루프에 빠질 수도 있다. (실제로 내 컴에선 무한루프에 빠짐)

        ![](/images/invisibility1.png)

    - CPU1: mainThread, CPU2: backgroundThread라고 합시다. CPU1에서 stopRequest를 true로 설정했으나 메인메모리에 업데이트되지 않고 CPU Cache만 쓰여진다.
    - 그래서 CPU2는 업데이트된 줄 모르고 stopRequest가 false인 채로 무한루프에 빠진다.
    - 이걸 해결하지 위해 공유자원에 volatile을 설정하면 RMW을 수행시 CPU Cache를 거치지 않고 RAM에 직접 읽고 쓰는 작업을 수행합니다.

        ```java
        public classMain {

        	private volatile static booleanstopRequest=false;
        	
        	public static void main(String[] args)throwsInterruptedException {
        		Thread backgroundThread =newThread(() -> {
        			int i = 0;
        			while(!stopRequest) {
        		    i++;
        	    }
        	  });
        	  backgroundThread.start();
        	  TimeUnit.SECONDS.sleep(1);
        		stopRequest=true;
        	}
        }
        ```

        ![](/images/invisibility2.png)
</details>
<details><summary>ExecutorService은 뭔가요?</summary>

- 재사용이 가능한 ThreadPool로 Executor 인터페이스를 확장하여 Thread의 라이프사이클을 제어
- Thread의 라이프사이클(생성, 제거)나 발생할 수 있는 여러가지 low level의 고려사항들이 존재하는데, 이를 개발자가 신경쓰지 않도록 편리하게 추상화한 것이 ExecutorService이다.
- ExecutorService에 task를 지정하면 ThreadPool에서 Thread에 task를 할당하여 수행

    ![](/images/excutor.png)

- 내부적으로 Thread Pool과 Blocking Queue로 구성되어있음
- submit된 task는 Blocking Queue에 들어가서 유휴 Thread가 생길때까지 대기합니다.
    - 유휴 Thread가 생기면 먼저 제출한 task를 할당하여 수행
- Thread를 생성하는 건 비용이 큰 작업이기 때문에 Executor Framework에선 미리 Thread Pool안에 thread를 생성해놓고 관리
</details>
<details><summary>Blocking/Non-Blocking, Sync/Async의 차이</summary>

![](/images/sync.gif)

- Blocking vs. Non-Blocking: 제어의 관점
    - Blocking: 호출된 함수가 자신의 할 일을 모두 **마칠때까지 제어권을 계속 가지고서** 호출한 함수에게 바로 돌려주지 않음
    - Non-Blocking: 호출된 함수가 자신의 할일을 채 **마치지 않았더라도 바로 제어권을 건네주어** 호출한 함수가 다른 일을 진행 수 있도록 해줌
- Sync vs. Async: 순서와 결과(처리)에 대한 관점
    - Sync: 호출된 함수의 수행 결과 및 종료를 호출한 함수가 **호출된 함수 뿐만아니라 호출한 함수도 함께 같이 관리**
    - Async: 호출된 함수의 수행 결과 및 종료를 **호출된 함수 혼자 직접 관리**(as a callback function)
- Blocking & Synchronous
    > 나 : 대표님, 개발자 좀 더 뽑아주세요..
    >
    > 대표님 : 오케이, 잠깐만 거기 계세요!
    >
    > 나 : …?!!
    >
    > 대표님 : (채용 공고 등록.. 지원자 연락.. 면접 진행.. 연봉 협상..)
    >
    > 나 : (과정 지켜봄.. 궁금함.. 어차피 내 일 하러는 못 가고 계속 서 있음)

- Blocking & Asynchronous
    > 나 : 대표님, 개발자 좀 더 뽑아주세요..
    >
    > 대표님 : 오케이, 잠깐만 거기 계세요!
    >
    > 나 : …?!!
    >
    > 대표님 : (채용 공고 등록.. 지원자 연락.. 면접 진행.. 연봉 협상..)
    >
    > 나 : (안 궁금함.. 지나가는 말로 여쭈었는데 붙잡혀버림.. 딴 생각.. 못 가고 계속 서 있음)

- Non-blocking & Synchronous
    > 나 : 대표님, 개발자 좀 더 뽑아주세요..
    >
    > 대표님 : 알겠습니다. 가서 볼 일 보세요.
    >
    > 나 : 넵!
    >
    > 대표님 : (채용 공고 등록.. 지원자 연락.. 면접 진행.. 연봉 협상..)
    >
    > 나 : 채용하셨나요?
    >
    > 대표님 : 아직요.
    >
    > 나 : 채용하셨나요?
    >
    > 대표님 : 아직요.
    >
    > 나 : 채용하셨나요?
    >
    > 대표님 : 아직요~!!!!!!

- Non-blocking & Asynchronous
    > 나 : 대표님, 개발자 좀 더 뽑아주세요..
    >
    > 대표님 : 알겠습니다. 가서 볼 일 보세요.
    >
    > 나 : 넵!
    >
    > 대표님 : (채용 공고 등록.. 지원자 연락.. 면접 진행.. 연봉 협상..)
    >
    > 나 : (열일중..)
    >
    > 대표님 : 한 분 모시기로 했습니다~!
    >
    > 나 : 😍

- [https://youtu.be/oEIoqGd-Sns](https://youtu.be/oEIoqGd-Sns)
</details>
<details><summary>스프링에서 `@Async`가 어떻게 동작하는가요?</summary>

- 스프링의 AOP에 의해서 동작. @Async 어노테이션이 선언된 메서드는 비동기 메서드로 동작
- AOP를 따라가다 보면 `AsyncExecutionAspectSupport`에 다다른다. 여기서 `doSubmit` 메서드에 주목
- 이 때 선언된 메서드의 리턴타입에 따라서 비동기 메서드를 수행하는 로직이 다르다.

    ![](/images/async0.png)

- 다음 4가지 케이스로 나뉘어진다.(CompletableFutuer, ListenableFuture, Future, void)
- void 경우
    - executor.submit(task)를 수행하고 곧바고 null을 반환
    - executor는 스프링부트에서 자동으로 정의된 ThreadPoolTaskExecutor가 동작
    - pool size를 조정하고 싶으면 `spring.task.execution.pool.max-size` 프로퍼티를 조절하면 됨
- Future 경우
    - 메서드 결과를 전달받고 싶으면 Future를 사용
    - 스프링에서 제공하는 AsyncResult는 Future의 구현체

        ![](/images/async1.png)

    - 클라이언트에서 아래와 같이 코드를 작성

        ![](/images/async2.png)

    - 하지만 메서드의 결과를 조회(get 메서드)할 때까지 기다려야 함. 즉 블로킹 현상 발생
    - 논블록킹으로 동작하고 싶으면, **콜백 메서드**를 작성하면 됨
- ListenableFuture 경우
    - Future와 마찬가지로 비동기로 수행한 결과를 전달받을 수 있는데, 클라이언트에서 콜백 메서드를 작성하는 것이 특징

        ![](/images/async3.png)

- CompletableFuture
    - Future 구현체와 마찬가지로 get 메서드를 사용할 수 있음 (블록킹으로 사용할거면)

        ![](/images/async4.png)

    - 하지만 논블록킹으로 사용할거면 `thenAccept`, `thenApply` 메서드를 통해 콜백 메서드를 등록할 수 있음

        ![](/images/async5.png)
</details>