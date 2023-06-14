# java-starter

## JDK
### Amazon corretto 설치  
> corretto 는 Amazon 에서 관리하는 OpenJDK 이다.   
> **macOS 에서 하위 버전의 corretto 설치**    
> `brew tap homebrew/cask-versions` 명령어를 치고 나서 `brew search corretto` 를 하면 안보이던 이전 버전의 corretto 들이 표시된다.  
> `brew install corretto8` 및 `brew install corretto11` 을 설치할 수 있다.  

### jps
> 유닉스의 명령어중 'ps'는 현재 실행되고 있는 프로세스들을 표시하는 명령어인데 `jps` 명령어는 현재 실행되고있는 JVM 프로세스를 표시해준다.  
> java 로 실행한 프로세스만 보이며, serivce 로 등록하여 실행한 프로세스는 보이지 않는다.  
> ```shell
> # 단순 실행
> jps
> 
> # 구동시 입력했던 JVM 파라미터도 함께 표시
> jps -v
> ```

### jstack
> 스레드 전체 덤프를 출력한다. JVM 내부에서 각 Thread 객체마다 Thread.getAllStackTraces, Thread.dumpStack()을 호출한 것과 동일하다. 
> 시스템에 따라 Hang이 발생할 수 있다. `-l` 옵션을 주면 잠금 세부 사항도 확인할 수 있다.  
> ```shell
> # 일반 사용
> sudo jstack <PID>
> 
> # 잠금 세부 사항 추가 확인
> sudo jstack -l <PID>
> ```
> Amazon EC2 사용 시 jre 만 설치되어 있는 경우 jstack 을 사용할 수 없다. 이 경우 JDK 를 설치하여 사용한다.  
> Amazon EC2 에 JDK 1.8 버전 설치: `sudo yum install -y java-1.8.0-openjdk-devel.x86_64`  
> 
> 스레드 덤프에는 다음의 정보가 들어 있다.  
> ```shell
> "<Thread name>" #<ID> prio=<Thread Priority> tid=<Java-level Thread ID> nid=<Native Thread ID> <Thread State> [<Last Known Java Stack Pointer>]
> java.lang.Thread.State: <Thread State>
> Call stack
> ```
> 
> **Thread Name**  
> 스레드 이름이며, 이름을 변경하여 사용하는 경우 스레드 덤프에도 반영된다. 일반적으로 스레드 덤프를 해석하기 쉽게 의미 있는 이름으로 설정한다.
> 
> **ID**  
> JVM 내 의 각 스레드에 할당된 고유 ID이다. 1부터 시작한다.
> 
> **Thread Priority**  
> Java 스레드의 우선순위이다.
> 
> **OS Thread Priority**  
> 자바의 스레드는 운영체제(OS)의 스레드와 매핑이 되는데, 매핑된 운영체제 스레드의 우선순위이다.
> 
> **Java-Level Thread ID**  
> JVM 내부(JNI 코드)에서 관리하는 Native Thread 구조체의 포인터 주소이다.
> 
> **Native Thread ID**  
> 자바 스레드에 매핑된 OS 스레드의 ID이다.  
> Windows에서는 OS Level의 스레드 ID이며, Linux에서는 LWP(Light Weight Process)의 ID를 의미한다.
> 
> **Thread State**  
> 스레드의 상태이다.
> 
> **Last Known Java Stack Pointer**  
> 스레드의 현재 Stack Pointer(SP)의 주소를 의미한다.  
> 스택 포인터는 스택에서 다음 데이터 갈 들어갈 영역을 뜻한다.  
> 
> **Call Stack**  
> 현재 스레드가 수행되는 함수들의 호출 관계(콜 스택)를 표현한다.

### 스레드 상태
> **NEW**  
> 스레드가 생성되었지만 아직 실행되지 않은 상태
> 
> **RUNNABLE**  
> 현재 CPU를 점유하고 작업을 수행 중인 상태. 운영체제의 자원 분배로 인해 WAITING 상태가 될 수도 있다.  
> 
> **BLOCKED**  
> Monitor를 획득하기 위해 다른 스레드가 락을 해제하기를 기다리는 상태  
> 
> **WAITING**  
> wait() 메서드, join() 메서드, park() 메서드 등를 이용해 대기하고 있는 상태  
> 
> **TIMED_WAITING**  
> sleep() 메서드, wait() 메서드, join() 메서드, park() 메서드 등을 이용해 대기하고 있는 상태.  
> WAITING 상태와의 차이점은 메서드의 인수로 최대 대기 시간을 명시할 수 있어 외부적인 변화뿐만 아니라 시간에 의해서도 WAITING 상태가 해제될 수 있다는 것이다.  
> 

### jmap
> jmap명령어는 jvm의 맵을 보여주는 기능을 제공하는 기본 분석 도구이다. 따라서 자바 힙 메모리의 정보를 얻거나 메모리 dump를 떠서 분석을 할 수 있다.  
> stop-the-world 를 발생시키므로 서비스 중인 프로세스에는 반드시 필요한 상황에서만 사용해야 한다.  
> ```shell
> jmap -dump:format=b,file=<파일명>.hprof <PID>
> ```

### VisualVM
> jmap 으로 heap 덤프를 뜬 .hprof 파일을 열어서 상세 정보를 보여주는 프로그램.  
> **설치**  
> macOS: `brew install --cask visualvm`  

### 참조사이트
> [[Java] JVM Thread Dump 분석하기](https://steady-coding.tistory.com/597)

---

## OOP
### 정적 팩토리 메서드(Static Factory Method)
> 생성자에 비해 갖는 장점  
> 1.이름을 가질 수 있다.  
> 2.객체 생성을 캡슐화할 수 있다.  
> 
> **내 의견**  
> 단순 데이터만 같은 객체의 경우에는 정적 팩토리 메서드가 갖는 이점이 없다. 
> 비지니스 로직이 없는 경우에는 생성자가 갖는 매개변수와 정적 팩토리 메서드가 갖는 매개변수의 차이가 없다.  
> 비지니스 로직을 갖는 객체의 경우 의미가 있을 수 있을 듯하다.  
> 예를 들어서 OrderItem(주문 아이템) 의 경우 정적 팩토리 메서드인 of(Item item, ...) 을 사용하면 정적 팩토리 메서드 안에서 item.removeStock(count) 와 같이 
> 단순히 데이터 저장 뿐 아니라 객체와의 관계에서 행해지는 비지니스 로직이 있는 경우에 의미가 있다.
> 
> 일단 생성자의 매개변수에 해당 객체가 가지는 멤버 변수 외의 요소는 지양한다고 가정했을 경우 DTO 객체를 생성자 대신에 from 메서드를 사용해서 만드는 것도 의미가 있을 듯 하다.
> 예시로 CarReadDto 를 생성할 때 Car 를 통해서 만들려는 경우 `CarReadDto dto = new CarReadDto(...)` 대신에 `CarReadDto dto = CarReadDto.from(car)` 로 만드는 것이
> 코드를 이해하는데 도움이 될 것으로 생각된다.
> 
> **네이밍 컨벤션**  
> `from`: 하나의 매개 변수를 받아서 객체를 생성  
> `of`: 여러개의 매개 변수를 받아서 객체를 생성  
> `getInstance` 혹은 `instance`: 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음. 보통 싱글톤 객체에서 많이 사용.   
> 
> 참조사이트: [정적 팩토리 메서드(Static Factory Method)는 왜 사용할까?](https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/)

---

## Java 8
### 지원(Support) 일정
> Amazon Corretto 8: 2026년 5월까지  
> 참조사이트: [Amazon Corretto 8 및 11 지원 연장](https://aws.amazon.com/ko/about-aws/whats-new/2020/08/amazon-corretto-8-11-support-extended/)
> 
> OpenJDK 8  
> Active Support: [만료됨] 2022년 3월 31일까지  
> Security Support: 2030년 12월 31일까지  
> 참조사이트: https://endoflife.date/java

### Interface 
> default 및 static 메서드 기능 제공
> ```java
> interface Java8Interface {
>   default String defaultMethod() {
>       return "default method";
>   }
> 
>   static String staticMethod() {
>       return "static method";
>   }
> }
> ```
> default 메서드: 구현 클래스에서 재정의가 가능하다. 구현 클래스에서 Override 하지 않더라도 기본적인 기능을
> 사용할 수 있도록 만들기 위해서 추가되었다.  
> static 메서드: 구현 클래스에서 재정의가 불가능하다.  

### @FunctionalInterface
> 인터페이스에 default 및 static 메서드를 제외하고 구현이 필요한 메서드가 1개인 인터페이스.  
> 애노테이션에 특별한 기능은 없고 이 인터페이스의 메서드가 익명 함수로 사용된다는 것을 표시한 것이다.  
> 
> 함수형 인터페이스를 사용하기 보다는 Java 에서는 다음 문법을 제공하여 함수형 인터페이스를 대체한다.  
> Predicate(필터링): T -> boolean  
> Consumer(소비): T -> void  
> Supplier(공급): () -> T  
> Function<T, R>(map): T -> R  
> Comparator(sort): (T, T) -> int  
> Runnable: () -> void  
> Callable: () -> T  

### 람다 표현식
> 익명 함수의 표현식. Java 8 이전에는 익명함수를 사용하기 위해서는 인터페이스를 구현하는 익명 클래스를 익명 함수로 사용하였었다.
> Java 는 함수만 따로 존재하지 않고 클래스가 있어야함으로 그러하다.  
> 하지만 Java 8 은 메서드를 1개만 가진 인터페이스를 다음과 같은 형태로 선언이 가능하다.  
> ```java
> Function<Int, String> func = (intValue) -> String.valueOf(intValue);
> ```

### Stream API 
> **합계 구하기**    
> ```java
> // Stream 의 reduce 이용
> Integer sum1 = Arrays.asList(1, 2, 3, 4, ,5).stream().reduce(0, Integer:sum);
> 
> // IntStream 의 sum 이용
> int sum2 = Arrays.asList(1, 2, 3, 4, ,5).stream().mapToInt(i -> i).sum();
>```

### LocalDate, LocalDateTime, ZonedDateTime
> java 8 에서 새로 추가된 날짜 클래스들.  
> 
> 현재 시간 구하기
> ```java
> // 시스템 타임존의 현재 시간
> LocalDateTime now1 = LocalDateTime.now();  
> ZonedDateTime now2 = ZonedDateTime.now();   
> 
> // 직접 정한 타임존의 현재 시간
> LocalDateTime now3 = LocalDateTime.now(ZoneId.of("UTC"));  
> ZonedDateTime now4 = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));    
> ```
> 날짜 변환하기
>   
> ```java
> public static final ZoneId ZONE_KST = ZoneId.of("Asia/Seoul");
> public static final ZoneId ZONE_UTC = ZoneId.of("UTC");
> 
> // UTC LocalDateTime -> KST ZonedDateTime 변환
> LocalDateTime nowUTC = LocalDateTime.now(ZONE_UTC);
> ZonedDateTime convertedKST = ZonedDateTime.of(nowUTC, ZONE_UTC)
>                .withZoneSameInstant(ZONE_KST);
> 
> // KST ZonedDateTime -> UTC LocalDateTime 변환
> ZonedDateTime nowKST = ZonedDateTime.now(ZONE_KST);
> LocalDateTime convertedUTC = nowKST
>                .withZoneSameInstant(ZONE_UTC)
>                .toLocalDateTime();
> 
> // timestamp millisecond -> UTC LocalDateTime 변환
> long nowTimestamp = System.currentTimeMillis(); 
> LocalDateTime timestampUTC = LocalDateTime.ofInstant(Instant.ofEpochMilli(nowTimestamp), ZONE_UTC);
> ```

### Optional
> 객체는 null 값을 가질 수 있다. Optional 객체는 null 값을 가질 수도 있는 객체를 감싸서 해당 객체가 null 인지 확인 및 그에 따른 처리를 깔끔하게 도와준다.  
> ```java
>     ...
>     Optional<String> stringOpt = getString("optional");
>     stringOpt.ifPresent(str -> System.out.println("str = " + str));
>     System.out.println("stringOpt.isPresent = " + stringOpt.isPresent());
>     System.out.println("stringOpt.isEmpty = " + stringOpt.isEmpty());
>       
>     String str = getString(null)
>             .orElse("orElseGet");
>     System.out.println("str = " + str);
> }
> 
> private static Optional<String> getString(String str) {
>     return Optional.ofNullable(str);
> }
> ```

### 배열 정렬의 병렬 처리
> 배열 정렬 시 Arrays.sort() 대신 `Arrays.parallelSort()` 를 통해서 멀티스레드로 정렬을 처리할 수 있다.  
> 배열의 갯수가 약 5000개 정도부터 Arrays.parallelSort() 가 더 빠르기 때문에 그 이하인 경우 Arrays.sort() 를 사용한다.    

### 참조사이트
> [[Java] Java 8 vs Java 11](https://steady-coding.tistory.com/598)

---

## Java 9
### TODO
> 

---

## Java 10
### TODO
>

---


## Java 11
### 지원(Support) 일정
> Amazon Corretto 11: 2027년 9월까지  
> 참조사이트: [Amazon Corretto 8 및 11 지원 연장](https://aws.amazon.com/ko/about-aws/whats-new/2020/08/amazon-corretto-8-11-support-extended/)
> 
> OpenJDK 11  
> Active Support: 2023년 11월 30일까지  
> Security Support: 2026년 11월 30일까지  
> 참조사이트: https://endoflife.date/java

### String 클래스에 새로운 메서드 추가
> `strip()`: 문자열 앞뒤 공백 제거  
> `stripLeading()`: 문자열 앞의 공백 제거  
> `stripTrailing()`: 문자열 뒤의 공백 제거  
> `isBlank()`: 문자열이 비어있거나, 공백만 포함되어 있을 경우 true 를 반환. - String.trim().isEmpty() 와 결과가 동일함.  
> `repeat(n)`: n개 만큼 문자열을 반복해서 붙여서 반환함.  

### java.nio.file.Files 클래스에 새로운 메서드 추가
> `Path writeString(Path, String, Charset, OpenOption)`  
> 파일에 문자열을 작성하고 Path로 반환한다. 파일 오픈 옵션에 따라 작동 방식을 달리하며, charset을 지정하지 않으면 UTF-8이 사용된다.
> 
> `String readString(Path, Charset)`  
> 파일 전체 내용을 읽어서 String으로 반환하고, 파일 내용을 모두 읽거나 예외가 발생하면 알아서 close를 한다. 
> charset을 지정하지 않으면 UTF-8이 사용된다.
>
> `boolean isSameFile(Path, Path)`  
> 두 Path가 같은 파일을 가리키며, true, 아니면 false를 반환한다.

### 컬렉션 인터페이스에 새로운 메서드 추가  
> 컬렉션(List, Set, Map 등)의 toArray() 메서드를 통해 원하는 타입의 배열을 선택하여 반환할 수 있음.  
> ```java
> List sampleList = Arrays.asList("Java", "Kotlin"); 
> String[] sampleArray = sampleList.toArray(String[]::new);
> assertThat(sampleArray).containsExactly("Java", "Kotlin");
> ```

### Predicate 인터페이스에 새로운 메서드 추가
> Predicate 인터페이스에 부정을 나타내는 not() 메소드가 추가되었다.  
> ```java
> List<String> sampleList = Arrays.asList("Java", "\n \n", "Kotlin", " ");
> List withoutBlanks = sampleList.stream()
>                                .filter(Predicate.not(String::isBlank))
>                                .collect(Collectors.toList());
> assertThat(withoutBlanks).containsExactly("Java", "Kotlin");
> ```

### 람다에서 로컬 변수 Var 사용
> 람다식에서 var 를 사용할 수 있게 되었다. var 사용 시 람다식의 매개변수에 @Nonnull 같은 애노테이션을 붙일 수 있다.  
> ```java
> List<String> sampleList = Arrays.asList("Java", "Kotlin");
> String resultString = sampleList.stream()
>                                 .map((@Nonnull var x) -> x.toUpperCase())
>                                 .collect(Collectors.joining(", "));
> assertThat(resultString).isEqualTo("JAVA, KOTLIN");
> ```

### 자바 파일 실행
> javac를 통해 컴파일 하지 않고도, 바로 java 파일을 실행할 수 있게 되었다.
> ```shell
> // Java 11 이전
> $ javac HelloWorld.java
> $ java Helloworld
> Hello Java 8!
> 
> // Java 11 이후
> $ java HelloWorld.java
> Hello Java 11!
> ```

### Garbage Collector
> Java 8 에서 사용하던 GC 인 Paralle GC 에서 변경되어 Java 11의 Default GC는 `G1 GC` 이다.

---

## Async
### 동기/비동기
> **동기식 처리(Synchronous)**  
> 동기식 처리 모델(Synchronous processing model)은 직렬적으로 태스크(task)를 수행한다.  
> 즉, 태스크는 순차적으로 실행되며 어떤 작업이 수행 중이면 다음 작업은 대기하게 된다.  
> 
> **비동기식 처리 (Asynchronous)**  
> 비동기식 처리 모델(Asynchronous processing model 또는 Non-Blocking processing model)은 병렬적으로 태스크를 수행한다.
> 즉, 태스크가 종료되지 않은 상태라 하더라도 대기하지 않고 다음 태스크를 실행한다.

### Executor
> Thread, Runnable과 같은 low level API를 프로그래머가 직접 다루는 것은 현실적으로 어렵다. (수십,수백 개 쓰레드의 자원을 관리하려면..머리가 꽤 아플 것이다.)  
> 그래서 쓰레드를 만들고 관리하는 작업을 애플리케이션에서 분리하여 high level API인 'Executor'에 위임하였다.  
> 주요 인터페이스로는, 1) Executor, 2) ExecutorService, 3) ScheduledExecutorService 가 있다.  
> 
> **Executor**  
> ```java
> Executor executor = Executors.newSingleThreadExecutor(); // single thread
> executor.execute(() -> System.out.println("Thread: " + Thread.currentThread().getName()));
> ```
> Executor는 쓰레드를 생성하고 처리하는 인터페이스이다.
> execute()라는 메서드만을 가지고 있는데, 쓰레드를 처리할 수는 있지만 종료할 수는 없다. idle 에서 실행시켜보면 강제종료하기 전까지 계속 실행되는 것을 확인할 수 있다.  
> 
> **ExecutorService**  
> ```java
> ExecutorService executorService = Executors.newSingleThreadExecutor();
> executorService.execute(() -> System.out.println("Thread : " + Thread.currentThread().getName()));
> executorService.shutdown();
> 
> ExecutorService executorService2 = Executors.newSingleThreadExecutor();
> executorService2.submit(() -> {
>     try {
>         Thread.sleep(30000);
>         System.out.println("Thread2 : " + Thread.currentThread().getName());
>     } catch (InterruptedException e) {
>         System.out.println("Call shutdownNow!");
>         e.printStackTrace();
>     }
> });
> executorService2.shutdownNow();
> ```
> ExecutorService는 Executor를 상속 받은 인터페이스이다. 쓰레드를 생성하고 처리하고 종료하는 등의 작업을 할 수 있다.  
> submit()을 사용하면 작업을 ExecutorService가 만든 쓰레드풀에서 처리하고, shutdown()으로 쓰레드 풀을 종료할 수 있다.  
> shutdown()을 사용하면 작업이 다 종료될 때까지 기다리고 종료하지만, 작업이 끝나든 말든 지금 당장 종료하고 싶다면 shutdownNow()를 사용하면 된다.  
>   
> ExecutorService는 Executor를 상속 받았기 때문에 execute()와 submit() 모두 호출이 가능하다.  
> 다만, 두 메서드는 받을 수 있는 인자 타입에 차이가 있다. execute()는 Runnable 인터페이스만 인자로 받을 수 있지만, 
> submit()은 Runnable과 Callable 인터페이스 모두 인자로 받을 수 있다.  
> 또한, 두 메서드는 리턴 타입도 다르다. execute()는 반환 값이 없는 반면, submit()은 Future 객체를 반환한다.  
> 
> **Callable**  
> Runnable은 반환 값이 void 이기 때문에, 작업을 처리하는 것 뿐 작업 결과를 리턴할 수 없었다.  
> Callable은 Runnable과 유사하지만, 작업의 결과(Future 객체)를 받을 수 있다는 차이가 있다.  
> ```java
> public class Main {
>     public static void main(String[] args) throws ExecutionException, InterruptedException {
>         ExecutorService executorService = Executors.newSingleThreadExecutor();
> 
>         Callable<String> hello = () -> {
>           Thread.sleep(2000);
>           return "hello";
>         };
> 
>         Future<String> result = executorService.submit(hello);
>         
>         System.out.println("Started!");
>         result.get(); // 블로킹
>         System.out.println("End!");
>         executorService.shutdown();
>     }
> }
> ```

### Future
> Future는 비동기적인 연산의 결과를 표현하는 클래스입니다. Future를 이용하면 멀티쓰레드 환경에서 처리된 어떤 데이터를 다른 쓰레드에 전달할 수 있습니다.  
> Future 내부적으로 Thread-Safe 하도록 구현되었기 때문에 synchronized block을 사용하지 않아도 됩니다.  
> Future 는 저수준의 스레드에 비해 직관적으로 이해하기 쉽다는 장점이 있다.  
> Future를 이용하려면 시간이 오래걸리는 작업을 Callable 객체 내부로 감산 다음에 ExecutorService에 제출해야 한다.  
> 
> ```java
> ExecutorService executor = Executors.newCachedThreadPool();
> Future<Double> future = executor.submit(() -> doSomeLongComputation());   // 시간이 오래걸리는 Task
> doSomethingElse(); // 비동기 작업을 수행하는 동안 다른 작업을 수행.
> try {
>     Double result = future.get(1,TimeUnit.SECONDS);
> } catch(ExecutionException ee){
>     // 계산 중 예외 발생
> } catch (InterruptedException ie){
>     // 현재 스레드에서 대기 중 인터럽트 발생
> } catch (TimeoutException te){
>     // Future가 완료되기 전에 타임아웃 발생
> }
> ```

### CompatableFuture
> **Future 제한**  
> Future 인터페이스가 비동기 계산이 끝났는지 확인할 수 있는 isDone 메소드, 계산이 끝나길 기다리는 메소드, 결과 회수 메소드 등을 제공한다.  
> 하지만 이들 메서드만으로 간결한 동시 실행코드를 구현하기에는 충분하지 않다.  
> 예를들어 여러 Future의 결과가 있을 때 이들의 의존성을 표현하기가 어렵다.
> 즉, 오래걸리는 A라는 계산이 끝나면 그 결과를 다른 오래 걸리는 계산 B로 전달하시오. 
> 그리고 B의 결과가 나오면 다른 질의의 결과와 B의 결과를 조합하시오. 와 같은 요구사항을 Future 에서는 쉽게 구현할 수 없다.  
> 
> **Java 8 CompletableFuture**  
> Java 8 에서 제공하는 CompletableFuture 클래스는 다음 기능을 제공한다.  
> * 두 개의 비동기 계산 결과를 하나로 합친다. 두 가지 계산 결과는 서로 독립적일 수도 있으며 또는 두 번째 결과가 첫 번째 결과에 의존하는 상황일 수 있다.
> * Future 집합이 실행하는 모든 태스크의 완료를 기다린다.
> * Future 집합에서 가장 빨리 완료되는 태스크를 기다렸다가 결과를 얻는다.
> * 프로그램적으로 Future를 완료시킨다.
> * Future 완료 동작에 반응한다.(즉, 결과를 기다리면서 블록되지 않고 결과가 준비되었다는 알림을 받은 다음에 Future의 결과로 원하는 추가동작을 수행할 수 있음)
> 
> 비동기 API 에서는 메소드가 즉시 반환되며, 끝내지 못한 나머지 작업을 호출자 스레드와 동기적으로 실행될 수 있도록 다른 스레드에 할당한다. 
> 이와같은 비동기 API를 사용하는 상황을 비블록 호출이라고 한다. 
> 다른 스레드에 할당된 나머지 계산 결과는 콜백 메소드를 호출해서 전달하거나 호출자가 계산결과를 끝날 때까지 기다림 메소드를 추가로 호출하면서 전달된다.
> 
> 비동기 메서드 예시
> ```java
> public Future<Double> getPriceAsync(String product){
>     CompletableFuture<Double> futurePrice = new CompletableFuture<>();
> 
>     new Thread(()->{
>         double price = calculatePrice(product); // 다른 스레드에서 비동기적으로 계산 수행.
>         futurePrice.complete(price);  // 오랜 시간이 걸리는 계산이 완료되면 Future에 값을 설정한다. JS 의 Promise 를 사용했을 때와 유사함을 확인할 수 있다.
>     }).start();
> 
>     return futurePrice;
> }
> ```
> 
> 비동기 메서드 사용 예시
> ```java
> Future<Double> futurePrice = shop.getPriceAsync("my favorite product");
> 
> // 제품의 가격을계산하는 동안 다른작업을 수행
> doSomethingElse();
> 
> //다른 상점 검색 등 다른 작업 수행
> try{
>     double price = futurePrice.get();
> } catch (Exception e){
>     throw new RuntimeException(e);
> }
> ```
> **에러처리 방법**  
> 지금까지 개발한 코드는 아무 문제없이 작동한다. 그런데 가격을 계산하는 동안 에러가 발생하면 어떻게 될까? 아마 이상한 일이 벌어질 것이다. 
> 예외가 발생하면 해당 스레드에만 영향을 미친다. 즉 에러가 발생해도 가격 계산은 계속 진행되며 일의 순서가 꼬인다. 
> 결과적으로 클라이언트는 get 메소드가 반환될 때까지 영원히 기다리게될 수도 있다.  
> 클라이언트는 타임아웃값을 받는 get 메소드의 오버로드 버전을 만들어 이 문제를 해결할 수 있다. 
> 이처럼 블록 문제가 발생할 수 있는 상황에서는 타임아웃을 활용하는것이 좋다. 
> 그래야 문제가 발생했을 때 클라이언트가 영원히 블록되지 않고, 타임아웃 시간이 지나면 TimeoutException을 받을 수 있다. 
> 하지만 이때 제품 가격 계산에 왜 에러가 발생했는지 알 수 있는 방법이 없다. 따라서 completableExceptionally 메소드를 이용하여 
> CompletableFuture 내부에서 발생한 예외를 클라이언트로 전달해야 한다.  
> ```java
> public Future<Double> getPriceAsync(String product) {  
>     CompletableFuture<Double> futurePrice = new CompletableFuture<>();  
>     new Thread(()-> {    
>         try{ 
>            double price = calculatePrice(product);      
>            futurePrice.complete(price);    
>         } catch (Exception ex) {
>             futurePrice.completeExceptionally(ex);    
>         }  
>     }).start();  
>     return futurePrice;
> }
> ```
> 
> **팩토리 메소드 supplyAsync로 Completable Future 만들기**  
> 지금까지 CompletableFuture를 직접 만들었다. 하지만 좀 더 간단하게 CompletableFuture를 만드는 방법도 있다. 
> 예를 들어 getPriceAsync 메소드를 다음처럼 간단하게 한행으로 구현할 수도 있다.  
> ```java
> public Future<Double> getPriceAsync(String product) {  
>     return CompletableFuture.supplyAsync(()-> calculatePrice(product));
> }
> ```

### 참조사이트
> [[Java]동기와 비동기 방식(Asynchronous processing model)](https://webheck.tistory.com/entry/Java%EB%8F%99%EA%B8%B0%EC%99%80-%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%B0%A9%EC%8B%9DAsynchronous-processing-model)  
> [Java - Future 사용 방법](https://codechacha.com/ko/java-future/)  
> [JAVA 비동기 프로그래밍: CompletableFuture](https://velog.io/@suyeon-jin/JAVA-CompletableFuture)  
> 

---

## JVM 튜닝
### 메모리 설정
> **-Xms**  
> -Xms이 설정은 Java 힙의 초기 크기를 제어합니다. 이 매개변수를 적절하게 조정하면 가비지 콜렉션의 오버헤드를 줄여서 서버 응답 시간 및 처리량을 개선합니다. 
> 일부 응용프로그램의 경우, 이 옵션에 대한 기본 설정이 너무 낮아서 사소한 가비지 콜렉션의 수가 높아질 수 있습니다.  
> 기본값 : 50MB. 이 기본값은 32비트 및 64비트 구성 모두에 적용됩니다.  
> 권장 : 워크로드에 특정하지만, 기본값보다 높습니다.  
> 사용법 : -Xms256m은 초기 힙 크기를 256MB로 설정합니다.  
> 
> **-Xmx**  
> -Xmx이 설정은 Java 힙의 최대 크기를 제어합니다. 이 매개변수를 늘리면 Application Server에 사용 가능한 메모리가 늘어나고 가비지 콜렉션 빈도가 줄어듭니다. 
> 이 설정을 늘리면 서버 응답 시간 및 처리량이 개선될 수 있습니다. 그러나 이 설정을 늘리면 가비지 콜렉션이 발생할 때 해당 콜렉션의 지속 기간이 늘어납니다. 
> 이 설정은 Application Server 인스턴스에 대해 사용 가능한 시스템 메모리 이상으로 증가해서는 안됩니다. 
> 설정을 사용 가능한 시스템 메모리 이상으로 늘리면 시스템 페이징 및 상당한 성능 감소를 유발할 수 있습니다.  
> 기본값 : 256MB. 이 기본값은 32비트 및 64비트 구성 모두에 적용됩니다.  
> 권장 : 워크로드에 특정하지만, 사용 가능한 실제 메모리의 양에 따라서 기본값보다 높습니다.  
> 사용법 : -Xmx512m은 최대 힙 크기를 512MB로 설정합니다.  
>
> **-XX:NewRatio**  
> -XX:NewRatio 옵션을 이용해 전체 힙 크기 중 New 크기의 비율을 지정하거나 XX:NewSize 옵션을 사용해 원하는 크기만큼의 New 영역 크기를 지정하는 것이 좋다. 
> 대부분의 객체는 생존 시간이 길지 않기 때문에 New 영역 크기 지정이 중요해진다. 웹 애플리케이션에서 캐시 데이터를 제외한 대부분의 객체는 HttpRequest 에 대한 
> HttpResponse 가 만들어지는 시간에 생성된다. 보통 이 시간은 1초를 넘지 않기 때문에 객체의 생존 시간도 1초가 되지 않는다.  
> 만약 New 영역의 크기가 크지 않다면 새로 생성되는 객체의 자리를 위해 Old 영역으로 이동돼야 하고 Old 영역에 대한 GC 비용은 New 영역에 대한 GC 비용보다 
> 상당히 크기 때문에 충분한 New 영역의 크기를 잡아줘야 한다.
> 
> 다만 일정 수치 이상으로 New 영역의 크기가 커지면 응답 반응성이 떨어지는 문제가 발생할 수 있으므로 주의하자. 
> New 영역에 대한 GC는 기본적으로 어느 한 Survivor 영역에서 다른 Survivor 영역으로 복사하는 것이기 때문이다. 
> 또한 Old 영역뿐만 아니라 New 영역에 대한 GC를 할 때에도 stop-the-world 현상은 발생한다. New 영역이 커지면 상대적으로 Survivor 영역의 크기도 커져 
> 그만큼 복사해야 할 데이터의 크기도 늘어난다. 이런 특성을 감안해 New 영역의 크기를 정할 때는 HotSpot JVM 의 OS별 NewRatio 를 참고하는 것이 좋다.
> 
> |OS & option	| 디폴트 -XX:NewRatio |
> |------------|------------------|
> | Sparc-server	| 2                |
> | Sparc-client	| 8                |
> | x86-server	| 8 -> 2~3         |
> | x86-client	| 12               |
> 
> NewRatio를 지정하면 전체 힙 크기 중에서 1/(NewRatio+1) 만큼이 New 영역의 크기가 된다.
> Sparc-server의 NewRatio가 유독 작은 것을 알 수 있는데 기본값을 정하던 당시 x86보다 Sparc 시스템을 하이엔드 용도로 사용했기 때문이다. 
> 요즘은 x86 서버 사용이 흔해졌고 성능 또한 향상됐기 때문에 Sparc-server 에 준하는 값인 2 또는 3 정도를 지정하는 것이 좋다.
> 
> **-Xms 와 -Xmx 를 같게 하는 이유**  
> 1.힙 크기를 늘릴 때 OS 에 추가 메모리를 요청해야 하므로 시간이 걸린다 (GC 가 처리되었던 요청의 응답시간에 추가됨)  
> 2.JVM 은 힙으로 늘린 메모리를 다시 해제하지 않아 결국엔 Xmx 값이 된다  
> 3.힙 크기를 늘리는 작업은 stop-the-world 이벤트여서 이를 방지할 수 있다  
> 4.Xms 가 작게 시작하는 경우 GC 가 더 많이 발생하게 됨  
> 5.일반적으로 다른 앱과 메모리 경쟁을 하지 않는 경우에 이렇게 설정  
> 
> **참조사이트**  
> [JAVA -Xms -Xmx 등 메모리 설정](https://velog.io/@bbkyoo/JAVA-Xms-Xmx-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%84%A4%EC%A0%95)  
> [[java] JVM 옵션 -Xms 와 -Xmx 를 같게 하는 이유](https://kimxavi.tistory.com/entry/java-JVM-%EC%98%B5%EC%85%98-Xms-%EC%99%80-Xmx-%EB%A5%BC-%EA%B0%99%EA%B2%8C-%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0?category=857470)  

### 서버 관련 설정
> `-Djava.awt.headless=true`  
> 서버 환경이나 GUI를 사용하지 않는 환경에서 Java 애플리케이션을 실행할 때 유용합니다. 
> 이 옵션을 사용하면 그래픽 관련 예외를 방지하고, 그래픽 디스플레이 없이도 Java AWT 및 Swing 기능을 안전하게 사용할 수 있습니다.  
> 
> `-Dfile.encoding=UTF-8`  
> JVM이 파일을 읽거나 쓸 때, 파일의 인코딩을 UTF-8로 가정합니다. UTF-8로 인코딩된 텍스트 파일을 읽을 때, 파일의 내용을 올바르게 유니코드 문자로 해석할 수 있습니다.
> JVM이 문자열을 다룰 때, 문자열의 인코딩을 UTF-8로 가정합니다.  
> 
> `-server`  
> JVM의 실행 모드를 서버 모드로 설정하는 옵션입니다. 이 옵션을 사용하면 JVM이 서버 환경에서 최적화된 기능을 사용하여 애플리케이션을 실행합니다.  
> `-server` 옵션을 사용하는 경우 JVM은 다음과 같은 최적화를 수행합니다.  
> JIT 컴파일 최적화: 서버 모드에서는 Just-In-Time (JIT) 컴파일러가 애플리케이션 코드를 더 많이 컴파일합니다. 이는 애플리케이션의 실행 시간 동안 동적으로 컴파일된 코드의 성능을 향상시킵니다.  
> 메모리 관리 최적화: 서버 모드에서는 힙 메모리와 가비지 컬렉션 관련 기능이 최적화됩니다. 예를 들어, 서버 모드에서는 큰 힙 크기를 기본값으로 사용하고, 가비지 컬렉션 알고리즘이 긴 지연 시간을 갖도록 조정됩니다.  

