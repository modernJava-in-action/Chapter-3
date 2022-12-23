# Chapter 3-람다 표현식
2장에서 동작 파라미터화를 이용해서 변화하는 요구사항에 효과적으로 대응하는 코드를 구현할 수 있음을 확인했습니다.  
또한 정의한 코드 블록을 다른 메서드로 전달할 수 있습니다.  
  
정의한 코드 블록을 특정 이벤트(예를 들어 마우스 클릭)가 발생할 때 실행되도록 설정하거나 알고리즘의 일부로 실행되도록 설정할 수 있습니다.  
따라서 더 유연하고 재사용할 수 있는 코드를 만들 수 있습니다.  
  
깔끔하지 않은 코드는 동작 파라미터를 실전에 적용하는 것을 막는 요소입니다.  
`더 깔끔한 코드로 동작을 구현하고 전달하는` Java8의 새로운 기능인 람다 표현식을 설명합니다.  
  
람다 표현식은 `익명 클래스처럼 이름이 없는 함수면서 메서드를 인수로 전달할 수 있습니다.`  
# 람다 표현식 요약 
+ 변수에 할당하거나  
`Runnable r1 = () -> System.out.println("Hello World 1);`
+ 함수형 인터페이스를 인수로 받는 메서드(ex:filterApples)로 전달할 수 있으며  
```java
filterApples(inventory, (Apple a) -> a.getWeight() > 150);

static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {...}

```
+ 함수형 인터페이스의 추상 메서드와 같은 시그니처를 가집니다  

## 3.1 람다란 무엇인가?
**람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이라고 할 수 있습니다.**  
람다 표현식에는 이름은 없지만, `파라미터 리스트, 바디, 반환 형식, 발생할 수 있는 예외 리스트`는 가질 수 있습니다.  
  
- 익명 : 보통의 메서드와 달리 이름이 없으므로 **익명**이라고 표현합니다.  
- 함수 : 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부릅니다. 하지만 메서드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함합니다.  
- 전달 : 람다 표현식을 `메서드 인수`로 전달하거나 `변수로 저장`할 수 있습니다.  
- 간결성 : 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없습니다.  
  
람다를 이용하면 2장에서 살펴본 동작 파라미터 형식의 코드를 더 쉽게 구현할 수 있습니다.  
```java
Comparator<Apple> byWeight = new Comparator<Apple>() {
  @Override
    public int compare(Apple a1, Apple a2) {
      return Integer.compare(a1.getWeight(), a2.getWeight());
    }
  };
```
다음은 람다를 이용한 코드입니다.  
```java
Comparator<Apple> byWeightWithLambda = (Apple a1, Apple a2) -> a1.getWeight() - a2.getWeight();
```  
람다를 이용하면 `compare 메서드의 바디를 직접 전달하는 것처럼` 코드를 전달할 수 있습니다.  
  
람다는 **세 부분**으로 이루어집니다.  
```java
(Apple a1, Apple a2) -> a1.getWeight() - a2.getWeight();
```
+ 파라미터 리스트  
Comparator의 compare 메서드 파라미터(사과 두 개)  
+ 화살표  
화살표(->)는 람다의 파라미터 리스트와 바디를 구분합니다.  
+ 람다 바디  
두 사과의 무게를 비교합니다. **람다의 반환값**에 해당하는 표현식입니다.  
  
Java8에서 지원하는 다섯 가지 람다  표현식 예제  
```java
(String s) -> s.length() //String 형식의 파라미터 하나를 가지며 int를 반환한다. return이 함축, return문 명시적으로 사용 안해도 된다.
(Apple a) -> a,getWeight > 150 //Apple 형식의 파라미터 하나를 가지며 boolean을 반환한다.
(int x, int y) -> { //int 형식의 파라미터 두 개를 가지며 리턴값이 없다(void)
  System.out.println("Result:");
  System.out.println(x + y); //람다 표현식은 여러 행의 문장을 포함할 수 있다.
}
() -> 42 //파라미터가 없으며 int 42를 반환한다.
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()); //Apple형식의 파라미터 두 개를 가지며 int를 반환한다.
```
다음은 표현식 스타일 람다라고 알려진 람다의 기본 문법이다.  
(parameters) -> expression  
  
또는 다음처럼 표현할 수 있다(블록 스타일)  
(parameters) -> { statements; }  
  
expression 쉽게 말해 값을 기술하는 것, Statement는 하나의 동작을 기술하는 것입니다.  
|사용 사례|람다 예제|
|------|---|
|불리언 표현식|`(List<String> list) -> list.isEmpty()`|
|객체 생성|`() -> new Apple(10`)|
|객체에서 소비|`(Apple a) -> {System.out.println(a.getWeight());}`|
|객체에서 선택/추출|`(String s) -> s.length()`|
|두 값을 조합|`(int a, int b) -> a * b`|
|두 객체 비교|`(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())`|

## 3.2 어디에, 어떻게 람다를 사용할까?
2장에서 구현했던 필터 메서드에도 람다를 활용할 수 있었습니다.  
```java
List<Apple> greenApples = filter(inventory, (Apple a) -> GREEN.equals(a.getColor()));
```
함수형 인터페이스라는 문맥에서 람다 표현식을 사용할 수 있습니다.  
위 예제에서는 함수형 인터페이스 `Predicate<T>`를 기대하는 filter 메서드의 두 번째 인수로 람다 표현식을 전달했습니다.  

### 3.2.1 함수형 인터페이스 
`Predicate<T>`가 바로 함수형 인터페이스입니다. `Predicate<T>`는 오직 하나의 추상 메서드만 지정하기 때문입니다.  
```java
public interface Predicate<T> {
  boolean test (T t);
}
```
간단히 말해 **함수형 인터페이스**는 정확히 하나의 추상 메서드를 지정하는 인터페이스입니다.  
지금까지 살펴본 Java API의 함수형 인터페이스로 Comparator, Runnable 등이 있습니다.  
  
많은 디폴트 메서드가 있더라도 추상 메서드가 오직 하나면 함수형 인터페이스입니다.  
  
람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 **전체 표현식을 함수형 인터페이스의 인스턴스로 취급**할 수 있다.  
(기술적으로 따지면 함수형 인터페이스를 구현한 클래스의 인스턴스). 익명 내부 클래스로도 같은 기능을 구현할 수 있다.  
```java
 Runnable r1 = () -> System.out.println("Hello World 1 "); // 람다 사용 
    Runnable r2 = new Runnable() {
      @Override
      public void run() {
        System.out.println("Hello World 2");
      }
    };
    process(r1);
    process(r2);
    process(() -> System.out.println("Hello World3"));

    public static void process(Runnable r) {
    r.run();
  }
```
### 3.2.2 함수 디스크립터 
함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가리킵니다.  
람다 표현식의 시그니처를 서술하는 메서드를 `함수 디스크립터` 라고 부릅니다.  
  
`() -> void` 표기는 파라미터 리스트가 없으며 void를 반환하는 함수를 의미합니다. 즉, Runnable의 run이 이에 해당합니다.  
`(Apple, Apple) -> int`는 두 개의 Apple을 인수로 받아 int를 반환하는 함수를 가리킵니다.  
  
람다 표현식은 변수에 할당하거나 함수형 인터페이스를 인수로 받는 메서드로 전달할 수 있으며, 함수형 인터페이스의 추상 메서드와  
같은 시그니처를 가집니다.  
  
```java
public void process(Runnable r) {
  r.run();
}
process(() -> System.out.println("This is awesome!!"));
```
위 코드를 실행하면 "This is awesome!!"이 출력됩니다.  
`() -> System.out.println(....)`은 인수가 없으며 void를 반환하는 람다 표현식입니다. 이는 Runnable 인터페이스의 run 메서드 시그니처와 같습니다.  
  
여러 개의 디폴트 메서드가 있더라도 **추상 메서드가 오직 하나면 함수형 인터페이스**입니다.  

### @FunctionalInterface
@FunctionalInterface는 함수형 인터페이스를 가리키는 어노테이션입니다.  
@FunctionalInterface로 인터페이스를 선언했지만 실제로 함수형 인터페이스가 아니면 컴파일러가 에러를 발생시킵니다.  

## 3.3 람다 활용 : 실행 어라운드 패턴 
순환 패턴은 자원을 열고, 처리한 다음에, 자원을 닫는 순서로 이루워집니다.  
즉, 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 가집니다.  
  
```java 
public String processFile() throws IOException {
  try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) { // 파일에서 한 행을 읽는 코드 
    return br.readLine(); //실제 필요한 작업을 하는 행 
  }
}
```
다음과 같은 코드가 필요합니다.  
processFile 메서드가 한 번에 두 행을 읽게 하려면 코드를 어떻게 고쳐야 할까요?  
우선 BufferedReader를 인수로 받아서 String을 반환하는 람다가 필요합니다.  
```java
String result = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

함수형 인터페이스 자리에 람다를 사용할 수 있습니다. 따라서 BufferedReader -> String과 IOException을 던질 수 있는 시그니처와  
일치하는 함수형 인터페이스를 만들어야 합니다.
```java
@FunctionalInterface //실제로 함수형 인터페이스가 아니라면 컴파일러가 예외를 발생시킴
  public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException; // BufferedReader -> String
}
```
정의한 인터페이스를 processFile 메서드의 인수로 전달할 수 있습니다.  
```java
public String processFile(BufferedReaderProcessor p) throws Exception {

}
```
이제 BufferedReaderProcessor에 정의된 process 메서드의 시그니처 (BufferedReader -> String) 과 일치하는 람다를 전달할 수 있습니다.  
**람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으며 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리됩니다.**  
```java
public static String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {//한 행을 읽는 코드
      return p.process(br);
    }
}
```
요약하자면, 함수형 인터페이스를 인수로 받는 메서드에 람다를 전달할 수 있습니다.  
람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있습니다.  
## 3.4 함수형 인터페이스 사용 
함수형 인터페이스의 추상 메서드는 람다 표현식의 시그니처를 묘사합니다.  
함수형 인터페이스의 추상 메서드 시그니처를 **함수 디스크립터** 라고 합니다.  
이미 자바 API는 Comparable, Runnable, Callable 등의 다양한 함수형 인터페이스를 포함하고 있습니다.  
  
### 3.4.1 Predicate
`java.util.function.Predicate<T>` 인터페이스는 test라는 추상 메서드를 정의하며 test는 제네릭 형식 T의 객체를 인수로 받아 **불리언을 반환합니다.**    
T 형식의 객체를 사용하는 불리언 표현식이 필요한 상황에서 Predicate를 사용할 수 있습니다.  
```java
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
}
```
활용은 다음과 같이 사용할 수 있습니다.
```java
public class PredicateTest {

  public static void main(String[] args) {

    List<String> listOfStrings = new ArrayList<>(List.of("hello"));

    Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
    List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
    System.out.println(nonEmpty);
  }

  public static <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> results = new ArrayList<>();
    for (T t : list) {
      if (p.test(t)) {
        results.add(t);
      }
    }
    return results;
  }

}
```
### 3.4.2 Consumer
`java.util.function.Consumer<T>` 인터페이스는 제네릭 형식 T 객체를 받아서 **void를 반환**하는 accept라는 추상 메서드를 정의합니다.  
T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 Consumer 인터페이스를 사용할 수 있습니다.  
예를 들어 Integer 리스트를 인수로 받아서 각 항목에 어떤 동작을 수행하는 forEach 메서드를 정의할 때 Consumer를 활용할 수 있습니다.  
```java
@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);
}
```
활용은 다음과 같이 할 수 있습니다.  
```java
/**
 * Consumer<T>
 * 제네릭 형식 T 객체를 받아서 void를 반환하는 accept라는 추상 메서드를 정의
 */
public class ConsumerTest {

  public static void main(String[] args) {
    forEach(Arrays.asList(1, 2, 3, 4, 5),
        (Integer i) -> System.out.println(i)); //Consumer의 accept를 구현하는 람다
  }
  
  public static <T> void forEach(List<T> list, Consumer<T> c) {
    for (T t : list) {
      c.accept(t);
    }
  }
}
```
### 3.4.3 Function
`java.util.function.Function<T, R>` 인터페이스는 **제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의합니다.**  
입력을 출력으로 매핑하는 람다를 정의할 때 Function 인터페이스를 활용할 수 있습니다.  
```java
@FunctionalInterface
public interface Function<T, R> {
  R apply(T t);
}
```
활용은 다음과 같습니다.  
```java
public class Functiontest {

  public static void main(String[] args) {
    List<Integer> length = map(Arrays.asList("lambdas", "in", "action"),
        (String s) -> s.length());

    System.out.println(length); //[7, 2, 6]
  }

  public static <T, R> List<R> map(List<T> list, Function<T, R> f) {
    List<R> result = new ArrayList<>();
    for (T t : list) {
      result.add(f.apply(t));
    }
    return result;
  }
}
```
### 기본형 특화
제네릭 파라미터(예를 들어 `Consumer<T>`의 `T`)에는 참조형만 사용할 수 있습니다.  
기본형을 참조형으로 변환하는 기능을 박싱, 참조형을 기본형으로 변환하는 반대 동작을 언박싱이라고 합니다.  
또한 프로그래머가 편리하게 코드를 구현할 수 있도록 박싱과 언박싱이 자동으로 이루어지는 **오토박싱**이라는 기능도 제공합니다.  
```java
List<Integer> list = new ArrayList<>();
for (int i = 300; i < 400; i++) {
  list.add(i);
}
```
하지만 이런 변환 과정은 비용이 소모됩니다. 박싱한 값은 기본형을 감싸는 `래퍼`이며 `힙`에 저장됩니다.  
따라서 박싱한 값은 메모리를 더 소비하며 기본형을 가져올 때도 메모리를 탐색하는 과정이 필요합니다.  

Java8에서는 기본형을 입출력으로 사용하는 상황에서 오토박싱 동작을 피할 수 있도록 특별한 버전의 함수형 인터페이스를 제공합니다.  
```java
IntPredicate evenNumbers = (int i) -> i % 2 == 0;
System.out.println(evenNumbers.test(1000)); //참(박싱 없음)

Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
System.out.println(oddNumbers.test(1000)); //거짓(박싱)
```
(T, U) -> R은 제네릭 형식 T와 U를 인수로 받으며 R을 반환하는 함수입니다.  
|사용 사례|람다 예제|대응하는 함수형 인터페이스|
|------|---|---|
|불리언 표현|`(List<String> list) -> list.isEmpty()`|`Predicate<List<String>>`|
|객체 생성|() -> new Apple(10)|`Supplier<Apple>`|
|객체에서 소비|(Apple a) -> System.out.println(a.getWeight())|`Consumer<Apple>`|

### 예외, 람다, 함수형 인터페이스의 관계
함수형 인터페이스는 확인된 예외를 던지는 동작을 허용하지 않습니다.  
즉, 예외를 던지는 람다 표현식을 만드려면 확인된 예외를 선언하는 함수형 인터페이스를 직접 정의하거나 람다를 try/catch 블록으로 감싸야 합니다.  
  
API를 사용하는 경우, 명시적으로 확인된 예외를 잡을 수 있다.  
```java
Function<BufferedReader, String> f = (BufferedReader b) -> {
  try {
    return b.readLine();
  }
  catch (IOException e) {
    throw new RuntimeException(e);
  }
}
```
## 3.5 형식 검사, 형식 추론, 제약 

## 3.6 메서드 참조



 







  








