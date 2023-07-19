# 추상화와 유연성
>
> 작성자: 최선규

## 목차

- [추상화와 유연성](#추상화와-유연성)
  - [목차](#목차)
  - [추상화의 힘](#추상화의-힘)
    - [추상화의 두가지 장점](#추상화의-두가지-장점)
  - [추상 클래스와 인터페이스 트레이드오프](#추상-클래스와-인터페이스-트레이드오프)
    - [이전 코드의 문제점](#이전-코드의-문제점)
    - [해결 방법 1 : 기존 클래스 구조를 그대로 두고 NoneDiscountPolicy 클래스를 추가](#해결-방법-1--기존-클래스-구조를-그대로-두고-nonediscountpolicy-클래스를-추가)
    - [해결 방법 2 : 방법1에서 문제가 됬던걸 해결하기 위해 DiscountPolicy를 인터페이스로 바꾸고, NoneDiscountPolicy가 getDiscountAmount()가 아닌 calculateDiscountAmount()를 오버라이딩 하도록 변경](#해결-방법-2--방법1에서-문제가-됬던걸-해결하기-위해-discountpolicy를-인터페이스로-바꾸고-nonediscountpolicy가-getdiscountamount가-아닌-calculatediscountamount를-오버라이딩-하도록-변경)
  - [코드 재사용](#코드-재사용)
    - [상속](#상속)
    - [합성](#합성)
    - [상속을 사용해야할 때](#상속을-사용해야할-때)

## 추상화의 힘

> 추상화: 구현의 일부(추상 클래스인 경우) 또는 전체(자바 인터페이스 경우)를 자식 클래스가 결정할 수 있도록 결정권을 위임하는 것

### 추상화의 두가지 장점

1. 추상화 계층만 따로 뗴어 놓고 살펴보면 `요구사항의 정책을 높은 수준에서 서술`할 수 있다.<br>
   - 세부사항(금액 할인 정책과 비율 할인 정책을 사용한다)에 억눌리지 않고 상위 개념(할인 정책이 존재한다)만으로 도메인의 중요한 개념을 설명할수 있게 한다.
   - 재사용 가능한 설계의 기본을 이루는 디자인 패턴(design pattern)이나 프레임워크(framework) 모두 추상화를 이용해 상위 정책을 정의하는 객체지향의 메커니즘을 활용한다.

2. 설계가 유연해진다.
   - 추상화를 이용해 상위 정책을 표현하면 기본 구조를 수정하지 않고도 새로운 기능을 추가하거나 변경할 수 있다.

<center>

![그림 2.13](https://github.com/luke0408/study_for_object/assets/98688494/d07e395d-964f-42c3-867c-3845050b92f2)

</center>

## 추상 클래스와 인터페이스 트레이드오프
>
> 우리가 작성하는 모든 코드에는 합당한 이유가 있어야한다. <br>
> 비록 아주 사소한 결정이라도 트레이드오프를 통해 얻어진 결론과 그렇지 않은 결론의 차이는 크다.

### 이전 코드의 문제점

- 책임의 위치를 결정하기 위해 조건문을 사용하고 있음

```java
public class Movie {
  public Money calculateMovieFee(Screening screening) {
    if (discountPolicy == null) {
      return fee;
    }
    return fee.minus(discountPolicy.calculateDiscountAmount(screening));
  }
}
```

### 해결 방법 1 : 기존 클래스 구조를 그대로 두고 NoneDiscountPolicy 클래스를 추가

- 단점: Money.ZERO를 이용하는 것은 좋으나, 부모 클래스인 DiscountPolicy에 할인 조건이 없는 경우에는 getDiscountAmount()를 호출하지 않음으로 어떤 값을 넣더라도 상관없다.

```java
public class NoneDiscountPolicy extends DiscountPolicy {
  @Override
  protected Money getDiscountAmount(Screening screening) {
    return Money.ZERO;
  }
}
```

### 해결 방법 2 : 방법1에서 문제가 됬던걸 해결하기 위해 DiscountPolicy를 인터페이스로 바꾸고, NoneDiscountPolicy가 getDiscountAmount()가 아닌 calculateDiscountAmount()를 오버라이딩 하도록 변경

- 결과적으로 2번 방법이 더 좋지만. NoneDiscountPolicy를 위해 인터페이스를 추가하는 것이 과하다는 생각이 들수도 있다.

```java
public class NoneDiscountPolicy implements DiscountPolicy {
  @Override
  public Money calculateDiscountAmount(Screening screening) {
    return Money.ZERO;
  }
}
```

![그림 2.15](https://github.com/luke0408/study_for_object/assets/98688494/cc2dc726-7af5-4374-aa52-50369836674a)

## 코드 재사용
>
> 객체지향에서 가장 중요한것은 객체들 사이의 상호작용이다.

### 상속
>
> 상속: 부모 클래스의 구현을 자식 클래스로 물려주는 것

**[상속의 두 가지 문제점]**

1. 상속은 캡슐화를 위반한다.
2. 상속은 설계를 유연하지 못하게 만든다.

상속을 이용하기 위해서는 부모 클래스의 내부 구조를 잘 알고 있어야한다.<br>
ex) AmountDiscountMovie와 PercentDiscountMovie를 구현하는 개발자는 부모 클래스인 Movie의 calculateMovieFee() 메서드 안에서 추상 메서드인 getDiscountAmount()를 호출한다는 사실을 알고 있어야한다.

### 합성
>
> 합성: 객체가 다른 객체의 구현을 사용하는 것

**[합성 이용해 상속의 문제 해결]**

ex) Movie 클래스에서 DiscountPolicy를 인스턴스 변수로 추가하고 이를 변경 하할 수 있도록 changeDiscountPolicy() 메서드를 추가

```java
public class Movie {
  private DiscountPolicy discountPolicy;

  public void changeDiscountPolicy(DiscountPolicy discountPolicy) {
    this.discountPolicy = discountPolicy;
  }
}
```

- 테스트 예제

```java
public class MovieTest {

  @Test
  void 영화등록() {
    Moive avater = new Movie("아바타", 
    Duration.ofMinutes(120), 
    Money.wons(10000), 
    new AmountDiscountPolicy(
      Money.wons(800), 
      ...));
    
    avater.changeDiscountPolicy(new PercentDiscountPolicy(0.1, ...));
  }
}
```

- 바뀐점
  - 전 : AmountDiscountPolicy를 PercentDiscountPolicy로 변경하기 위해서는 이미 생성된 객체의 클래스 변환이 불가능 하기 때문에 PercentDiscountPolicy를 생성하고, AmountDiscountPolicy의 상태를 복사해야 했다.
  - 후 : 그럴 필요없이 changeDiscountPolicy() 메서드를 통해 변경이 가능해졌다.

**[합성의 장점]**
1. 구현을 효과적으로 캡슐화 할 수 있다.
2. 의존하는 인스턴스를 교체하는 것이 비교적 쉽기 때문에 설계를 유연하게 만든다.

### 상속을 사용해야할 때
> 객체지향에서 가장 중요한 것은 객체 중심적으로 생각하는 것이다.

- 물론 대부분의 상황에서 합성이 상속의 문제를 해결하는 것은 맞다.
- 하지만 다형성과 인터페이스를 사용하는 경우에는 상속을 이용해야 한다.
  - 합성은 다형성과 같은 기능을 제공하지 못함.
