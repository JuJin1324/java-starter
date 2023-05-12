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

## Spring Async
### TODO
> TODO
