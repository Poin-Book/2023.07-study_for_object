# 구현을 통한 검증
> 작성자:고민정

## 목차
- [할인 조건 개선](#할인-조건-개선)
- [타입 분리](#타입-분리)
- [다형성 분리](#다형성-분리)
- [Movie class 개선](#Movie-class-개선)
- [변경과 유연성](#변경과-유연성)

## 할인 조건 개선 
>  _변경에 취약한 클래스란?_ <br> 코드를 수정해야 하는 이유를 한 가지 이상 가지는 클래스

**Discount condition은 다음의 세 가지의 이유로 변경될 수 있다.**
1. 새로운 할인 조건 추가
    - isSatisfied 매서드 안의 if else 구문 수정
    - 새로운 할인 조건이 새로운 데이터를 요구한다면 DiscountCondition에 속성을 추가하는 작업도 필요
2. 순번 조건을 판단하는 로직 변경
    - isSatisfiedBySequence 메서드의 내부 구현을 수정
    - 순번 조건을 판단하는 데 필요한 데이터가 변경된다면 DiscountCondition의 sequence 속성 역시 변경
3. 기간 조건을 판단하는 로직이 변경되는 경우
    - isSatisfiedByPeriod 메서드 내부 구현 수정
    - 기간 조건을 판단하는 데 필요한 데이터가 변경된다면 DiscountCondition의 dayOfWeek, startTime, endTime 속성 변경

→ DiscountCondition은 하나 이상의 변경 이유를 가지기 때문에 응집도가 낮다. <br>
*응집도가 낮다는 것은 연관성이 없는 기능이나 데이터가 하나의 클래스 안에 뭉쳐져 있다는 것을 의미

 **클레스의 응집도 판단하기**
1. 인스턴스 변수가 초기화 되는 시점을 보자
응집도가 높은 클래스는 인스턴스를 생성할 때 모든 속성을 함께 초기화한다. <br>
클래스의 속성이 서로 다른 시점에 초기화되거나 일부만 초기화된다는 것은 응집도가 낮다는 증거<br>
**→  함께 초기화되는 속성을  기준으로  코드를 분리해야 한다.**

2. 매서드들이 인스턴스 변수를 사용하는 방식을 보자
모든 메서드가 객체의 모든 속성을 사용한다면 클래스의 응집도는 높다.<br>
**→  속성 그룹과 해당 그룹에 접근하는 메서드 그룹을 기준으로 코드를 분리해야 한다.**

## 타입 분리
>DiscountCondition의 문제는 순번 조건과 기간 조건이라는 두 개의 독립적인 타입이 하나의 클래스 안에 공존하고 있다는 점
 
클래스 분리를 통해 sequence 속성만 사용하는 메서드는 SequenceCondition으로, dayOfWeek, startTime, endTime을 사용하는 메서드는 PeriodCondition으로 이동 **→  개별 클래스의 응집도 향상**

##### BUT 위의 방식이 문제를 야기

|수정 전|수정 후|
|------|---|
|Movie class와 협력하는 클래스는 DiscountCondition |Movie class의 인스턴스는 SequenceCondition과 PeriodCondition 두 개의 인스턴스 모두와 협력가능해야함|

**해결방안 )** <br>
Movie class 안에서 SequenceCondition의 목록과 PeriodCondition의 목록을 따로 유지한다.
   ##### 하지만  이 방법은 또다른 새로운 문제를 야기한다
1. Movie class가 PeriodCondition과 SequenceCondition 양쪽 모두에게 결합된다.
2. 수정 후에 새로운 할인 조건을 추가하기가 더 어려워 졌다.

**→  DiscountCondition의 입장에서 보면 응집도가 높아졌지만 변경과 캡슐화라는 관점에서 보면 전체적으로 설계의 품질이 나빠졌다.**

## 다형성 분리
> POLYMORPHISM (다형성)<br>
객체의 타입에 따라 변하는 로직이 있을때는 타입을 명시적으로 정의하고 각 타입에 다형적으로 행동하는 책임을 할당하라

Movie 입장에서 보면 SequenceCondition과 PeriodCondition은 아무 차이도 없다. <br>
<br>
**해결 방안 )** <br>
DiscountCondition 이라는 이름을 가진 인터페이스를 이용해 역할을 구현 <br>(할인조건은 SequenceCondition와 PeriodCondition class가 구현을 공유할 필요가 없기 때문이다.)
```java
public interface DiscountCondition{
    boolean isSatisfiedBy(Screening screening);
}

// SequenceCondition & PeriodCondition 실체화
public class PeriodCondition implements DiscountCondition {...}
public class SequenceCondition implements DiscountCondition {...}

public class Movie{
    ...
    private boolean isDiscountable(Screening screening){
        return discountConditions.stream().anyMatch->condition.isSatisfiedBy(screening));
        //메세지를 수신한 객체가 SequenceCondition라면 SequenceCondition의 isSatisfedBy 메서드 실행
    }
}
```    
**→ 객체의 타입에 따라 변하는 행동이 있다면 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당**   
> PROTECTED VARIATION (변경 보호)<br>
책임 할당의 관점에서 캡슐화를 설명한 것. <br>
"설계에서 변하는 것이 무엇인지 고려하고 변하는 개념을 캡슐화하라."

새로운 할인 조건을 추가하게 되는 경우 Movie의 관점에서는 DiscountCondition이 타입을 추가하더라도 전혀 영향을 받지 않는다. <br>
Movie에 대한 어떠한 수정도 필요 없고 오직 DiscountCondition interface를 실체화 하는 클래스를 추가하는 것으로 확장가능하다.

## Movie class 개선
###### 문제점 ) <br>
금액 할인 정책 영화와 비율 할인 정책 영화 두 가지 타입을 하나의 클래스 안에 구현하고 있다.<br>
**→  하나 이상의 이유로 변경될 수 있기에 응집도가 낮다.** <br>
<br>
Movie의 경우에는 클래스들 사이에 구현을 공유할 필요가 있기 때문에 추상클래스를 이용한다.
```java
public abstract class Movie{
    ...         //delete discountAmount & discountPercent
private boolean isDiscountable(Screening screening){
    return discountConditions.stream().anyMatch(condition->condition.isSatisfiedBy(screening));
}
abstract protected Money calculateDiscountAmount(); //할인 정책에 따라 할인 금액 계산 로직이 달라져야함
}
```

<br>**AmountDiscountMovie (금액 할인 정책)** <br>
Movie를 상속받은 후 Movie에 선언된 calculateDiscountAmount 매서드를 오버라이딩 하고 할인 후 금액을 반환한다.
```java
public class AmountDiscountMovie extends Movie{
    private Money discountAmount;
    ...
    @Override
    protected Money calculateDiscountAmount(){
        return discountAmount;
    }
}
``` 
*PercentDiscountMovie (비율 할인 정책) 도 위와 같은 방법으로 구현한다.<br>
*NoneDiscountMovie (할인 정책 적용 X) 는 calculateDiscountAmount에서 0원 반환
데이터 중심 설계는 응집도가 낮고 변경에 취약한 코드가 만들어질 가능성이 높다.
**→ 책임 중심으로 설계하자.**  

## 변경과 유연성
**변경에 대비할 수 있는 방법 )** <br>
1. 코드를 이해하고 수정하기 쉽도록 최대한 단순하게 설계
2. 코드를 수정하지 않고도 변경을 수용할 수 있도록 코드를 더 유연하게 만들기

전자가 더 좋은 방법이지만 유사한 변경이 반복적으로 발생하고 있다면 복잡성이 상승하더라도 후자의 방법을 추천
<br>
상속을 이용하면 새 조건이 추가, 변경 될 때 마다 새 인스턴스를 생성하고 상태를 복사하는 등 번거로워진다.<br>
**→ 이에 해결방법은 상속대신 합성을 사용한다.** <br>
금액 할인 정책이 적용된 영화를 비율 할인 정책으로 바꾸는 일은 Movie에 연결된 DiscountPolicy의 인스턴스를 교체하면 된다.
```java
Movie movie=(
    ...
    new AmountDiscountPolicy(...));
)
movie.changeDiscountPolicy(new PercentDiscountPolicy(...));
``` 
요소들 사이의 의존성 정도가 유연성의 정도를 결정한다. 
