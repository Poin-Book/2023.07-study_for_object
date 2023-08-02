# 자율적인 객체를 향해
> 작성자: 최선규

## 목차
- [자율적인 객체를 향해](#자율적인-객체를-향해)
  - [목차](#목차)
  - [캡슐화를 지켜라](#캡슐화를-지켜라)
    - [예시: Rectangle 클래스](#예시-rectangle-클래스)
    - [예제 코드의 문제점](#예제-코드의-문제점)
    - [예제 코드의 문제점 해결: 캡슐화 강화](#예제-코드의-문제점-해결-캡슐화-강화)
  - [스스로 자신의 데이터를 책임지는 객체](#스스로-자신의-데이터를-책임지는-객체)
    - [코드개선: 영화 예매 시스템](#코드개선-영화-예매-시스템)

## 캡슐화를 지켜라
> 캡슐화는 설계의 제 1법칙이다.

- 데이터 중심의 설계가 낮은 응진볻와 높은 결합도라는 문제로 몸살을 앓게 된 근본적인 원인은 캡슐화를 제대로 지키지 못했기 때문이다.
- 객체는 `스스로의 상태를 책임`져야 하며 외부에서는 `인터페이스에 정의된 메서드`를 통해서만 상태를 변경할 수 있도록 해야 한다.
  - 여기서 말하는 `메서드`는 단순히 속성 하나의 값을 반환하거나 변경하는 접근자나 수정자를 말하는 것이 아니다.
  - 의미있는 메서드란 객체가 `책임`져야하는 무언가를 수행하는 메서드를 말한다.
- 속성의 가시성을 `private`으로 설정했다고 해도 접근자와 수정자를 통해 속성을 외부로 제공하고 있다면 캡슐화를 위반한 것이다.

### 예시: Rectangle 클래스

- Rectangle은 사각형의 좌표들을 포함하고 각 속성에 대한 접근자와 수정자를 제공한다.

```java
class Rectangle {
  private int left;
  private int top;
  private int right;
  private int bottom;

  public Rectangle(int left, int top, int right, int bottom) {
   ... 
  }

  public int getLeft() { return left; }
  public void setLeft(int left) { this.left = left; }

  public int getTop() { return top; }
  public void setTop(int top) { this.top = top; }

  public int getRight() { return right; }
  public void setRight(int right) { this.right = right; }

  public int getBottom() { return bottom; }
  public void setBottom(int bottom) { this.bottom = bottom; }
}
```

- 그리고 이 사각형의 너비와 높이를 증가 시키는 코드를 Rectangle 외부의 클래스에서 작성한다고 가정해보자.

```java
class AnyClass {
  public void increase(Rectangle rectangle) {
    rectangle.setRight(rectangle.getRight() + 10);
    rectangle.setBottom(rectangle.getBottom() + 10);
    ...
  }
}
```

### 예제 코드의 문제점

[코드 중복의 발생 확률 높음]
> 코드의 중복은 악의 근원이다.
- 만약 다른 곳에도 사각형의 너비와 높이를 증가시키는 코드가 필요하다면 유사한 코드가 여러 곳에 중복되어 존재하게 된다.

[변경에 취약함]
- 만약 Rectangle이 right와 bottom 대신 length와 height를 사용하도록 변경한다면?
  - getRight()와 getBottom()을 getLength()와 getHeight()로 변경해야 한다.
  - setRight()와 setBottom()을 setLength()와 setHeight()로 변경해야 한다.
  - 또한, 해당 getter, setter를 사용하는 모든 코드를 변경해야 한다.

### 예제 코드의 문제점 해결: 캡슐화 강화

- Rectangle 내부에 너비와 높이를 조절하는 로직을 추가한다.
```java
class Rectangle {
  ...
  public void enlarge(int multiple) {
    right *= multiple;
    bottom *= multiple;
  }
  ...
}
```

## 스스로 자신의 데이터를 책임지는 객체
> 우리가 상태와 행동을 객체라는 하나의 단위로 묶는 이유는 객체 스스로 자신의 상태를 처리할 수 있게 하기 위함이다.

- 객체 내부에 저장되는 데이터보다 객체가 협력에 참여하면서 수행할 책임을 정의하는 오퍼레이션이 중요
- 즉, `이 객체가 어떤 데이터를 포함하는가?` 에 대한 두 개의 개별적인 질문에 답해야함
  - 이 객체가 어떤 데이터를 포함하는가?
  - 이 객체가 데이터에 대해 수행해야하는 오퍼레이션은 무엇인가?

### 코드개선: 영화 예매 시스템 
- ReservationAgency로 새어나간 데이터에 대한 책임을 실제 데이터를 포함하고 있는 객체로 옮기는 과정 -> DiscountCondition에 isDiscountable 메서드가 필요. Screening을 통해 Reservation을 생성
- Movie -> getMovieType 메서드, isDiscountable 메서드.
- Screening -> Movie를 통해 영화 요금 계산
- 결합도 측면에서 ReservationAgency에 의존성이 몰려있던 첫 번째 설계보다 개선되었다. -> 두 번째 설계가 첫 번째 설계보다 내부 구현을 더 면밀하게 캡슐화하고 있다.

<center>

![그림 4.5](https://github.com/luke0408/study_for_object/assets/98688494/25d53d9c-d10a-4bd8-82fe-b3c6e14b7625)

</center>