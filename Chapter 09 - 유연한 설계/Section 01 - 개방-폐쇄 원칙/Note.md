# 개방-폐쇄 원칙
> 작성자: 고민정

## 목차
- [개방-폐쇄 원칙](#개방-폐쇄-원칙)
- [컴파일 의존성을 고정시키고 런타임 의존성을 변경하라](#컴파일-의존성을-고정시키고-런타임-의존성을-변경하라)
- [핵심은 추상화](#핵심은-추상화)

<br>

## 개방-폐쇄 원칙
##### 확장에 대해 열려있다 : 요구사항이 변경될 때 새로운 '동작'을 추가해서 기능을 확장한다.
##### 수정에 대해 닫혀있다 : 코드를 수정하지 않고도 동작을 추가하거나 변경할 수 있다. <br>
> **개방-폐쇄 원칙 (Open-Closed Principle, OCP)** <br> 소프트웨어 개체(클래스, 모듈, 함수 ect)는 확장에 대해 열려있어야 하고, 수정에 대해서는 닫혀있어야한다.
<br>

개방-폐쇄 원칙에서 **_유연한 설계_** 란 기존 코드를 수정하지 않고도 애플리케이션의 동작을 확장할 수 있는 설계
<br>
<br>

## 컴파일 의존성을 고정시키고 런타임 의존성을 변경하라
> **런타임 의존성** <br> 실행시에 협력에 참여하는 객체들 사이의 관계 <br> <br> **컴파일 의존성** <br> 코드에서 드러나는 클래스들 사이의 관계

* _유연하고 재사용 가능한 설계_ 에서 런타임 의존성과 컴파일 타임 의존성은 서로 다른 구조를 가진다.
<img src="https://github.com/luke0408/study_for_object/assets/112948730/672c88c1-3674-4bac-a2fa-f37596b7c3ea" style="width:1000px; height:300px;"/>

컴파일타임 의존성 관점에서 Movie는 DiscountPolicy에 의존한다. <br>
런타임 의존성 관점에서 Movie 인스턴스는 AmoountDiscountPolicy와 PercentDiscountPolicy 인스턴트에 의존한다. <br>
→ **Movie의 관점에서 DiscountPolicy에 대한 컴파일타임 의존성과 런타임 의존성은 동일하지 않다.**
<br>
<br>

 
새로운 할인 정책을 추가하거나 중복 할인 정책을 추가할 때, DiscountPolicy의 수정없이 **(컴파일 의존성 고정)** 자식 클래스로 NoneDiscountPolicy와 OverlappedDiscountPolicy 클래스만 추가 **(런타임 의존성 변경)** <br>
→ 개방-폐쇄의 원칙을 따르는 설계란 **컴파일타임 의존성은 유지하면서 런타임 의존성의 가능성을 확장하고 수정할 수 있는 구조** 
<br>
<br>

## 핵심은 추상화
#### 개방-폐쇄 원칙의 핵심은 _추상화에 의존하는 것_ 이다.
개방-폐쇄 원칙의 관점에서 생략되지 않는 부분은 _공통점을 반영한 추상화의 결과물_ <br>
공통적 부분은 문맥이 바뀌어도 수정할 필요가 없어야한다.<br>
→ 변하지 않는 부분을 고정하고 변하는 부분을 생략하는 추상화 매커니즘이 개방-폐쇄 원칙의 기반 <br><br>

**BUT)** 추상화했다고 해서 수정에 대해 닫혀 있는 설계를 만들 수 있는 것은 아니다<br>
→ 폐쇄(수정에 대해 닫혀있다)를 가능하게 하는 것은 의존성의 방향
  ```java
public class Movie{
...
private DiscountPolicy discountPolicy;

public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy){
  ...
  this.discountPolicy=discountPolicy;
}

public Money calculateMovieFee(Screening screening){
  return fee.minus(discountPolicy.calculateDiscountAmount(screening));
}
}
```
Movie는 DiscountPolicy에만 의존<br>
DiscountPolicy는 변하지 않음<br>
DiscountPolicy의 자식 클래스를 추가하더라도 Movie는 영향을 받지 않음<br>
**→ movie와 DiscountPolicy는 수정에 대해 닫혀있다.** <br>
<br>
<br>
**추상화를 했다고 모든 수정에 대해 설계가 폐쇄되는 것은 아니다.** <br>
추상화가 수정에 대해 닫혀있을 수 있는 이유는 변경되지 않을 부분을 신중하게 결정했기 때문이다.
