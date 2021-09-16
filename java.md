<details><summary>JVM의 구조에 대해 설명하세요.★</summary>

- JVM(Java Virtual Machine): Java Byte Code를 OS에 맞게 해석해주는 가상머신
- Java Compiler가 .java → .class(Java Byte Code)로 변환
- JVM은 OS가 Java Byte Code를 이해할 수 있도록 해석해주는 역할을 수행
    - OS에 네이티브한 C언어에 비해 속도는 느렸지만, JIT(Just In Time) 컴파일러 구현으로 이점을 극복
- Java ByteCode는 OS 상관없이 동작
    - JVM은 OS에 독립적이지만 의존적(??)
- JVM 구성

    ![](/images/jvm0.png)

    - Class Loader
        - 런타임 시점에 .class에서 바이트코드를 읽고 메모리에 저장
        - 로딩: 클래스를 읽어오는 과정
        - 링크: 레퍼런스를 연결하는 과정
        - 초기화: static 값들을 초기화 및 변수에 할당
    - Runtime Data Areas
        - Heap과 Method는 모든 쓰레드가 공유, 나머지는 쓰레드마다 생성
        - JVM이 프로그램을 수행하기 위해 OS로부터 별도로 할당받은 메모리 공간
            - PC Register: CPU가 명령어을 수행하는 동안 필요한 정보를 저장
            - JVM Stack: Thread가 새작될 때 생성되며 메소드와 메소드 정보 저장
            - Native Method Stack: Java 이외의 언어로 작성된 native 코드를 위한 Stack(JNI)
            - Method Area: 모든 쓰레드가 공유하는 메모리 영역(클래스, 인터페이스, 메소드, 필드, Static 변수등의 바이트 코드 등을 보관)
            - Heap: 런타임시 동적으로 할당하여 사용하는 영역. Class를 통해 인스턴스가 생성하면 Heap에 저장됨
                - Heap의 경우 명시적으로 만든 class와 암묵적인 static class(.class 파일의 class)가 담긴다.
                - 또한 암묵적인 static 클래스의 경우, 클래스 로딩 시 class 타입의 인스턴스를 만들어 힙에 저장한다. 이는 Reflection에 등장
    - Execution Engine
        - Load된 Class의 ByteCode를 실행하는 Runtime Module
        - Class Loader를 통해 JVM 내의 Runtime Data Areas에 배치된 바이트 코드는 Execution Engine에 의해 실행(바이트 코드를 명령어 단위로 읽어서 실행)
- Class Loader

    ![](/images/jvm1.png)

    - 로딩 → 링크 → 초기화 순으로 진행
    - 로딩
        - 클래스 로더가 .class 파일을 읽고, 그 내용에 따라 적절한 바이너리 데이터를 만들고 Method Area에 저장
        - 이 때 메소드 영역에 저장하는 데이터
            - FQCN(Fully Qualified Class Name)
            - 클래스, 인터페이스, Enum
            - 메서드 변수
        - Bootstrap → Extension → Application 순서로 클래스를 찾는다
            - Bootstrap에 존재하는지 검사 (JAVA_HOME/lib)
            - 없다면, Extension에 존재하는지 검사 (JAVA_HOME/lib/ext)
            - 없다면, Application에 존재하는지 검사(일반적으로 99% Application에 존재) (앱 classpath)
                - 앱 실행시 주는 -classpath 옵션 또는 java.class.path 환경변수의 값에 해당하는 위치
            - 없다면 `ClassNotFoundException` 예외가 발생
        - 로딩이 끝나면 해당 클래스 타입의 Class 객체를 생성하여 Heap Area에 저장.
    - 링크
        - Verify, Prepare, Resolve(Optional) 세 단계로 나눠짐
        - Verify: .class 파일의 형식이 유효한지 체크
        - Prepare: 클래스 변수(static 변수)의 기본값에 따라 필요한 메모리를 준비
        - Resolve: 심볼릭 메모리 레퍼런스를 Method Area에 있는 실제 레퍼런스로 교체
    - 초기화
        - Static 변수에 값을 할당한다. (static 블럭이 있다면 이 때 실행)
</details>
<details><summary>JIT(Just In-Time)이 무엇인가요?</summary>

- 프로그램을 실제 실행하는 시점에 기계어로 번역하는 컴파일 기법
- 프로그래밍 언어를 읽어가면서 해당 기능에 대응하는 기계어 코드를 실행.
    - 반면 정적 컴파일을 실행하기 전에 프로그램 코드를 기계어로 변역
- 그럼 컴파일은 어떻게 이루어지죠?
    1. Java Compiler에 의해 Java Source로부터 Byte Code가 생성
    2. JVM에 있는 Class Loader에 의해 Byte Code는 JVM내로로드, 실행엔진에 의해 기계어로 해석되어 메모리(Rumtime Data Area)에 배치
    3. 실행엔진에는 Interpreter와 JIT Compiler가 있는데, Interpreter에 의해 Byte Code를 한 줄씩 읽어 실행하다가 적절한 시점에 Byte Code 전체를 컴파일하고 더이상 인터프리팅하지 않고 해당 코드를 직접 실행
- JIT Compiler에 의해 해석된 코드는 캐시에 보관. 한번 컴파일된 후에는 빠르게 수행할 수 있다
- 코드 전체를 컴파일하기 때문에 인터프리팅하는 것보다 시간이 오래 걸림. 한번만 실행해도 되는 코드들을 JIT보다 인터프리팅하는 것이 유리
</details>
<details><summary>가비지 컬렉터는 무엇인가요? ★★</summary>

- 사용되지 않는 객체를 알고리즘을 통해 모은 뒤 메모리에서 해제해주는 작업
- GC에서는 그 중에서 힙 메모리를 다루고 있고, 자바 콜렉터가 인식하고 할당하는 자바 메모리 영역은 힙 영역인 것이다.
- GC 수거 대상은 어떻게 됩니까?

    ![](/images/gc0.jpeg)

    - Unreachable Objects 객체가 된다. Unreachable Object는 참조되지 않는 객체들을 의미한다.
    - GC Roots로 부터 참조되는 객체들을 Reachable Objects라고 부른다.
    - GC Roots는 stack 영역의 데이터, method 영역의 static 데이터, JNI에 의해 생성된 객체들이 될 수 있다.
        - Reachable Objects의 대상을 다음과 같다
        - 힙 내에 다른 객체에 의한 참조
        - Java 스택, 즉 Java 메서드 실행 시에 사용하는 지역변수와 파라미터들에 의한 참조
        - 네이티브 스택(JNI, Java Native Interface)에 의해 생성된 객체에 대한 참조
        - 메서드영역의 정적 변수에 의한 참조
- 어떤 식으로 Unreachable Objects들이 수거가 되죠?
    - Mark and Sweep 알고리즘을 수행합니다.
    - Marking 과정은 GC는 GC Root로부터 모든 변수를 스캔하여 어떤 객체를 참조하는지 찾아서 마킹한다.
    - Sweep 과정은 마킹되지 않는 Unreachable Objects를 힙에서 제거해준다.
    - 추가적으로 Compact 작업을 통해 삭제된 객체의 시작 주소를 모아 메모리가 하당된 부분과 그렇지 않은 부분으로 나누어서 메모리 단편화를 막는다.
- 결국에 GC가 고려해야하는 영역은 Young 내 Eden 영역, 2개의 Survivor 영역, 그리고 Old 영역으로 나누어지게 됩니다.
- 일단 메모리에 객체가 생기면 Eden 영역에 할당됩니다. Eden 영역에 데이터가 어느 정도 쌓이면, Minor GC가 발생하게 됩니다.
    - Mark and Sweep 알고리즘에 의해 마킹된 객체들은 Survivor 영역으로 옮겨지고, 나머진 전부 Sweep이 됩니다.
    - **이 때, 반드시 다른 영역의 Survivor는 비어져 있어야 합니다.**
    - 옮겨진 객체들의 age들이 증가하게 됩니다.
- 그리고 또 Eden 영역에 메모리가 꽉 차면 Minor GC가 발생하게 됩니다.
    - 그러면서 Eden과 Survivor 영역을 통틀어서 Mark and Sweep 과정이 일어나게 됩니다.
    - 그리고 살아남은 객체들은 다른 Survivor 영역으로 옮겨지게 됩니다.
    - 이 과정에 계속 반복되게 됩니다.
- 계속해서 살아남은 객체들이 특정 임계점(age)에 도달하면 Old 영역으로 이동하게 됩니다. 이걸 promoted라고 부릅니다.
    - 만약 Old 영역에 메모리가 꽉 차게 되면 Major GC가 발생하게 됩니다.
- GC가 발생하는 순간에 모든 쓰레드들이 멈추는데, 이걸 stop-the-world라고 부른다
- [https://asfirstalways.tistory.com/159?category=660807](https://asfirstalways.tistory.com/159?category=660807)
- GC 방식([https://d2.naver.com/helloworld/1329](https://d2.naver.com/helloworld/1329)) - Major GC
    - Serial GC: 싱글 스레드를 사용하여 GC 작업을 수행
        - 다른 GC에 비해 stop-the-world 시간이 길다
        - Mark-Compact(Sweep 포함) 알고리즘 사용
        - 가급적이면 비추
    - Parallel GC: Java8 Default GC
        - GC를 멀티 쓰레드로 수행(Old 영역은 싱글스레드 그대로)
        - Serial GC에 비해 stop-the-world 시간 감소
        - Mark-Sweep-Compaction 알고리즘 수행
    - Parallel Old GC: Parallel GC를 개선한 GC
        - Parallel GC와 동일하지만 Mark-Summary-Compact 알고리즘을 수행
            - Summary: 멀티 스레드가 old 영역을 분리해서 훑는다.
    - CMS(Concurrent Mark Sweep) GC: stop-the-world 시간을 줄이기 위해 고안
        - Initial Mark: GC Root에서 참조하는 객체들만 우선 식별(이 때 stop-the-world 발생)
        - Concurrent Mark: 이전 단계에서 식별한 객체들이 참조하는 모든 객체 추적(이때는 다른 쓰레드와 같이 실행)
        - Remark: 이전 단계에서 식별한 객체를 다시 추적, 추가되거나 참조가 끊긴 객체 확정 - 이때 잠깐 stop-the-world 발생. 그리고 멀티스레드로 진행
        - Concurrent Sweep: 최종적으로 unreachable 객체들을 삭제
        - Compact 과정이 없음.
    - G1(Garbage First) GC: CMS GC를 개선(메모리 단편화) 좀 더 자세히
        - Java 9+ Default GC
        - Heap을 일정한 크기의 Region으로 나눔
        - 전체 Heap이 아닌 Region 단위로 탐색
        - Compact 진행
</details>
<details><summary>Collection</summary>

자바에서 `컬렉션 프레임워크(collection framework)`란 **다수의 데이터를 쉽고 효과적으로 처리할 수 있는 표준화된 방법을 제공하는 클래스의 집합** 을 의미합니다

즉, 데이터를 저장하는 자료 구조와 데이터를 처리하는 알고리즘을 구조화하여 클래스로 구현해 놓은 것입니다.

이러한 컬렉션 프레임워크는 자바의 인터페이스(interface)를 사용하여 구현됩니다.

### **Collection 인터페이스**

1. **List 인터페이스** : 배열과 유사하되, 추가할때마다 자동으로 Boundary를 늘려주는 구조로, 중복된 데이터를 허용하며, 순서가 존재한다.
    - ArrayList : 배열로 구현됬으며, 인접해 있기 때문에 데이터 조회에 매우 빠르다 하지만, 빈번한 삽입, 삭제시 새로 배열을 만들고 데이터를 옮겨야 하기 때문에 LinkedList에 비하여 속도가 느리다.
    - LinkedList : 링크 구조로 되어 있기 때문에 조회는 ArrayList에 비해 느리지만, 삽입 삭제시 링크를 끊고 새로 추가되는 데이터에 링크만 연결하면 되기 때문에 삽입, 삭제에 유리하다.
    - Vector : 구현 방식은 ArrayList와 유사하지만 Vector를 개선한 것이 ArrayList이다. 또한 Vector의 경우에는 ArrayList와 달리 Synchronized(동기화)가 걸려 있어 여러 쓰레드에서 동시에 접근할 수 없다.
2. **Set 인터페이스** : 집합처럼 중복된 데이터를 허용하지 않으며, 순서가 없다. 또한, 객체 내부의 중복된 데이터를 배제하고 싶은 경우 Object 클래스의 equals 메서드와 hashCode 메서드의 재정의가 반드시 필요하다.
    - HashSet
    - TreeSet : 순서가 있는 HashSet으로 이진 트리 구조로 만들어 졌다. 순서에 맞게 정렬되어 저장되기 위해서 Comparable을 구현해야한다.
3. **Map 인터페이스** : key와 value 쌍으로 데이터를 저장하며, key는 중복될 수 없고, value는 중복 저장이 가능하다.
    - HashMap
    - TreeMap
    - Properties : key value 쌍으로 저장되지만 value의 타입이 String만 가능하다.
    - Hashtable : HashMap과 구조는 같으며, 단지 Synchronized(동기화) 되어져 있다는 점이 다른점이다.
</details>
<details><summary>Annotation</summary>

- 주석이라는 뜻으로 인터페이스를 기반으로 한 문법이다. 주석과는 그 역할이 다르지만 주석처럼 코드에 달아 클래스에 특별한 의미를 부여하거나 기능을 주입할 수 있다. 또 해석되는 시점을 정할 수도 있다.(Retention Policy) 어노테이션에는 크게 세가지 종류가 존재한다. JDK에 내장된 built-in-annotation(, @Deprecated, @SurpressWarning, @FunctionalInterface), 어노테이션에 대한 정보를 나타내기 위한 어노테이션인 Meta annotation, 그리고 개발자가 직접 만들어내는 Custom Annotation이 있다.
- 빌트인 어노테이션으로 다음이 있다.
    - Override: 메서드 앞에만 붙일 수 있으며 수퍼클래스의 메소드를 오버라이드한 메소드임을 컴파일러에게 명시한다..
    - Deprecated: 차후 버전에 지원되지 않을 수 있는 기능을 명시한다. 컴파일 시점에 개발자에게 경고메시지를 날린다.
    - SupressWarning: 컴파일시 발생하는 경고를 무시하도록 컴파일러에게 명시한다.
    - FunctionalInterface: 컴파일러에게 다음의 인터페이스는 함수형 인터페이스라는 것을 명시한다.
- 메타 어노테이션으로 다음들이 존재
    - Target: 어노테이션이 적용가능한 대상을 지정한다.
    - Retention: 어노테이션이 유지되는 기간을 지정한다. 총 3가지 유지정책이 존재
        - SOURCE: 소스파일에만 존재, 클래스 파일에는 존재 x
        - CLASS: 클래스 파일에는 존재하지만 런타임 시에 사용 불가, Retention 어노테이션 디폴트
        - RUNTIME: 클래스 파일에 존재하며 런타임 시에도 사용 가능하다. 런타임 시에 리플렉션을 통해 클래스 파일에 저장된 어노테이션 정보를 읽어서 처리할 수 있음, 커스텀 어노테이션을 만들 때 주로 사용.
</details>
<details><summary>stream ★</summary>

- 컬렉션 내 데이터의 흐름을 의미
- 배열 또는 컬렉션 인스턴스에 함수 여러 개를 조합하여 원하는 결과를 필터링하고 가공된 결과를 얻을 수 있음
- 람다를 이용해 코드의 양을 줄이고 간결하게 표현할 수 있습니다.
- 하나의 작업을 둘 이상의 작업으로 잘게 나눠서 동시에 진행하는 병렬처리가 가능합니다.
    - 쓰레드를 이용해 많은 요소들을 빠르게 처리할 수 있습니다.
    <details><summary>생성하기: 스트림 인스턴스 생성</summary>

    - 배열 스트림: `Arrays.stream` 메소드를 사용할 수 있음

        ```java
        String[] arr = new String[]{"a", "b", "c"};
        Stream<String> stream = Arrays.stream(arr);
        Stream<String> streamOfArrayPart = Arrays.stream(arr, 1, 3);
        ```

    - 컬렉션 스트림: 컬렉션 인터페이스에 추가된 `stream` 디폴트 메소드를 이용해서 스트림을 만들 수 있음

        ```java
        public interface Collections<E> extends Iterable<E> {
        	default Stream<E> stream() {
        		return StreamSupport.stream(spliterator(), false);
        	}
        	// ...
        }

        List<String> list = Arrays.asList("a", "b", "c");
        Stream<String> stream = list.stream();
        Stream<String> parallelStream = list.parallelStream();
        ```

    - 빈 스트림: 빈 스트림은 null 대신 사용할 수 있음(`Stream.empty()`)
    - Stream.builder: 빌더를 사용하면 스트림에 직접적으로 원하는 값을 넣을 수 있음

        ```java
        Stream<String> builderStream =
        	Stream.<String>builder()
        		.add("Eric").add("Elena").add("Java")
        		.build();
        ```

    - Stream.generate: `generate` 메소드를 사용하면 `Supplier<T>`에 해당하는 람다로 값을 넣을 수 있음. `Supplier<T>`는 인자가 없고, 리턴값만 있는 함수형 인터페이스. 이 때 생성되는 스트림은 무한하기 때문에, `limit` 함수를 사용하여 최대 크기를 제한해야 합니다.

        ```java
        public static<T> Stream<T> generate(Supplier<T> s) { ... }

        Stream<String> generatedStream =
        	Stream.generate(() -> "gen").limit(5);
        ```

    - Stream.iterate: `iterator` 메소드를 이용하면 초기값과 해당 값을 다루는 람다를 이용해서 스트림에 들어갈 요소를 만듦. 이 때 생성되는 스트림은 무한하기 때문에, `limit` 함수를 사용하여 최대 크기를 제한해야 합니다.

        ```java
        Stream<Integer> iteratedStream =
        	Stream.iterate(30, n -> n + 2).limit(5) // [30, 32, 34, 36, 38]
        ```

    - 기본 타입형 스트림: 기본 타입(int, long, double) 스트림을 지원. range, rangeClosed의 차이는 끝의 수를 붙이냐 안붙이냐 차이

        ```java
        IntStream intStream = IntStream.range(1, 5);          // [1, 2, 3, 4]
        LongStream longStream = LongStream.rangeClosed(1, 5); // [1, 2, 3, 4, 5]
        ```

        - 제네릭을 사용하지 않기 때문에 불필요한 오토박싱이 일어나지 않음. 필요한 경우 `boxed` 메소드를 이용하여 박싱할 수 있음(int → Integer, long → Long, double → Double)

        ```java
        Stream<Integer> boxedIntStream = IntStream.range(1, 5).boxed();
        ```

    - 문자열 스트림: String을 이용해서 스트림을 생성할 수 있음

        ```java
        // 스트링의 각 문자를 IntStream으로 변환하는 예제
        IntStream charsStream =
        	"Stream".chars();  // [83, 116, 114, 101, 97, 109]

        // 정규표현식으로 문자열을 자르고, 각 요소들로 스트림을 만든 예제
        Stream<String> stringStream =
        	Pattern.compile(", ").splitAsStream("Eric, Elena, Java");
        // [Eric, Elena, Java]
        ```

    - 파일 스트림: 자바 NIO의 `Files` 클래스의 `lines` 메소드는 해당 파일의 각 라인을 스트링 타입의 스트림으로 만듦

        ```java
        Stream<String> lineStream =
        	Files.lines(Paths.get("file.txt"),
        							Charset.forName("UTF-8"));
        ```

    - 병렬 스트림: `stream` 메소드 대신 `parallelStream` 메소드를 사용해서 병렬 스트림을 생성. 내부적으로는 쓰레드를 처리하기 위해 [Fork/Join framework](https://warpgate3.tistory.com/entry/ForkJoin-Framework-in-Java)를 사용.

        ```java
        Stream<Product> parallelStream = productList.parallelStream();

        boolean isParallel = parallelStream.isParallel();
        boolean isMany = parallelStream
        	.map(product -> product.getAmount() * 10)
        	.anyMatch(amount -> amount > 200);   // 각 코드의 작업을 쓰레드를 이용해 병렬처리됨

        Arrays.stream(arr).parallel();  // 배열을 이용해서 병렬 스트림을 생성

        // 기본형 타입 스트림 역시 parallel 사용 가능
        IntStream intStream = IntStream.range(1, 150).parallel();
        boolean isParallel = intStream.isParallel();

        // 시퀀셜 모드로 돌릴려면 sequential 메소드 사용
        IntStream seqStream = intStream.sequential();
        boolean isParallel = intStream.isParallel(); // 이 때는 false 나옴
        ```

    - 스트림 연결하기: `Stream.concat` 메소드를 이용해 두 개의 스트림을 연결하여 새로운 스트림을 만들어냄

        ```java
        Stream<String> stream1 = Stream.of("Java", "Scala", "Groovy");
        Stream<String> stream2 = Stream.of("Python", "Go", "Swift");
        Stream<String> concat = Stream.concat(stream1, stream2);
        // [Java, Scala, Groovy, Python, Go, Swift]
        ```
    </details>
    <details><summary>가공하기: 내가 원하는 것만 뽑아낼 수 있음. 가공 단계를 중간 작업(intermediate operations)이라고 부르는데 이러한 작업은 스트림을 리턴하기 때문에 스트림끼리 chaining을 할 수 있음</summary>

    - Filtering: 스트림 내 요소들을 하나씩 평가해서 걸러내는 작업. 인자는 Predicate를 받음. Predicate는 boolean을 리턴하는 함수형 인터페이스

        ```java
        Stream<T> filter(Predicate<? super T> predicate);

        Stream<String> names = Arrays.asList("Eric", "Elena", "Java");
        Stream<String> stream =
        	names.stream().filter(name -> name.contains("a"));
        ```

    - Mapping: 스트림 내 요소들을 하나씩 특정 값으로 변환. 이 때 값을 변환하기 위한 람다를 인자로 받음

        ```java
        <R> Stream<R> map(Function<? super T, ? extends R> mapper);
        ```

        - 스트림에 들어가있는 값이 input이 되어서 특정 로직을 거친 후 output이 되어 새로운 스트림에 담김

        ```java
        Stream<String> names = Arrays.asList("Eric", "Elena", "Java");
        Stream<String> stream =
        	names.stream().map(String::toUpperCase);
        // [ERIC, ELENA, JAVA]

        Stream<Integer> stream =
        	productList.stream().map(Product::getAmount);
        // [23, 14, 13, 23, 13]
        ```

        - map 이외에도 조금 더 복잡한 flatMap 메소드가 존재. 인자로 mapper를 받는데 리턴 타입이 Stream. flatMap은 중첩 구조를 한 단계 제거하고 단일 컬렉션으로 만들어주는 역할

        ```java
        List<List<String>> list =
        	Arrays.asList(Arrays.asList("a"),
        								Arrays.asList("b"));
        List<String> flatList =
        	list.stream()
        		.flatMap(Collection::stream)
        		.collect(Collectors.toList()); 
        ```

    - Sorting: 스트림을 정렬. Comparator를 이용. 인자가 없으면 오름차순으로 정렬

        ```java
        Stream<T> sorted();
        Stream<T> sorted(Comparator<? super T> comparator);

        IntStream.of(14, 11, 20, 39, 23)
        	.sorted()
        	.boxed()
        	.collect(Collectors.toList()); // [11, 14, 20, 23, 39]

        List<String> lang =
        	Arrays.asList("Java", "Scala", "Groovy", "Python", "Go", "Swift");
        lang.stream()
        	.sorted()
        	.collect(Collectors.toList());
        // [Go, Groovy, Java, Python, Scala, Swift]

        lang.stream()
        	.sorted(Comparator.reverseOrder())
        	.collect(Collectors.toList());
        // [Swift, Scala, Python, Java, Groovy, Go]
        ```
    </details>
    <details><summary>결과만들기:</summary>
    </details>

- Lazy Evaluation(지연 연산)
    - 데이터가 실제로 필요해지는 시점에 연산을 시작하는 것을 의미(↔  Eager Evlauation)
    - Java 8 이후에 Functional 함수가 들어오면서 Lazy Evaluation을 수행할 수 있습니다.
    - 필요하지 않은 연산을 하지 않는 것이 가능하다는 것.

    ```java
    static boolean compute(String str) {
    	System.out.println("executing...");
    	try {
    		Thread.sleep(1000);
    	} catch (InterruptedException ignore) {
    	}
    	return str.contains("a");
    }

    static String eagerMatch(boolean b1, boolean b2) {
    	return b1 && b2 ? "match" : "incomplete";
    }

    @Test
    public void solution_1() {
    	boolean b1 = compute("Hello_1");  // 이미 이 시점에서 eagerMatch는 "incomplete"를 내밷음
    	boolean b2 = compute("Hello_2");  // 그래서 이 연산을 할 필요가 없음
    }
    ```

    ```java
    static boolean compute(String str) {
    	System.out.println("executing...");
    	try {
    		Thread.sleep(1000);
    	} catch (InterruptedException ignore) {
    	}
    	return str.contains("a");
    }

    static String lazyMatch(Supplier<Boolean> b1, Supplier<Boolean> b2) {
    	// get 함수를 호출한 시점에 compute 함수를 호출함
    	// 그 결과 b1.get만으로도 incomplete라는 결과를 얻게됨.
    	return b1.get() && b2.get() ? "match" : "incomplete";
    }

    @Test
    public void solution_1() {
    	Supplier<Boolean> a = () -> compute("Hello_1");  // 이 시점에서 compute 함수를 실행하지 않음
    	Supplier<Boolean> b = () -> compute("Hello_2");
    	lazyMatch(a, b);
    }

    ```

    - 자바 스트림에서는 모든 원소에 대해서 연산을 하지 않고 필요로 하는 부분만 연산 처리함
    - 마지막에 collect, findFirst, limit 등이 어떻게 되는지에 따라서 연산하는 엘리먼트 수가 달라집니다.
- stream과 Collector의 차이 (+ 메모리적 차이)
</details>
<details><summary>Lambda ★</summary>

- Stream 연산들은 매개변수로 함수형 인터페이스(Functional Interface)를 받도록 되어있음
- Stream API를 정확히 이해하기 위해 람다식과 함수형 인터페이스에 대해 알아야 함
- **함수를 하나의 식(expression)으로 표현**한 것.
- 메소드 이름이 필요없기 때문에 **익명함수**의 한 종류라고 볼 수 있음
- 함수와 람다 함수 비교

    ```java
    // 기존 방식
    // 반환타입 메소드명 (매개변수, ...) {
    //   실행문
    // }

    public String hello() {
    	return "Hello World";
    }

    // 람다 방식
    // (매개변수, ...) -> { 실행문 ... }
    () -> "Hello World";
    ```

    - 불필요한 코드를 줄이고 가독성을 높일 수 있음
    - 함수형 인터페이스의 인스턴스를 생성하여 함수를 변수처럼 선언하는 람다식에서는 메소드 이름이 불필요
    - 컴파일러가 문맥을 살펴서 타입을 추론
    - 일급 객체이기 때문에 Stream API의 매개변수로 전달이 가능
- 함수형 인터페이스(Functional Interface)
    - 함수를 일급 객체처럼 다룰 수 있게 해주는 어노테이션
    - 인터페이스에 선언하여 단 하나의 추상 메소드만을 갖도록 제한하는 역할.
    - 람다식이 함수형 인터페이스를 반환하기 때문임(람다식의 타입이라고 볼 수 있음)
    - 예를 들어 두 값 중 큰 값을 구하는 익명함수를 개발한다고 가정

        ```java
        public class Lambda {
        	public static void main(String[] args) {
        		System.out.println(new MyLambdaFunction() {
        			public int max(int a, int b) {
        				return a > b ? a : b;
        			}
        		}.max(3, 5));
        	}
        }
        ```

    - 하지만 함수형 인터페이스의 등장으로 우리는 **함수를 변수처럼 선언**할 수 있음
    - 함수형 인터페이스를 구현하기 위해서는 인터페이스를 개발하여 그 내부에 **1개 뿐인 abstract 함수를 선언**하고, `@FunctionalInterface` 어노테이션을 붙여주면 됩니다.

        ```java
        @FunctionalInterface
        interface MyLambdaFunction {
        	int max(int a, int b);
        }

        public class Lambda {
        	public static void main(String[] args) {
        		MyLambdaFunction lambdaFunction = (int a, int b) -> a > b ? a : b;
        		System.out.println(lambdaFunction.max(3, 5));
        	}
        }
        ```

    - 여기서 함수형 인터페이스에 여러개 함수를 선언하면 컴파일 에러가 발생합니다.
    - 자바에서 제공하는 함수형 인터페이스
        - Supplier<T>
            - 매개변수 없이 반환값만 갖는 함수형 인터페이스
            - `T get()`을 추상 메소드로 가지고 있음
        - Consumer<T>
            - 객체 T를 매개변수로 받으며 반환값이 없는 함수형 인터페이스이다.
            - `void accept(T t)`를 추상 메소드로 갖는다.
            - `andThen` 함수를 통해 연쇄적으로 Consumer를 이용할 수 있음
        - Function<T, R>
            - 객체 T를 매개변수로 받아 처리 후, R로 반환하는 함수형 인터페이스
            - `R apply(T t)`를 추상 메소드로 갖는다.
            - Consumer와 마찬가지로 andThen을 갖는다.
        - Predicate<T>
            - 객체 T를 매개변수로 받아 처리 후 boolean을 반환한다.
            - `Boolean test(T t)`을 추상 메소드로 갖는다.
</details>
<details><summary>Exception과 RuntimeException 차이 ★</summary>


![](/images/exception.png)

- Error
    - 시스템에 뭔가 비정상적인 상황이 발생했을 경우에 사용
        - 주로 VM에서 발생됨
    - 어플리케이션 코드에선 해당 에러를 Catch하려고 하면 안됨
    - 시스템 레벨에서 특별한 작업을 하는게 아니라면 애플리케이션에서는 Error 관련 처리를 하지 않아도 됨
- Exception(Checked Exception)
    - Checked Exception: Exception 클래스의 서브클래스이면서 RuntimeException 클래스를 상속하지 않은 클래스
    - Checked Exception가 발생할 수 있는 메소드를 사용할 경우 반드시 예외 처리하는 코드를 함께 작성해야함
    - catch 문으로 잡든지, throw를 정의해서 메소드 밖으로 던져야함(컴파일 에러 발생)
    - 대표적인 Exception
        - ClassNotFoundException: 존재하지 않는 클래스를 사용할려고 할 때 발생
        - InterruptedException: 인터럽트 되었을 때 발생한다.
        - NoSuchFieldException: 클래스가 명시한 필드를 포함하지 않을 때 발생한다.
        - NoSuchMethodException: 클래스가 명시한 메서드를 포함하지 않았을 때 발생한다.
        - IOException: 데이터 읽기 같은 입출력 문제가 있을 때 발생한다.
- RuntimeException(Uncheck Exception)
    - Unchecked Exception: RuntimeException 클래스와 그것의 서브클래스
    - try-catch 하지 않더라도 컴파일 가능
    - 왜 컴파일러는 RuntimeException을 확인하지 않을까?
        - 이 예외가 발생하는 경우는 보통 개발자의 실수에 의해 발생되는 예외들이 많음
            - ArithmeticException(0으로 나누기), IndexOutOfBoundsException(배열범위)
        - 이런 것들은 개발자가 조금만 더 신경쓰면 예외가 발생하지 않게 할 수 있기 때문에 컴파일러가 확인하지 않게 되었다고 볼 수 있음
    - 대표적인 RuntimeException
        - IllegalArgumentException: 매개변수가 의도하지 않는 상황을 유발할 때
        - IllegalStateException: 메서드를 호출하기 뒤한 상태가 아닐 때
        - NullPointerException: 변수 값이 null일 때 사용하는 경우
        - ArithmeticException: 산술적인 연산 오류가 있을 때
        - IndexOutOfBoundsException: 배열 범위를 벗어나서 참조할려고 할 때
- 예외 처리 방법
    - 예외 복구
        - 예외 상황을 파악하고 문제를 해결해서 정상상태로 돌려놓는 방법
        - 예외상황이 비쳐도 어플리케이션에서는 정상적으로 설계된 흐름을 따라 진행되어야함
    - 예외처리 회피
        - 예외 처리를 자신이 담당하지 않고 자신을 호출한 쪽으로 던져버리는 것

        ```java
        public void add() throws SQLException {
        	// JDBC API
        }
        ```

        - JdbcTemplate에서는 ResultSet이나 PrepareStatement 등을 이용해서 작업하다 발생하는 SQLException을 자신이 처리하지 않고 템플릿으로 던져버린다.
            - 콜백 오브젝트의 메서드는 모두 `throws SQLException`이 붙어있는데 Exception을 처리하는 일은 콜백 오브젝트의 역할이 아니라고 보기 때문
        - 예외를 회피하는 것은 예외를 복구하는 것보다 의도가 분명해야 한다.
    - 예외 전환
        - 예외 회피와 비슷하게 예외 메서드 밖으로 던지는 방법
        - 예외 회피와 다르게 좀 더 명확한 예외로 재정의하여 throws 한다.

            ```java
            public void add() throws DuplicateUserIdException, SQLException {
            	try {
            		// JDBC API
            	}	catch (SQLException e) {
            		if (e.getErrorCode() == MysqlErrorNumbers.ER_DUP_ENTRY) {
            			throws DuplicateUserIdException()
            		} else {
            			throws e;
            		}
            	}
            }
            ```

        - 중첩 예외를 만들어서 던지는 것이 좋음
            - 전환하려는 예외에 원래 예외를 담는 것
            - `throws DuplicateUserIdException(e)`
</details>
<details><summary>상속 관점에서 private 생성자는 어떤 효과가 있나?</summary>

- private 생성자는 A의 내부 메서드 혹은 Inner Class에서 호출할 수 있다.
- 부분 클래스가 부모의 생성자를 호출할 수 있기 때문에 이는 상속에 직접적인 영향을 끼침
- 클래스 A는 상속받을 수 있는데, 자기 자신이 아니면 부모의 내부 클래스에 의해서만 호출이 가능하다.
</details>
<details><summary>자바의 finally 블록은 try-catch-finally의 try 블록안에 return문을 넣어도 실행되는가?</summary>

- 실행됩니다. finally 블록은 try 블록이 종료되는 순간 실행된다.
- 다음과 같은 경우에서는 finally 블록이 실행되지 않는다.
    - try/catch 블록 수행 중에 가상 머신(Virtual Machine)이 종료됨
    - try/catch 수행하고 있던 스레드가 죽어버림
</details>
<details><summary>final, finally, finalize의 차이점은?</summary>

- final: 변수나 메서드 또는 클래스가 '변경 불가능'하도록 만든다.
- finally: try/catch 블록이 종료될 때 항상 실행도리 코드 블록을 정의하기 위해 사용됨
- finalize(): GC가 더 이상의 참조가 존재하지 않는 객체를 메모리에서 삭제하겠다고 결정하는 순간 호출되는 메서드.
</details>
<details><summary>Java의 제네릭 vs C++의 템플릿</summary>

- 제네릭과 템플릿은 List<String>처럼 코드를 작성할 수 있다는 이유에서 제네릭과 템플릿을 동일시 함
- 그리고 컴파일 단계에서 체크가 가능하다는 점
- 하지만 차이는 컴파일 이후에 발생하게 된다.
- 제네릭은 타입 제거라는 개념의 근거한다.
    - 소스코드를 JVM이 인식하는 바이트 코드로 변환할 때, 인자로 주어진 타입을 제거하는 기술이다.

        ```java
        // 바이트 변환 전
        Vector<String> vector = new Vector<String>();
        vector.add(new String("hello"));
        String str = vector.get(0);

        // 바이트 변환 후
        Vector vector = new Vector();
        vector.add(new String("hello"));
        String str = vector.get(0);
        ```

    - 제네릭이 있다고 해서 크게 달라지는 건 아님. 단지 코드를 좀 더 예쁘게 할 뿐
    - 그래서 자바 제네릭을 문법적 양념(syntatic sugar)라고 부름
- 반면 C++ 템플릿은 컴파일러가 인자로 주어진 각 타입에 대해 별도의 템플릿 코드를 생성한다.
    - 그래서 MyClass<Foo>와 MyClass<Bar>는 클래스 멤버 변수를 공유하지 않음
    - 하지만 MyClass<Foo>로 만들어진 두 객체는 클래스 멤버 변수를 공유함

        ```cpp
        template<class T> class MyClass {
        	public:
        		static int val;
        		MyClass(int v) { val = v; }
        };

        template<typename T>
        int MyClass<T>::bar;
        ```
</details>
<details><summary>TreeMap, HashMap, LinkedHashMap의 차이를 설명하세요.</summary>

- HashMap: 검색과 삽입에 O(1) 시간이 소요됨. 그렂미나 키를 기준으로 순회할 때 키의 순서는 무작위로 섞여있다. 그리고 클래스의 구현은 연결리스트로 이루어진 배열로 되어있음
- TreeMap: 검색과 삽입에 O(logN) 시간이 소요. 키는 정렬되어 있으므로 정렬된 순서로 키를 순회할 수 있음. 키는 반드시 Comparable 인터페이스를 구현하고 잇어야 함. 내부에 레드-블랙 트리로 구현되어 있음
- LinkedHashMap: 검색과 삽입에 O(1)이 소요. 키는 삽입한 순서대로 정렬되어 있고, 양방향 연결 버킷으로 구현되어있음.
- 보통은 HashMap을 사용함(일반적으로 빠르고 오버헤드가 적음). 삽입한 순서대로 키 정보를 얻고 싶으면 LinkedHashMap을 사용하고, 실제적인 순서대로 키 정보를 얻고 싶으면 TreeMap을 사용하면 됨.
</details>
<details><summary>싱글톤 설계 시 주의점</summary>

- 싱글톤은 클래스의 인스턴스를 단 한개만 생성하여 사용하는 패턴
- 처음 싱글톤 클래스를 호출한 시점에 인스턴스 생성한 후, 그 다음 호출부터는 생성한 인스턴스를 반환하는 방색
- 생성자를 private으로 선언하고 getInstance 함수를 통해 인스턴스 반환

```java
public class Singleton {
	public void run(int i) {
		Printer printer = Printer.getPrinter();
		printer.print("["+i+" 번째 객체 using " + printer.toString());
	}

	public static void main(String[] args) {
		Singletone[] singletons = new Singleton[5];
		for (int i = 0; i < singletons.length; i++) {
			singletons[i] = new Singletons();
			singletons[i].run(i+1);
		}
	}
}

class Printer {
	private static Printer printer = null;
	private Printer() {}  //외부에서 인스턴스를 생성하지 못하도록 private으로 선언

	// getPrinter를 통해 인스턴스 반환
	public static Printer getPrinter() {
		if (printer == null) {
			// getter를 호출한 시점에 인스턴스를 생성하는 걸 Lazy Initialize라고 함
			printer = new Printer();
		}
		return printer;
	}

	public void print(String str) {
		System.out.println(str);
	}
}
```

- 하지만 멀티스레드로 접근한다면 Printer 클래스가 여러개 생길 수 있다.
- 임의의 스레드가 getPrinter를 호출해서 printer가 null인걸 체크하고 생성하려는 순간 다른 쓰레드가 접근해서 printer null 체크한다.
- 그러면 또 생성자를 호출할 것이고 이게 다른 멀티 쓰레드에서도 동일하게 동작하게 되고, 결국에 다른 Printer 인스턴스가 할당될 것이다.

```java
public class JavaMainTest extends Thread {

    public JavaMainTest(String name) {
        super(name);
    }

    @Override
    public void run() {
        Printer printer = null;
        try {
            printer = Printer.getPrinter();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        printer.print("[" + Thread.currentThread().getName() + "] using " + printer.toString());
    }

    public static void main(String[] args) {
        JavaMainTest[] singletons = new JavaMainTest[5];
        for (int i = 0; i < singletons.length; i++) {
            singletons[i] = new JavaMainTest((i + 1) + "-thread");
            singletons[i].start();
        }
    }
}

class Printer {
    private static Printer printer = null;
    private Printer() {}

    public static Printer getPrinter() throws InterruptedException {
        if (printer == null) {
            Thread.sleep(1000);
            printer = new Printer();
        }
        return printer;
    }

    public void print(String str) {
        System.out.println(str);
    }
}
```

- Thread-safe한 싱글톤 패톤으로 설계해야한다
    - Eager Initializer
        - 정적 멤버 변수를 선언함과 동시에 생성하는 방법

            ```java
            public class Singleton {
            	private static Singletone instance = new Singleton();
            	private Singleton() {}
            	public static Singleton getInstance() {
            		return instance;
            	}
            }
            ```

        - 이른 초기화를 통해 클래스가 로딩하는 시점에 생성해버림
        - 가장 간단한 방법이지만 사용되지 않는 경우에도 인스턴스가 생성된다는 단점
    - Thread-safe singleton with **synchronized**

        ```java
        public class Singleton {
        	private static Singletone instance = null;

        	private Singleton() {}

        	public static synchronized Singleton getInstance() {
        		if (instance == null) {
        			instance = new Singleton();
        		}
        		return instance;
        	}
        }
        ```

        - synchronized 키워드를 이용하여 thread-safe한 싱글톤 패턴을 만들 수 있음
        - synchronized 키워드를 통해 getInstance은 동기화 블록이 됨
            - 하지만 synchronized 키워드는 성능을 매우 저하시킴
            - 그리고 그걸 getInstance 호출할 때마다 동기화 블록을 거쳐야 함 → 오버헤드가 큼
        - 안전하지만 개선할 필요가 있음
    - Double checked locking

        ```java
        public class Singleton {
        	private static Singletone instance = null;

        	private Singleton() {}

        	public static Singleton getInstance() {
        		if (instance == null) {
        			synchronized (Singleton.class) {
        				if (instance == null) {
        					instance = new Singleton();
        				}
        			}
        		}
        		return instance;
        	}
        }
        ```

        - synchronized 키워드를 사용한 double checked locking 방식입니다.
        - 생성 시점에만 동기화 블록을 지나게 되고, 동기화 블록 안에서 instance를 체크함
        - 불필요하게 동기화 블록으로 진입않도록 하여 좋은 성능을 낼 수 있도록 함
    - Initialization on demand holder idiom

        ```java
        public class Singleton {
        	private static Singletone instance = null;

        	private Singleton() {}

        	private static class LazyHolder {
        		public static final Singleton INSTANCE = new Singleton();
        	}

        	public static Singletone getInstance() {
        		return LazyHolder.INSTANCE;
        	}
        }
        ```

        - 내부 정적 클래스를 선언하고, 내부 클래스에서 인스턴스를 생성하도록 하는 방식
        - Singleton 클래스 자체에 인스턴스에 대한 어떤 선언도 없음
        - getInstance가 호출되기 전까지 초기화 되지 않음
        - LazyHoldeer 안에 INSTANCE는 final 키워드 때문에 변수가 재할당되지 않음
            - synchronized 키워드 없이 동시성 문제를 해결해주기 때문에 성능이 뛰어난 방법.
</details>
<details><summary>String, StringBuilder, StringBuffer 차이</summary>

- String
    - new 연산을 통해 생성된 인스턴스의 메모리 공간은 변하지 않는다.
    - GC로 제거해야한다.
    - 문자열 연산 시 새로 객체를 만드는 오버헤드가 발생

    객체가 불변하므로 멀티 스레드 환경에서 동기화를 신경쓸 필요가 없다.(조회 연산에서 매우 큰 장점)

- StringBuffer, StringBuilder
    - 공통점
        - new 연산으로 클래스를 한번만 만든다.
        - 문자열 연산 시 새로 객체를 만들지 않고, 크기를 변경시킨다.
        - StringBuffer와 StringBuilder는 클래스와 메소드가 동일
    - 차이점
        - StringBuffer는 Thread-safe하다
        - StringBuilder는 Thread-safe하지 않는다. 하지만 StringBuffer에 비해 성능은 좋기 때문에, 여러 스레드가 관여되지 않은 상황에선 StringBuilder를 사용하자.

</details>
<details><summary>OOP에 대해 설명해주세요</summary>

- 캡슐화: 객체가 내부적으로 기능을 어떻게 구현했는지 감추는 것
    - 구현에 사용된 데이터 상세 내용을 감춤
    - 캡슐화 하지 않으면? 변경이 연쇄적으로 퍼짐.
    - 캡슐화를 하면? 기능을 제공하고 구현 상세를 감춤
    - 캡슐화를 위한 2가지
        - Tell, Don't ask: 데이터를 달라고 하지말고 해달라고 하기
        - Demeter's Law: 메서드에서 생성한 객체의 메서드만 호출. 잘은 모르겠지만 조회메서드를 지양하는 것 같다. 결국 외부에 노출한거나 마찬가지니까
- 다형성과 추상화
    - 다형성: 여러 모습을 갖는 것
        - 모습: 타입, 객체지향 언어에선 타입상속으로 구현
    - 추상화: 데이터나 프로세스 등 **의미가 비슷한 개념**이나 **의미 있는 표현**으로 정의하는 과정
        - 타입 추상화
            - 여러 구현 클래스를 대표하는 상위 타입 도출
            - 추상타입은 구현은 감춤. 의도를 더 잘드러냄
        - 추상화 결과: 사용 대상의 변경 유연함
        - 변경될 때 추상화를 하라: 변경이나 확장이 발생할 때 추상화 하라!!
    - OCP(Open-Closed Principle): 확장에 열려있고 수정에는 닫혀있음
</details>
<details><summary>List, Set과 Map의 차이 ★</summary>

![](/images/list_set_map.jpeg)

- List
    - 순서가 있는 데이터들의 집합, 데이터 중복을 허용한다.
    - ArrayList
        - 상당히 빠르고 크기를 마음대로 조절할 수 있는 배열
        - 단방향 포인터 구조로 자료에 대한 순차적인 접근에 강점이 있음
    - Vector
        - ArrayList의 구형버전, 모든 메소드가 동기화 되어있음. 요샌 잘 안쓰임
    - LinkedList
        - 양방향 포인터 구조로 데이터의 삽입, 삭제가 빈번한 경우 빠른 성능을 보장
        - 스택, 큐, 양방향 큐 등을 만들기 위한 용도로 쓰임
- Set
    - 순서를 유지하지 않는 데이터의 집합. 데이터의 중복을 허용하지 않는다.
    - HashSet
        - 가장 빠른 임의 접근 속도. 순서를 전혀 예측할 수 없음
        - 내부적으로 HashMap을 사용함
    - LinkedHashSet
        - 추가된 순서, 또는 가장 최근에 접근한 순서대로 접근 가능
    - TreeSet
        - 정렬된 순서대로 보관하며 정렬 방법을 지정할 수 있음
- Map
    - Key와 Value의 쌍으로 이루어진 데이터의 집합. 순서는 유지되지 않고, Key는 중복을 허용하지 않지만 Value 중복은 허용
    - HashMap
        - Map 인터페이스를 구현하기 위해 해시테이블을 사용한 클래스
        - 중복을 허용하지 않고 순서를 보장하지 않음
        - 키, 값으로 null 허용
        - 내부 구조
            - O(1)에 접근해야하기 때문에 배열을 사용하고 있음
            - 이 때, 배열의 인덱스를 계산하기 위해 hashCode를 사용
            - 인덱스는 hashCode % (버킷수)를 통해 계산됨.
            - 디폴트로 hashmap에 16개 버킷이 세팅
            - 로드팩터를 넘어가면 버킷을 2배로 세팅
    - HashTable
        - HashMap 보다는 느리지만 동기화가 지원
        - 키, 값으로 null 허용 x
    - TreeMap
        - 이진검색트리의 형태로 키와 값의 쌍으로 이루어진 데이터를 저장
        - 정렬된 순서로 키/값 쌍을 저장하므로 빠른 검색이 가능
        - 저장시 정렬(오름차순)을 하기 때문에 저장시간이 다소 오래 걸림
    - LinkedHashMap
        - 기본적으로 HashMap을 상속받아 HashMap과 매우 흡사
        - Map에 있는 엔트리들의 연결 리스트를 유지되므로 입력한 순서대로 반복 가능
- [https://brunch.co.kr/@springboot/57](https://brunch.co.kr/@springboot/57)
</details>
<details><summary>자바 기본 자료형에 대해서 설명해주세요. ★</summary>

|자료형|형태|크기|표현가능 범위|
|----|----|----|-----------|
|boolean|참과 거짓|1|true, false|
|char|문자|2|'\u0000 ~ \uffff'(0 ~ 2^15 - 1)|
|byte|정수|1|-2^7 ~ 2^7 - 1(-128 ~ 127)|
|short|정수|2|-2^15 ~ 2^15 - 1(-32,768 ~ 32,767)|
|int|정수|4|-2^31 ~ 2^31 - 1 (-2,147,483,648 ~ 2,147,483,647)|
|long|정수|8|-2^63 ~ 2^63 - 1 (-9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807)|
|float|실수|4|±(1,40 x 10^-45 ~ 3.40 x 10^38)|
|double|실수|8|±(4.94 x 10^-324 ~ 1.79 x 10^308)|

- 기본형 타입을 제외한 타입들이 모두 참조형 타입(Reference Type)입니다.
    - 빈 객체를 의미하는 null이 존재함
    - 값이 저장되어 있는 곳의 주소값을 저장하는 공간으로 힙(Heap) 메모리에 저장됩니다.
    - 문법적으로는 에러가 없음(컴파일 통과!), 하지만 실행시켰을 때 런타임 에러가 발생(NullPointerException)

|자료형|예시|기본값|할당되는 메모리 크기|
|-----|---|-----|------------------|
|배열(Array)|`int[] arr = new int[5]`|null|4바이트|
|열거(Enum)|`enum { MON, TUE, ... }` |null|4바이트|
|클래스(Class)|`Student s = new Student();`|null|4바이트|
|인터페이스||null|4바이트|


- Wrapper Class
    - Primitive Type을 객체로서 다루기 위해 사용하는 클래스
    - primitive type을 감싼 객체라고 해서 포잠 객체(Boxing Object)라고 합니다.
    - 래퍼 클래스로 감싸고 있는 기본 타입 값은 외부에서 변경할 수 없습니다. 변경하고 싶으면 새로운 래퍼 클래스를 만들어 할당해야 합니다.

    |자료형|래퍼 클래스|
    |-----|----------|
    |boolean|Boolean|
    |char|Character|
    |byte|Byte|
    |short|Short|
    |int|Integer|
    |long|Long|
    |float|Float|
    |double|Double|

- 래퍼 클래스 구조도

    ![](/images/wrapper_struct.png)

    - Boolean, Character를 제외한 나머지 래퍼 클래스는 Number 클래스를 상속하고 있음
    - 그리고 모두 Object 클래스를 상속하고 있습니다.
- 문자열을 기본 타입 값으로 변환

    ```java
    public class WrapperEx {
        public static void main(String[] args) {
            String str1 = "10";
            String str2 = "10.5";
            String str3 = "true";

            byte b = Byte.parseByte(str1);
            int i = Integer.parseInt(str);
            short s = Short.parseShort(str);
            long l = Long.parseLong(str);
            float f = Float.parseFloat(str2);
            double d = Double.parseDouble(str2);
            boolean bool = Boolean.parseBoolean(str3);

            System.out.println("문자열 byte값 변환 : "+b);
            System.out.println("문자열 int값 변환 : "+i);
            System.out.println("문자열 short값 변환 : "+s);
            System.out.println("문자열 long값 변환 : "+l);
            System.out.println("문자열 float값 변환 : "+f);
            System.out.println("문자열 double값 변환 : "+d);
            System.out.println("문자열 boolean값 변환 : "+bool);
    	}
    }
    ```

- 값 비교

    ```java
    public class WrapperEx {
        public static void main(String[] args)  {
            Integer num = new Integer(10); //래퍼 클래스1
            Integer num2 = new Integer(10); //래퍼 클래스2
            int i = 10; //기본타입
    		 
            System.out.println("래퍼클래스 == 기본타입 : "+(num == i)); //true
            System.out.println("래퍼클래스.equals(기본타입) : "+num.equals(i)); //true
            System.out.println("래퍼클래스 == 래퍼클래스 : "+(num == num2)); //false
            System.out.println("래퍼클래스.equals(래퍼클래스) : "+num.equals(num2)); //true
        }
    }
    ```

    - 래퍼 클래스는 참조형 타입으로 취급됨
        - 따라서 `==` 비교는 객체간의 주소값을 비교하게됨 → 그래서 false를 반환
        - 값을 비교할려면 `equals` 메소드를 통해 비교
</details>
<details><summary>문자열 비교하기 `== vs. equals()`</summary>

- String을 생성하는 방법은 2가지 방법이 존재합니다.
    - 리터럴을 이용한 방식(`String s1 = "abcd";`)
    - new 연산자를 이용한 방식
- 리터럴을 사용하게 되면 string constant pool이라는 영역에 존재, new로 생성하면 Heap 영역에 존재
    - 리터럴로 선언할 경우 내부적으로 intern() 메서드를 호출.
    - intern() 메소드는 주어진 문자열이 string constant pool에 존재하는지 검색
        - 있으면 주소값을 반한
        - 없으면 string constant pool에 넣고 새로운 주소값을 반환
- ==: 객체의 주소값을 비교
- equals(): 값 비교

```java
public class Main {
	public static void main(String[] args) {
		String s1 = "abcd";
		String s2 = new String("abcd");
		System.out.println("s1 == s2 => " + (s1 == s2));  // false
		System.out.println("s1.equals(s2) => " + s1.equals(s2));  // true
	}
}
```

</details>
<details><summary>BigInteger</summary>

- long형의 값의 범위를 넘어서 저장할려고 할 때 사용
- 내부 안에 int array로 담아 넣음
- 반드시 String 타입으로 저장 ⇒ 자릿수 부족으로 인한 문제 해결
- 연산자가 아닌 메서드를 이용하여 사칙연산을 수행
    - add, subtract, multiply, divide
    - compareTo(this < val ⇒ -1, this == val ⇒ 0, this > val ⇒ 1), pow(지수)