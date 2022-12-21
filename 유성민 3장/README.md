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
  - **@FunctionalInterface** 애노테이션을 가지고 있는 인터페이스
  - 함수형 인터페이스도 디폴트 메서드를 포함할 수 있다. 많은 디폴트 메서드가 있더라도 **추상 메서드가 오직 하나면** 함수형 인터페이스다.

