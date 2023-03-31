# java-starter

## JDK
### Amazon corretto 설치  
> corretto 는 Amazon 에서 관리하는 OpenJDK 이다.   
> macOS 에서 하위 버전의 corretto 설치  
> `brew tap homebrew/cask-versions` 명령어를 치고 나서 `brew search corretto` 를 하면 안보이던 이전 버전의 corretto 들이 표시된다.  
> `brew install corretto8` 및 `brew install corretto11` 을 설치할 수 있다.  

## OOP
### 정적 팩토리 메서드(Static Factory Method)
> 생성자에 비해 갖는 장점  
> 1.이름을 가질 수 있다.  
> 2.객체 생성을 캡슐화할 수 있다.  
> 
> 내 의견  
> 단순 데이터만 같은 객체의 경우에는 정적 팩토리 메서드가 갖는 이점이 없다. 
> 비지니스 로직이 없는 경우에는 생성자가 갖는 매개변수와 정적 팩토리 메서드가 갖는 매개변수의 차이가 없다.  
> 비지니스 로직을 갖는 객체의 경우 의미가 있을 수 있을 듯하다.  
> 예를 들어서 OrderItem(주문 아이템) 의 경우 정적 팩토리 메서드인 of(Item item, ...) 을 사용하면 정적 팩토리 메서드 안에서 item.removeStock(count) 와 같이 
> 단순히 데이터 저장 뿐 아니라 객체와의 관계에서 행해지는 비지니스 로직이 있는 경우에 의미가 있다.
> 
> 일단 생성자의 매개변수에 해당 객체가 가지는 멤버 변수 외의 요소는 지양한다고 가정했을 경우 DTO 객체를 생성자 대신에 from 메서드를 사용해서 만드는 것도 의미가 있을 듯 하다.
> 예시로 CarReadDto 를 생성할 때 Car 를 통해서 만들려는 경우 `CarReadDto dto = new CarReadDto(...)` 대신에 `CarReadDto dto = CarReadDto(car)` 로 만드는 것이
> 코드를 이해하는데 도움이 될 것으로 생각된다.
> 
> 네이밍 컨벤션
> `from`: 하나의 매개 변수를 받아서 객체를 생성  
> `of`: 여러개의 매개 변수를 받아서 객체를 생성  
> `getInstance` 혹은 `instance`: 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음. 보통 싱글톤 객체에서 많이 사용.   
> 
> 참조사이트: [정적 팩토리 메서드(Static Factory Method)는 왜 사용할까?](https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/)

## Java 8
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

---

## java 11
### TODO
> 

### TODO
> 

### TODO
> 

### TODO
> 
