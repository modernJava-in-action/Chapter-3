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
람다로 함수형 인터페이스의 인스턴스를 만들 수 있는데, 람다 자체에는 어떤 함수형 인터페이스를 구현하는지의 정보가 포함되어 있지 않습니다.  
따라서 람다 표현식을 더 제대로 이해하려면 람다의 실제 형식을 파악해야 합니다.  
  
### 3.5.1 형식 검사
람다가 사용되는 콘텍스트를 이용해서 람다의 형식을 추론할 수 있습니다.  
어떤 콘텍스트(예를 들면 람다가 전달될 메서드 파라미터나 람다가 할당되는 변수 등)에서 기대되는 람다 표현식의 형식을 `대상 형식`이라고 부릅니다.  
```java
List<Apple> heavierThan150g = 
filter(inventory, (Apple apple -> apple.getWeight() > 150));
```
1. filter 메서드의 선언을 확인합니다.  
2. filter 메서드는 두 번째 파라미터로 `Predicate<Apple>` 형식(대상 형식)을 기대합니다.
3. `Predicate<Apple>`은 test라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스입니다.  
4. test 메서드는 Apple을 받아 boolean을 반환하는 함수 디스크립터를 묘사합니다.  
5. filter 메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 합니다.  
  
위 예제에서 람다 표현식은 Apple을 인수로 받아 boolean을 반환하므로 유효한 코드입니다.  
람다 표현식이 예외를 던질 수 있다면 추상 메서드도 같은 예외를 던질 수 있도록 throws로 선언해야 합니다.  
  
### 3.5.2 같은 람다, 다른 함수형 인터페이스 
`Callable`과 `PrivilegedAction`인터페이스는 인수를 받지 않고 제네릭 형식 T를 반환하는 함수( () -> T ) 를 정의합니다. 따라서 다음 두 할당문은 모두 유효합니다.  
```java
Callable<Integer> c = () -> 42;
PrivilegedAction<Integer> p = () -> 42;
```
위 코드에서 첫 번째 할당문의 대상 형식은 `Callable<Integer>`고, 두 번째 할당문의 대상 형식은 `PrivilegedAction<Integer>`입니다.  
즉, 하나의 람다 표현식을 다양한 함수형 인터페이스에 사용할 수 있습니다.  
  
람다의 바디에 일반 표현식이 있으면 void를 반환하는 함수 디스크립터와 호환됩니다.  
List의 add 메서드는 Consumer(T -> void)가 기대하는 void 대신 boolean을 반환하지만 유효합니다.  
```java
Consumer<String> b = s -> list.add(s); //list.add는 boolean 반환. Consumer는 T -> void
```
할당문 콘텍스트, 메서드 호출 콘텍스트(파라미터, 반환값), 형변환 콘텍스트 등으로 람다 표현식의 형식을 추론할 수 있다.  
    
### 3.5.3 형식 추론
자바 컴파일러는 람다 표현식이 사용된 콘텍스트(대상 형식)을 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론합니다.  
즉, 대상 형식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있습니다.  
  
**결과적으로 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생략할 수 있습니다.** 
```java
Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
```
상황에 따라 명시적으로 형식을 포함하는 것이 좋을 때도 있고 형식을 배제하는 것이 가독성을 향상시킬 때도 있습니다.  

### 3.5.4 지역 변수 사용
람다 표현식에서는 익명 함수가 하는 것처럼 **자유 변수**(파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수)를 활용할 수 있습니다.    
이와 같은 동작을 람다 캡처링 이라고 부릅니다.    
다음은 portNumber 변수를 캡처하는 람다 예제입니다.  
  
```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
```
하지만 자유 변수에도 약간의 제약이 있습니다. 람다는 인스턴스 변수와 정적 변수를 자유롭게 캡처(자신의 바디에서 참조할 수 있도록)할 수 있습니다.  
하지만 그러려면 지역 변수는 명시적으로 `final`로 선언되어 있어야 하거나 실질적으로 final로 선언된 변수와 똑같이 사용되어야 합니다.  
즉, 람다 표현식은 **한 번만 할당할 수 있는 지역 변수를 캡처할 수 있습니다.** 예를 들어 다음 예제는 컴파일할 수 없는 코드입니다.  
```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
portNumber = 31337;
```
Intellij Error Messgae = Variable used in lambda expression should be final or effectively final  
  
인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 위치합니다.  
람다에서 지역 변수에 바로 접근할 수 있다는 가정하에 람다가 스레드에서 실행된다면 변수를 할당한 스레드가 사라져서  
변수 할당이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수 있습니다.  
  
따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공합니다.  
따라서 복사본의 값이 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴 것입니다.  
  
지역 변수값은 스택에 존재하므로 자신을 정의한 스레드와 생존을 같이 해야 하며 따라서 지역 변수는 final이어야 합니다.  
가변 지역 변수를 새로운 스레드에서 캡처할 수 있다면 안전하지 않은 동작을 수행할 가능성이 생깁니다.(인스턴스 변수는 스레드가 공유하는 힙에 생성되므로 특별한 제약이 없습니다.)  
  
## 3.6 메서드 참조
메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있습니다.  
```java  
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())); //기존 코드
inventory.sort(Comparator.comparing(Apple::getWeight)); //메서드 참조와 java.util.Comparator.comparing을 활용한 코드
```  
### 3.6.1 요약
메서드 참조는 **특정 메서드만을 호출하는 람다의 축약형**이라고 생각할 수 있습니다.  
예를 들어 람다가 이 메서드를 직접 호출해 라고 명령한다면 이 메서드를 어떻게 호출해야 하는지 설명을 참조하기보다는 메서드명을 직접 참조하는 것이 편리합니다.  
  
메서드 참조의 유형은 다음과 같습니다.
1. 정적 메서드 참조  
예를 들어 Integer의 pareseInt 메서드는 `Integer::parseInt` 로 표현할 수 있습니다.  
2. 다양한 형식의 인스턴스 메서드 참조  
예를 들어 String의 length 메서드는 `String::length`로 표현할 수 있습니다.  
3. 기존 객체의 인스턴스 메서드 참조  
예를 들어 Transaction 객체를 할당받은 expensiveTranscation 지역 변수가 있고, Transcation 객체에는 getValue 메서드가 있다면 이를 `expensiveTranscation::getValue`  
라고 표현할 수 있습니다.  
  
세 번째 유형의 메서드 참조는 람다 표현식에서 현존하는 외부 객체의 메서드를 호출하는 데 사용됩니다.  
예를 들어 () -> expensiveTranscation.getValue()라는 람다 표현식을 expensiveTranscation::getValue로 줄여서 표현할 수 있습니다.  
  
예를 들어 다음처럼 isValid 라는 헬퍼 메서드를 정의했다고 가정해봅니다.  
```java
private boolean isValidName(String string) {
  return Character.isUpperCase(string.charAt(0));
}
```  
이제 `Predicate<String>`을 필요로 하는 상황에서 메서드 참조를 사용할 수 있습니다.  
`filter(words, this::isValidName)`  
  
컴파일러는 람다 표현식의 형식을 검사하던 방식과 비슷한 과정으로 **메서드 참조가 주어진 함수형 인터페이스와 호환하는지 확인합니다.**  
즉, 메서드 참조는 콘텍스트의 형식과 일치해야 합니다.  
### 3.6.2 생성자 참조 
```java
Supplier<Apple> c1 = Apple::new; //() -> Apple(T) 와 같은 시그니처 갖는 생성자 있다고 가정.
Apple a1 = c1.get();
```
Supplier의 get 메서드를 호출해서 새로운 Apple 객체를 만들 수 있습니다.  
  
Apple(Integer weight)라는 시그니처를 갖는 생성자는 Function 인터페이스의 시그니처와 같습니다.  
따라서 다음과 같은 코드를 구현할 수 있습니다.  
```java
Function<Integer, Apple> c2 = Apple::new;
Apple a2 = c2.apply(110); // Function 의 apply 메서드에 무게를 인수로 호출해서 새로운 Apple 객체를 만들 수 있다.
```
  
사용 예제는 다음과 같습니다.  
```java
List<Integer> weights = Arrays.asList(7, 3, 4, 10);
List<Apple> apples = map(weights, Apple::new);

public static List<Apple> map(List<Integer> list, Function<Integer, Apple> f) {
  List<Apple> result = new ArrayList<>();
  for (Integer i : list) {
    result.add(f.apply(i));
  }
  return result;
}
```
Apple(String color, Integer weight)처럼 두 인수를 갖는 생성자는 BiFunction 인터페이스와 같은 시그니처를 가지므로. `(T,U -> R)` 다음처럼 할 수 있습니다.  
```java
BiFunction<Color, Integer, Apple> c3 = Apple::new;
Apple a3 = c3.apply(Color.GREEN, 110);
```
인스턴스화하지 않고도 생성자에 접근할 수 있는 기능을 다양한 상황에 응용할 수 있습니다.  
예를 들어 Map으로 생성자와 문자열값을 관련시킬 수 있습니다. 그리고 String과 Integer가 주어졌을 때 다양한 무게를 갖는 여러 종류의 과일을 만드는  
giveMeFruit라는 메서드를 만들 수 있습니다.  
```java
static Map<String, Function<Integer, Fruit>> map = new HashMap<>();
static {
  map.put("apple", Apple::new);
  map.put("orange", Orange::new);
  //등등 
}

public static Fruit giveMeFruit(String fruit, Integer weight) {
  return map.get(fruit.toLowerCase()) //map에서 Function<Integer, Fruit>를 얻었습니다.
        .apply(weight) //Function의 apply 메서드에 정수 무게 파라미터를 제공해서 Fruit를 만들 수 있습니다.
}
```
생성자 참조 문법은 `ClassName::new`입니다.  
## 3.7 람다, 메서드 참조 활용하기
어떻게 sort 메서드에 정렬 전략을 전달할 수 있을까요?  sort 메서드는 다음과 같은 시그니처를 가집니다.  
`void sort(Comparator<? super E> c)`  
Comparator 객체를 인수로 받아 두 사과를 비교합니다.  
  
객체 안에 동작을 포함시키는 방식으로 다양한 전략을 전달할 수 있습니다.  
이제 sort의 동작은 **파라미터화되었다**라고 말할 수 있습니다. 즉, sort에 전달된 정렬 전략에 따라 sort의 동작이 달라질 것입니다.  
```java
list.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
list.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight())); 
```
자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 활용해서 람다의 파라미터 형식을 추론합니다.  
Comparator는 Comparable 키를 추출해서 Comparator 객체로 만드는 Function 함수를 인수로 받는 정적 메서드 comparing을 포함합니다.  
```java
Comparator<Apple> c = comparing((Apple a) -> a.getWeight());
inventory.sort(comparing(apple -> apple.getWeight()));
```
람다 표현식은 사과를 비교하는데 사용할 키를 어떻게 추출할 것인지 지정하는 한 개의 인수만 포함합니다.  
```java
inventory.sort(comparing(Apple::getWeight));
```
코드 자체로 Apple을 무게(Weight)별로 비교해서 inventory를 sort하라는 의미를 전달할 수 있습니다.  
## 3.8 람다 표현식을 조합할 수 있는 유용한 메서드  
예를 들어 두 프리디케이트를 조합해서 두 프리디케이트의 or 연산을 수행하는 커다란 프리디케이트를 만들 수 있습니다.  
또한 한 함수의 결과가 다른 함수의 입력이 되도록 두 함수를 조합할 수 있습니다.  
  
### 3.8.1 Comparator 조합
`Comparator.comparing`을 이용해서 비교에 사용할 키를 추출하는 Function 기반의 Comparator를 반환할 수 있습니다.  
```java
Comparator<Apple> c = comparing((Apple a) -> a.getWeight());
```
역정렬은 다음과 같이 가능합니다.
```java
inventory.sort(comparing(Apple::getWeight).reversed());
```
다른 Comparator 인스턴스를 만들 필요가 없습니다. 인터페이스 자체에서 주어진 비교자의 순서를 뒤바꾸는 reverse라는 디폴트 메서드를 제공하기 때문입니다.  
  
예를 들어 무게가 같은 두 사과가 존재할 때 , 두 번째 Comparator를 만들 수 있습니다.  
`thenComparing` 메서드로 두 번째 비교자를 만들 수 있습니다.  
```java
inventory.sort(comparing(Apple::getWeight)
    .reversed()
    .thenComparing(Apple::getCountry));
```
thenComparing은 함수를 인수로 받아 첫 번째 비교자를 이용해서 두 객체가 같다고 판단되면 두 번째 비교자에 객체를 전달합니다.  
  
### 3.8.2 Predicate 조합
Predicate 인터페이스는 복잡한 프레디케이트를 만들 수 있도록 `negate, and, or` 세 가지 메서드를 제공합니다.  
`Predicate<Apple> redApple = (apple -> apple.getColor().name().equals("RED"));`기본 조건이 다음과 같을 때,(빨간색인 사과)  
`빨간색이 아닌 사과`처럼 특정 프레디케이트를 반전시킬 때 negate 메서드를 사용할 수 있습니다.  
```java
Predicate<Apple> notRedApple = redApple.negate(); //기존 프레디케이트 객체 redApple의 결과를 반전시킨 객체를 만든다.
```
또한 and 메서드를 이용해서 빨간색이면서 무거운 사과를 선택하도록 두 람다를 조합할 수 있습니다.  
```java
Predicate<Apple> readAndHeavyApple = redApple.and(apple -> apple.getWeight() > 150);
```
그뿐만 아니라 or을 이용해서 `빨간색이면서 무거운(150그램 이상) 사과 또는 그냥 녹색 사과` 등 다양한 조건을 만들 수 있습니다.  
```java
Predicate<Apple> redAndHeavyAppleOrGreen = redApple.and(apple -> apple.getWeight() > 150)
        .or(apple -> GREEN.equals(apple.getColor()));
```
람다 표현식을 조합해서 더 복잡한 람다 표현식을 만들 수 있기 때문에 대단합니다.  
  
### 3.8.3 Function 조합 
Function 인터페이스는 Funtion 인스턴스를 반환하는 `andThen, compose` 두 가지 디폴트 메서드를 제공합니다.  
andThen 메서드는 주어진 함수를 먼저 적용한 결과를 다른 함수의 입력으로 전달하는 함수를 반환합니다.  
```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.andThen(g); //f를 먼저 적용한 결과를 g에 대입 
Integer result = h.apply(1);
System.out.println(result); // 4를 반환 
```
compose 메서드는 인수로 주어진 함수를 먼저 실행한 다음에 그 결과를 외부 함수의 인수로 제공합니다.  
```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.compose(g); //g를 먼저 실행, 그 결과를 외부 함수의 인수로 제공
Integer res = h.apply(1); // 3을 반환
System.out.println(res);
```
사용 예시는 다음과 같습니다.  
```java
public class Letter {
  public static String addHeader(String text) {
    return "From Raoul, Mario and Alan: " + text;
  }

  public static String addFooter(String text) {
    return text + " Kind regards";
  }

  public static String checkSpelling(String text) {
    return text.replaceAll("labda", "lambda"); //대상 문자열을 원하는 문자 값으로 변환하는 함수
    //replaceAll()은 정규표현식 사용이 가능합니다.
  }
}
```
유틸리티 메서드를 조합해서 다양한 변환 파이프라인을 만들 수 있습니다.  
헤더를 추가(addHeader)한 다음에, 철자 검사(checkSpelling)를 하고, 마지막에 푸터를 추가(addFooter)할 수 있습니다.  
```java
Function<String, String> addHeader = Letter::addHeader;
Function<String, String> transformationPipeline = addHeader.andThen(Letter::checkSpelling)
    .andThen(Letter::addFooter);
```
다음과 같이 철자 검사는 빼고 헤더와 푸터만 추가하는 파이프라인도 만들 수 있습니다.  
```java
Function<String, String> transformationPipeline = addHeader.andThen(Letter::addFooter);
```
## 3.10 마치며
+ 람다 표현식은 익명 함수의 일종입니다. 이름은 없지만, 파라미터 리스트, 바디, 반환 형식을 가지며 예외를 던질 수 있습니다.  
+ 함수형 인터페이스는 하나의 추상 메서드만을 정의하는 인터페이스입니다.  
+ 함수형 인터페이스를 기대하는 곳에서만 람다 표현식을 사용할 수 있습니다.  
+ 람다 표현식을 이용해서 함수형 인터페이스의 추상 메서드를 즉석으로 제공할 수 있으며 **람다 표현식 전체가 함수형 인터페이스의 인스턴스로 취급**됩니다.  
+ 메서드 참조를 이용하면 기존의 메서드 구현을 재사용하고 직접 전달할 수 있습니다.  
+ Comparator, Predicate, Function 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있는 다양한 디폴트 메서드를 제공합니다.  







 







  








