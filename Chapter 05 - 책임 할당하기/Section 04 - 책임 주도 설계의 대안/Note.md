# 책임 주도 설계의 대안
> 작성자:고민정

## 목차
- [메서드 응집도](#메서드-응집도)
- [자율적 객체 만들기](#자율적-객체-만들기)

## 메서드 응집도
>REFACTORING <br> 
겉으로 보이는 동작은 바꾸지 않은 채 내부 구조를 변경하는 것

<br>**긴 메서드가 주는 부정적 영향 )**
1. 어떤 일을 수행하는지 한눈에 파악하기 어렵기 때문에 코드를 전체적으로 이해하는 데 너무 많은 시간 소요
2. 하나의 메서드 안에서 너무 많은 작업을 처리하기 때문에 변경이 필요할 때 수정해야할 부분을 찾기 힘듦
3. 메서드 내부의 일부 로직만 수정하더라도 메서드 나머지 부분에서 버그가 발생할 확률이 높음
4. 로직의 일부만 재사용하는 것이 불가능
5. 코드를 재사용하는 유일한 방법은 원하는 코드를 복사해서 붙여넣는 것뿐이므로 중복을 초래하기 쉬움

**→ 긴메서드는 응집도가 낮다.**
<br>이를 **monster method** 라고 부른다.
<br>
<br>객체로 책임을 분배할 때 가장 먼저 할 일은 메서드를 응집도 있는 수준으로 분해하는 것이다.<br>
수정 후에는 각 메서드들이 단 하나의 이유에 의해서만 변경됨을 알 수 있다.<br>
하나의 변경 이유를 가지도록 개선될 때 결과적으로 응집도 높은 클래스가 만들어진다. <br><br>
* **수정 전** <br>
<img src="https://github.com/luke0408/study_for_object/assets/112948730/c83c1ec8-e40b-4fe4-bdc8-1e4150241e12" style="width:700px; height:1000px;"/>
<br><br>

* **수정 후**

```java
public class ReservationAgency{
  public Reservation reserve(Screening screening, Customer customer, int audienceCount){
    boolean discountable=checkDiscountable(screening);
    Money fee = calculateFee(screening, discountable, audienceCount);
    return createReservation(screening, customer, audience, fee);
  }
  ...
}
```

**위의 경우 메서드들의 응집도 자체는 높아졌지만 메서드를 담고있는 class의 응집도가 낮아짐)** <br>
→reserve와 같은 각각의 메서드의 응집도는 높아졌지만 이를 담고있는 ReservationAgency의 응집도는 낮아졌다.<br><br>
**해결 방안 )** <br>
→변경 이유가 다른 메서드들을 적절한 위치로 분배해야한다. <br>
적절한 위치 = 각 메서드가 사용하는 데이터를 정의하고 있는 클래스<br>

## 자율적 객체 만들기
> 자율적 객체란? <br>자신이 소유하고 있는 데이터를 자기 스스로 처리하는 것

<br>**→ 메서드가 사용하는 데이터를 저장하고 있는 클래스로 메서드를 이동시킨다.**
<br>어떤 데이터를 사용하는지를 쉽게 알 수 있는 방법은 메서드 안에서 어떤 클래스의 접근자 메서드를 사용하는지 파악하면 된다.<br>
<br>isSatisfiedBySequence & isSatisfiedByPeriod 내부구현은 할인 여부를 판단하기 위해 DiscountCondition의 접근자 메서드를 이용해 데이터를 가져온다. <br>
두 메서드가 DiscountCondition에 속한 데이터를 주로 이용함을 알 수 있다.<br>
두 메서드를 데이터가 존재하는 DiscountCondition으로 이동하고 ReservationAgency에서 삭제한다.
```java
public class ReservationAgency{
    private boolean isDiscountable(Discountcondition condition, Screening screening){
        if (condition.getType()==DiscountCondition, Screening screening){
            return isSatisfiedByPeriod(condition, screening);
        }
        return isSatisfiedBySequence(condition, screening);
    }
    ...
}
```
<br>이동후에는 DiscountCondition의 내부에서만 DiscountCondition의 인스턴스 변수에 접근하여 캡슐화했다. <br>
또한 할인 조건을 계산하는데 필요한 로직이 DiscountCondition에 모여있기 때문에 응집도도 높아졌다.<br>
<br>ReservationAgency는 할인 여부를 판단하기 위해 DiscountCondition의 isDiscountable 메서드를 호출하도록 변경
```java
public class ReservationAgency{
    private boolean checkDiscountable(Screening screening){
        return screening.getMovie().getDiscuontConditions().stream().anyMatch(condition->condition.isDiscountable(screening));
    }
}
```
**→ 책임 주도 설계 방법에 익숙하지 않다면 일단 데이터 중심으로 구현한 후 이를 리팩터링하자**
