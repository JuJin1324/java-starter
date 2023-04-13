# java-starter

## JDK
### Amazon corretto 설치  
> corretto 는 Amazon 에서 관리하는 OpenJDK 이다.   
> **macOS 에서 하위 버전의 corretto 설치**    
> `brew tap homebrew/cask-versions` 명령어를 치고 나서 `brew search corretto` 를 하면 안보이던 이전 버전의 corretto 들이 표시된다.  
> `brew install corretto8` 및 `brew install corretto11` 을 설치할 수 있다.  

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
