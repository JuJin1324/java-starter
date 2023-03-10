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
### Stream 을 통한 합계 구하기  
> ```java
> // Stream 의 reduce 이용
> Integer sum1 = Arrays.asList(1, 2, 3, 4, ,5).stream().reduce(0, Integer:sum);
> 
> // IntStream 의 sum 이용
> int sum2 = Arrays.asList(1, 2, 3, 4, ,5).stream().mapToInt(i -> i).sum();
>```

