# 람다 표현식

## 람다란 무엇인가?

**람다 표현식**은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다. 람다는 이름은 없지만, 파라미터 리스트, 바디, 반환 형식, 예외 리스트는 가질수 있다.

**람다의 특징**
  - **익명** - 보통의 메서드와 달리 이름이 존재하지 않는다
  - **함수** - 특정 클래스에 종속되지 않으므로 함수이다
  - **전달** - 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다
  - **간결성** - 익명 클래스보다 훨씬 간결하다

위 특성들 때문에 람다를 사용하면 매우 간결하고, 유연한 코드를 구현할 수 있다.

아래는 기존의 Comparator 객체를 람다로 간단하게 구현했다.

```java
Comparator<Apple> byWeight = new Comparator<Apple>(){
  public int compare(Apple a1, Apple a2) {
    return a1.getWeight().compareTo(a2.getWeight());
  }
};

Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```
기존의 Comparator 코드와 비교했을 때 매우 간결하다.

이렇게 람다는 파라미터 리스트, 화살표, 그리고 람다 바디로 이루어져있다.

  - **파라미터 리스트** - '(Apple a1, Apple a2)'
  - **화살표** - '->' 이 화살표가 람다의 파라미터 리스트와 바디를 구분한다
  - **람다 바디** - 'a1.getWeight().compareTo(a2.getWeight())' 위 예제에서는 두 사과의 무게를 비교하고있다. 람다의 반환값에 해당하는 표현식이다.

**자바 8에서 지원하는 람다 표현식 예제**

  - **(String s) -> s.length()** String 형식의 파라미터 하나를 가지며 int를 반환한다. 람다 표현식에는 return이 함축되어 있다.
  - **(Apple a) -> a.getWeight() > 150** Apple 형식의 파라미터 하나를 가지며 boolean을 반환한다.
  - **(int x, int y) -> {
    System.out.println("Result:");
    System.out.println("x + y");
  }**
  
  int 형식의 파라미터 두 개를 가지며 리턴값이 없다. 람다 표현식은 여려 행의 문장을 포함할 수 있다.
  - **() -> 42** 파라미터가 없으며 int 42를 반환한다.
  - **(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())** Apple 형식의 파라미터 두개를 가지며 int를 반환한다.

이렇게 **(parameters) -> expression** 형식을 **표현식 스타일 (expression style) 람다**라고 하고, **(parameters) -> {statements;}** 형식을 **블록 스타일 (block style) 람다** 라고 한다.


## 함수형 인터페이스 (Functional Interface)

**함수형 인터페이스란?**
  - 추상 메서드를 딱 하나만 가지고 있는 인터페이스
  - **@FunctionalInterface** 어노테이션을 가지고 있는 인터페이스
  - 함수형 인터페이스도 디폴트 메서드를 포함할 수 있다. 많은 디폴트 메서드가 있더라도 **추상 메서드가 오직 하나면** 함수형 인터페이스다.

**@FunctionalInterface**는 함수형 인터페이스임을 가리키는 어노테이션이다. 이 어노테이션을 인터페이스에 선언했지만 실제로 함수형 인터페이스가 아니면 컴파일 에러를 발생시킨다.

**함수 디스크립터**

람다 표현식의 시그니처를 서술하는 메서드를 **함수 디스크립터 (function descriptor)** 라고 부른다. 

ex) *Runnable* 인터페이스의 유일한 추상 메서드 *run*은 인수와 반환값이 없으므로 *Runnable* 인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있다.

## 람다 활용 - 실행 어라운드 패턴

자원 처리에 사용하는 순환 패턴은 자원을 열고 -> 처리하고 -> 자원을 닫는다. 즉 이런식으로 **초기화/준비코드 -> 작업 -> 정리/마무리** 형식을 갖는 코드를 **실행 어라운드 패턴 (execute around pattern)** 이라고 부른다.

아래 코드를 통해서 실행 어라운드 패턴을 살펴보자

```java
  public static String processFile() throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("example.txt"))) {
      return br.readLine();   //실제 필요한 작업을 수행
    }
  }
```

#### 1단계: 동작 파라미터화

현재 위 예제 코드에서는 한 번에 한 줄만 읽을 수 있다. 하지만 한 번에 여러 줄을 읽거나 자주 사용되는 단어를 반환하려면 어떻게 해야할까? 바로 processFile의 동작을 파라미터화 하면 된다. 

```java
  String twoLines = processFile((BufferedReader b) -> b.readLine() + b.readLine());
```

위 코드는 BufferredReader에서 두 행을 출력하는 코드다.

#### 2단계: 함수형 인터페이스를 이용해서 동작 전달

함수형 인터페이스 자리에 람다를 사용할 수 있다. 그러므로 BufferedReader -> String과 IOException을 던질수 있는 시그니처와 일치하는 함수형 인터페이스를 만들어야 한다. 이 인터페이스를 BufferedReaderProcessor라고 정의하자.

```java
  @FunctionalInterface
  public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
  }
```

#### 3단계: 동작 실행

위에서 정의한 인터페이스를 processFile 메서드의 인수로 전달할 수 있다. 그러므로 이제 BufferedReaderProcessor에 정의된 process 메서드의 시그니처와 일치하는 람다를 전달할 수 있다.

```java
  public static String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(FILE))) {
      return p.process(br);   //BufferedReader 객체 처리
    }
  }
```

#### 4단계: 람다 전달

이제 람다를 이용해서 다양한 동작을 processFile 메서드로 전달해보자.

```java
 String oneLine = processFile((BufferedReader b) -> b.readLine());  //한 행을 처리하는 코드

 String twoLines = processFile((BufferedReader b) -> b.readLine() + b.readLine());  //두 행을 처리하는 코드
```

## 함수형 인터페이스 사용

다양한 람다 표현식을 사용하려면 공통의 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다. 자바 API는 Comparable, Runnable, Callable 등의 다양한 함수형 인터페이스를 포함한다.

#### Predicate

*java.util.function.Predicate<T>* 인터페이스는 test라는 추상 메서드를 정의하며 test는 제네릭 형식 T의 객체를 인수로 받아 불리언을 반환한다. BufferedReaderProcessor와 같은 형식의 인터페이스인데 따로 정의할 필요 없이 바로 사용할 수 있다는 점이 특징이다. 

```java
  public class PredicateTest {
    public static void main(String[] args) {
      List<String> listOfStrings = new ArrayList<>();
      listOfStrings.add("hello");
      listOfStrings.add("bye");

      Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
      List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
      System.out.println("nonEmpty = " + nonEmpty);
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

#### Consumer

*java.util.function.Consumer<T> 인터페이스는 제네릭 형식 T 객체를 받아서 void를 반환하는 accept라는 추상 메서드를 정의한다. T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 Consumer 인터페이스를 사용할 수 있다.

ex) Integer 리스트를 인수로 받아서 각 항목에 어떤 동작을 수행하는 forEach 메서드를 정의할때 활용할 수 있다.

```java
  public class ConsumerTest {
    public static void main(String[] args) {
      forEach(
        Arrays.asList(1, 2, 3, 4, 5),
        (Integer i) -> System.out.println(i)      //Consumer의 accept 메서드를 구현하는 람다
      );
    }

    public static <T> void forEach(List<T> list, Consumer<T> c) {
      for (T t : list) {
        c.accept(t);
      }
    }
  }
```

#### Function

*java.util.function.Function<T, R>* 인터페이스는 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 apply를 정의한다. 입력을 출력으로 매핑하는 람다를 정의할 때 Function 인터페이스를 활용할 수 있다.

ex) 사과의 무게 정보를 추출하거나 문자열을 길이와 매핑해야 할 때.

```java
  public class FunctionTest {
    public static void main(String[] args) {
      List<Integer> integerList = map(
        Arrays.asList("lambdas", "in", "action"),
        (String s) -> s.length()      //Function의 apply 메서드를 구현하는 람다
      );
      System.out.println("integerList = " + integerList);
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

##### 기본형 특화

자바의 모든 형식은 **참조형 (reference type)** 또는 **기본형 (primitive type)** 에 해당한다. 하지만 제네릭 파라미터에는 참조형만 사용할 수 있다. 그렇지만 자바에서는 기본형을 참조형으로 변환하는 기능을 제공한다. 이 기능을 **박싱** 이라고 한다, 그리고 그 반대 동작(참조형 -> 기본형)을 **언박싱** 이라고 한다. 또한 박싱과 언박싱이 자동으로 이루어지는 **오토박싱** 이라는 기능도 제공한다.

아래 코드는 오토박싱의 예제이다:
```java
  List<Integer> list = new ArrayList<>();
  for (int i = 300; i < 400; i++) {
    list.add(i);
  }
```

하지만 당연하게도 이런 변환 과정은 비용이 소모된다. 박싱한 값은 기본형을 감싸는 래퍼이며 힙에 저장되기 때문이다, 따라서 박싱한 값은 메모리를 더 소모하고 가져올 때도 메모리를 탐색하는 과정이 필요하다.

그래서 자바8에서는 기본형을 입출력으로 사용하는 상황에서 오토박싱을 피할 수 있도록 특별한 버전의 함수형 인터페이스를 제공한다. 아래 예제에서 살펴보도록 하겠다:
```java
  IntPredicate evenNumbers = (int i) -> i % 2 == 0;
  evenNumbers.test(1000);       //박싱 없음

  Predicate<Integer> oddNumbers = (Integer i) -> i % 2 != 0;
  oddNumbers.test(1000);        //박싱
```
일반적으로 위처럼 인터페이스의 이름 앞에 IntPredicate, DoublePredicate, IntConsumer 등 형식명이 앞에 붙는다.

##### 예외, 람다, 함수형 인터페이스의 관계

함수형 인터페이스는 확인된 예외를 던지는 동작을 혀용하지 않는다. 람다에서 예외를 던지려면 확인된 예외를 선언하는 함수형 인터페이스를 직접 정의하거나 람다를 try/catch 블록으로 감싸야 한다.

이전에 살펴봤던 BufferedReaderProcessor의 람다 표현식에서 예외를 던지는 방법을 알아보자.
```java
  Function<BufferedReader, String> f = (BufferedReader b) -> {
    try {
      return b.readLine();
    } catch(IOException e) {
      throw new RuntimeException(e);
    }
  };
```

---------------------------------------

## 형식 검사, 형식 추론, 제약

##### 형식 검사

람다가 사용되는 콘텍스트를 이용해서 람다의 형식을 추론할 수 있다. 특정 콘텍스트에서 기대되는 람다의 형식을 **대상 형식 (target type)** 이라고 부른다.

아래 코드로 형식 확인 과정을 살표보자:
```java
  List<Apple> heavierThan150g = filter(inventory, (Apple apple) -> apple.getWeight() > 150);
```
  1. filter 메서드의 선언을 확인한다.
  2. filter 메서드는 두 번째 파라미터로 Predicate<Apple> 형식 (대상 형식)을 기대한다.
  3. Predicate<Apple>은 test라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스다.
  4. test 메서드는 Apple을 받아 boolean을 반환하는 함수 디스크립터를 묘사한다.
  5. filter 메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 한다.

##### 형식 추론

자바 컴파일러는 람다 표현식이 사용된 콘텍스트(대상 형식)을 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론한다. 대상 형식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있다.

아래 코드를 통해서 형식을 추론했을 때와 하지 않았을 때의 차이를 알아보자:
```java
  Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());   //형식을 추론하지 않음

  Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());   //형식을 추론함
```
이렇듯 형식 추론을 사용하면 코드의 가독성이 향상될수 있다. 하지만 모든 상황에서 그렇지는 않으므로 상황에 따라 맞는 방법을 택하는것은 개발자의 역량이다.

##### 지역 변수 사용

람다 표현식에서는 익명 함수가 하는 것처럼 **자유 변수 (free variable)** 를 활용할 수 있다. 이와 같은 동작을 **람다 캡처링 (capturing lambda)** 이라고 부른다.

  - **자유 변수** : 파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수

아래는 portNumber 변수를 캡처하는 람다 예제다:
```java
  int portNumber = 1337;
  Runnable r = () -> System.out.println(portNumber);
```
하지만 자유 변수에도 제약이 있다. 람다는 인스턴스 변수와 정적 변수를 자유롭게 캡처할 수 있다, 하지만 지역 변수는 명시적으로 final로 선언되어 있거나 실질적으로 final로 선언된 변수와 똑같이 사용되어야 한다.

아래는 컴파일 에러가 발생하는 람다 코드이다:
```java
  int portnumber = 1337;
  Runnable r = () -> System.out.println(portNumber);
  portNumber = 15155;
```

**지역 변수의 제약**

지역 변수에 이런 제약이 존재하는 이유는 인스턴스 변수와 지역 변수의 태생이 다르기 때문이다. 인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 존재한다. 그렇기 때문에 람다에서 지역 변수에 바로 접근할 수 있다는 가정하에 람다가 스레드에서 실행된다면 변수를 할당한 스레드가 사라져서 변수 할당이 해제되어도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수가 있다. 그렇기 때문에 자바 구현에서는 원래 변수에 접근을 혀용하지 않고 자유 지역 변수의 복사본을 제공한다. 그러하므로 복사본의 값을 유지하기 위해서 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생겼다.

## 메서드 참조

메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다. 때로는 이 방법이 코드의 가독성이 더 좋고 자연스러울 수 있다.

아래 코드로 람다와 메서드 참조를 살펴보자:
```java
  inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));

  inventory.sort(comparing(Apple::getWeight));    //메서드 참조
```

메서드 참조를 이용하면 기존 메서드 구현으로 람다 표현식을 만들 수 있다. 그리고 이때 메서드를 명시적으로 참조함으로서 가독성을 높일 수 있다. 

##### 메서드 참조를 만드는 방법

메서드 참조는 세가지 유형으로 분리할 수 있다:

  1. **정적 메서드 참조**
    Integer의 parseInt 메서드는 Integer::parseInt로 표현할 수 있다.

  2. **다양한 형식의 인스턴스 메서드 참조**
    String의 length 메서드는 String::length로 표현할 수 있다.

  3. **기존 객체의 인스턴스 메서드 참조**
    Transaction 객체를 할당받은 expensiveTransaction 지역 변수가 있고, Transaction 객체에는 getValue 메서드가 있다면, 이를 expensiveTransaction::getValue 라고 표현할 수 있다.

세 번째 유형의 메서드 참조는 비공개 헬퍼 메서드를 정의한 상황에서 유용하게 활용할 수 있다. 예를 들어 다음처럼 isValidName이라는 헬퍼 메서드를 정의했다고 하자:
```java
  private boolean isValidName(String string) {
    return Character.isUpperCase(string.charAt(0));
  }
```
이제 Predicate<String>을 필요로 하는 적당한 상황에서 메서드 참조를 사용할 수 있다.
```java
  filter(words, this::isValidName)
```

## 람다, 메서드 참조 활용

지금까지 사용했던 사과 리스트를 다양한 정렬 기법으로 정렬하는 문제로 다시 돌아가서 이 문제를 더 세련되고 간결하게 해결해보자.

##### 1단계: 코드 전달

List API에서 sort 메서드를 제공하므로 정렬 메서드를 직접 구현할 필요는 없다. sort 메서드는 다음과 같은 시그니처를 갖는다.
```java
  void sort(Comparator<? super E> c)
```
이 코드는 Comparator 객체를 인수로 받아 두 사과를 비교한다. sort의 동작은 파라미터화되었다라고 말할수 있으므로, sort에 전달된 정렬 전략에 따라 sort의 동작이 달라질 것이다.

```java
  public class AppleComparator implements Comparator<Apple> {
    public int compare(Apple a1, Apple a2){
      return a1.getWeight().compareTo(a2.getWeight());
    }
  }
  inventory.sort(new AppleComparator());
```

##### 2단계: 익명 클래스 사용

한번만 사용할 Comparator를 위 코드처럼 구현하기 보다는 익명 클래스를 활용하자.
```java
  inventory.sort(new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2){
      return a1.getWeight().compareTo(a2.getWeight());
    }
  });
```
익명 클래스를 사용한 코드는 여전히 가독성이 좋지 않다.

##### 3단계: 람다 표현식 사용

이제 람다를 사용하면 다음과 같이 코드를 개선할 수 있다.
```java
  inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
```
자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 활용해서 람다의 파라미터 형식을 추론한다고 설명했다. 형식 추론을 사용해서 코드를 더 간략하게 줄일 수 있다.
```java
  inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

Comparator는 Comparable 키를 추출해서 Comparator 객체로 만드는 Function 함수를 인수로 받는 정적 메서드 comparing을 포함한다. comparing 메서드를 사용해서 가독성을 높일 수 있다.
```java
  Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());
```
스태틱 임포트를 활용하면 한단계 더 간소화 할 수있다.
```java
  import static java.util.Comparator.comparing;
  inventory.sort(comparing(apple -> apple.getWeight()));
```

##### 4단계: 메서드 참조 사용

메서드 참조를 사용하면 아래와 같은 코드가 완성된다.
```java
  inventory.sort(comparing(Apple::getWeight));
```
사과를 무게별로 비교해서 인벤토리를 정렬하라는 의미의 코드가 명확하고 간결하게 작성된 것을 확인할 수 있다.

## 람다 표현식을 조합할 수 있는 유용한 메서드

##### Comparator 조합

**역정렬**
사과의 무게를 내림차순으로 정렬하고 싶다면?
```java
  inventory.sort(comparing(Apple::getWeight).reversed());
```

만약에 무게가 같은 두 사과가 존재할 때 다른 정렬 조건을 추가하고 싶다면?
```java
  inventory.sort(comparing(Apple:getWeight)
           .reversed()
           .thenComparing(Apple::getCountry));      //두 사과의 무게가 같으면 국가별로 정렬
```
이렇게 두 사과의 무게가 같을때는 국가별로 정렬하도록 **thenComparing** 을 활용할 수 있다!

##### Predicate 조합

Predicate 인터페이스는 복잡한 프레디케이트를 만들 수 있도록 negate, and, or 세 가지 메서드를 제공한다.

빨각색이 아닌 사과?
```java
  Predicate<Apple> notRedApple = redApple.negate();
```

빨간색이면서 무거운 사과?
```java
  Predicate<Apple> redAndHeavyApple = redApple.and(apple -> apple.getWeight() > 150);
```

빨간색이면서 무거운 사과 또는 그냥 녹색 사과?
```java
  Predicate<Apple> redAndHeavyAppleOrGreen = redApple.and(apple -> apple.getWeight() > 150)
                                                     .or(apple -> GREEN.equals(a.getColor()));
```

##### Function 조합

Function 인터페이스는 Function 인스턴스를 반환하는 andThen, compose 두 가지 디폴트 메서드를 제공한다. 

andThen 메서드는 주어진 함수를 먼저 적용한 결과를 다른 함수의 입력으로 전달하는 함수를 반환한다. 
아래 코드를 살펴보자:
```java
  Function<Integer, Integer> f = x -> x + 1;
  Function<Integer, Integer> g = x -> x * 2;
  Function<Integer, Integer> h = f.andThen(g);

  int result = h.apply(1);
```
위 코드에서 result의 값은 4 이다. 수학적으로 위 코드를 살펴보면 g(f(x)) 로 설명할 수 있다.

compose 메서드는 인수로 주어진 함수를 먼저 실행한 다음에 그 결과를 외부 함수의 인수로 제공한다.
즉, 위 코드에서 f.andThen(g) 대신 f.compose(g)를 사용하면 g(f(x))가 아니라 f(g(x)) 라는 수식이 된다.
```java
  Function<Integer, Integer> f = x -> x + 1;
  Function<Integer, Integer> g = x -> x * 2;
  Function<Integer, Integer> h = f.compose(g);

  int result = h.apply(1);
```
위 코드에서 result의 값은 3 이다. g 가 먼저 실행된 후 그 값이 f 의 인수로 주어지는 것이다.


## 정리
  - **람다 표현식**은 익명 함수의 일종이다. 익명 함수보다 훨씬 간결하고 가독성 있는 코드 작성을 가능케 한다.
  - **함수형 인터페이스**는 하나의 추상 메서드만을 정의하는 인터페이스다.
  - *java.util.function* 패키지는 다양한 함수형 인터페이스를 제공한다.
  - 자바8은 박싱을 피할 수 있는 IntPredicate과 같은 기본형 특화 인터페이스도 제공한다.
  - 실행 어라운드 패턴을 람다와 활용하면 유연성과 재사용성을 얻을 수 있다.
  - 람다 표현식의 기대 형식을 대상 형식 이라고 한다.
  - 메서드 참조를 이용하면 기존의 메서드 구현을 재사용하고 직접 전달할 수 있다. 코드의 가독성과 간결함면에서도 메서드 참조는 아주 좋은 방법이다.
  - 함수형 인터페이스가 제공하는 다양한 디폴트 메서드를 잘 활용하면 람다 표현식을 더 유용하게 조립할 수 있다.