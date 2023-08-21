# 의존성 이해하기

> 작성자: 최선규

## 목차

- [의존성 이해하기](#의존성-이해하기)
  - [목차](#목차)
  - [변경과 의존성](#변경과-의존성)
    - [의존성의 종류](#의존성의-종류)
  - [의존성 전이](#의존성-전이)
    - [직접 의존성과 간접 의존성](#직접-의존성과-간접-의존성)
  - [런타임 의존성과 컴파일타임 의존성](#런타임-의존성과-컴파일타임-의존성)
    - [런타임(Runtime) 의존성](#런타임runtime-의존성)
    - [컴파일타임(Compile-time) 의존성](#컴파일타임compile-time-의존성)
  - [컨텍스트 독립성](#컨텍스트-독립성)
  - [의존성 해결하기](#의존성-해결하기)

## 변경과 의존성
>
> 두 요소 가이의 의존성은 의존되는 요소가 변경될 때 의존하는 요소도 함께 변경될 수 있다는 사실을 의미한다.

- 협력은 객체가 다른 객체에 대해 알 것을 강요한다. 다른 객체와 협력하기 위해서는 그런 객체가 존재한다는 `사실`을 알고있어야 하며, 동시에 그 객체가 `어떤 일`을 할 수 있다는 사실도 알고 있어야 한다.
  - 이런 지식이 객체 사이의 `의존성`을 만든다.

### 의존성의 종류

- 어떤 객체가 협력하기 위해서 다른 객체를 필요로 할 때, 두 객체 사이에는 의존성이 존재하게 된다. 그리고 의존성은 `실행 시점`과 `구현 시점`에 서로 다른 의미를 가진다.

[실행 시점]

- 의존하는 객체가 정상적으로 작동하기 위해서는 실행 시점에 의존 대상 객체가 반드시 존재해야 한다.

[구현 시점]

- 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경될 수 있다.

[예시 코드]
  
  ```java
  public class PeriodCondition implements DiscountCondition {
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    ...

    public boolean isSatisfiedBy(Screening screening) {
      return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
          startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0 &&
          endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
    }
  }
  ```

- `PeriodCondition`이 `실행 시점`에 정상적으로 작동하려면 `Screening`가 존재해야 한다.
- 그리고 이 둘 사이에는 의존성이 존대하며, 의존성은 방향성을 가지며 항상 단방항이다.
  - 즉, `PeriodCondition`이 `Screening`에 의존한다는 것은 `Screening`이 `PeriodCondition`에 의존한다는 것을 의미하지 않는다.

## 의존성 전이

[의존성은 변경에 취약하다]

- 설계과 관련된 대부분의 용어들이 변경과 관련이 있다.
- 의존성 역시 변경과 관련이 있다.
- 두 요소 사이의 의존성은 의존되는 요소가 변경될 때 의존하는 요소도 함께 변경될 수 있다.
- 따라서 의존성은 변경에 의한 영향의 `전파가능성`을 암시한다.

[의존성 전이]

![image](https://github.com/luke0408/study_for_object/assets/98688494/351bfd60-8543-4dfb-bfc2-9ef6e6840afa)

- PeriodCondition 클래스가 Screening 클래스에 의존하고, Screening 클래스가 Movie 클래스에 의존하는 경우 의존성이 전이될 수 있다.
  
- 의존성이란 함께 변경될 수 있는 `가능성`을 의미하기 때문에 모든 경우에 의존성이 전이되는 것은 아니다.
  - 의존성이 실제로 전이될지 여부는 변경의 방향과 캡슐화의 정도에 따라 달라진다.
  - 단, 의존성 전이는 변경에 의해 영향이 전파될 수도 있다는 일종의 경고이다.

### 직접 의존성과 간접 의존성

[직접 의존성]

- 말 그대로 한 요소가 다른 요소에 직접 의존하는 경우를 의미한다.
- 위 예제를 기준으로 `PeriodCondition`이 `Screening`에 직접 의존한다.
- 직접 의존선의 경우 코드에 명시적으로 나타나기 때문에 쉽게 파악할 수 있다.
  - `PeriodCondition`의 코드를 보면 `Screening`에 대한 참조가 존재한다.

[간접 의존성]

- 직접적인 관계는 존재하지 않지만 의존성 전이에 의해 영향이 전파되는 경우를 가리킨다.
- 위의 예제에서 Movie 클래스 변경으로 인해 PeriodCondition까지 변경의 영향이 있을 수도 있음을 의미한다.
- 이 경우 의존성은 명시적으로 드러나지 않는다.
  - `PeriodCondition` 내부 코드에서 `Movie`에 대한 참조는 없다

## 런타임 의존성과 컴파일타임 의존성

### 런타임(Runtime) 의존성

### 컴파일타임(Compile-time) 의존성

## 컨텍스트 독립성

## 의존성 해결하기
