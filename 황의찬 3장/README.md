# Chapter 3-람다 표현식
2장에서 동작 파라미터화를 이용해서 변화하는 요구사항에 효과적으로 대응하는 코드를 구현할 수 있음을 확인했습니다.  
또한 정의한 코드 블록을 다른 메서드로 전달할 수 있습니다.  
  
정의한 코드 블록을 특정 이벤트(예를 들어 마우스 클릭)가 발생할 때 실행되도록 설정하거나 알고리즘의 일부로 실행되도록 설정할 수 있습니다.  
따라서 더 유연하고 재사용할 수 있는 코드를 만들 수 있습니다.  
  
깔끔하지 않은 코드는 동작 파라미터를 실전에 적용하는 것을 막는 요소입니다.  
`더 깔끔한 코드로 동작을 구현하고 전달하는` Java8의 새로운 기능인 람다 표현식을 설명합니다.  
  
람다 표현식은 `익명 클래스처럼 이름이 없는 함수면서 메서드를 인수로 전달할 수 있습니다.`    
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







