# 생성 사용 분리
> 작성자: 고민정

## 목차
- [생성과 사용 분리](#생성과-사용-분리)
- [팩토리 추가](#팩토리-추가)
- [순수가공물에 책임 할당](#순수가공물에-책임-할당)
<br>

## 생성과 사용 분리
결합도가 높아질수록 개방-폐쇄 구조를 설계하기 어려움 <br>

* 부적절한 곳에 객체를 생성한 예시
  
 ```java
public class Movie{
  ...
  private DiscountPolicy discountPolicy;

  pulic Movie(String title, Duration runningTime, Money fee){
    ...
    this.discountPolicy=new AmountDiscountPolicy(...);
  }
public Money calculateMovieFee(Screnning screening){
    return fee.minus(discountPolicy.calculateDiscountAmount(screening));
  }
}
```
생성자 안에서 DiscountPolicy의 인스턴스를 생성 <br>
calculateMovieFee 메서드 안에서 객체에 메세지 전송 <br>
**→ 동일 클래스 안에서 객체 생성과 사용이 공존하는 것이 문제** <br><br>
유연하고 재사용 가능한 설계를 원한다면? <br>
**객체와 관련된 두 가지 책임을 서로 다른 객체로 분리해야한다. → 생성과 사용을 분리**   <br><br>
분리하는 방법)<br>
**객체를 생성할 책임을 클라이언트로 옮긴다.** <br>
Movie의 클라이언트가 적절한 DiscountPolicy의 인스턴트를 생성한 후 Movie에게 전달<br>
→ Movie는 특정한 클라이언트에 결합되지 않고 독립적일 수 있음<br>
 ```java
public class Client{
  public Money getAvatatFee(){
    Movie avatar=new Movie(...);
    return avater.getFee()
  }
}
```
Movie의 의존성을 DiscountPolicy로 제한하기 때문에 확장은 가능하나 수정은 불가능한 코드로 만들 수 있음
<br><br>

## 팩토리 추가 
> _**FACTORY**_ <br> 생성과 사용을 분리하기 위해 객체 생성에 특화된 객체
Client도 Movie 인스턴트를 생성하는 동시에 getFee 매새지도 함께 전송하고 있음 <br>
**→ 생성과 사용의 책임을 함께 지님**
위의 Movie와 동일한 방법으로 해결 가능<br>
→ 책임만 전담하는 별도의 객체를 추가하고 **(FACTORY)** Client가 이 객체를 사용<br>
 ```java
public class Factory{
  public Money createAvatarMovie(){
    Movie avatar=new Movie(...);
  }
}
```
 ```java
public class Client{
  private Factory factory;
  ...
public Money getAvatarFee(){
    ...
    return avatar.getFee();
  }
}
```
Client는 사용과 관련한 책임만 지고 생성과 관련된 어떤 지식도 가지지 않을 수 있다. <br><br>

## 순수가공물에 책임 할당
* 시스템을 객체로 분해하는 두 가지 방식
  1) 표현적 분해 (representation decomposition)<br>
     도메인에 존재하는 사물 또는 개념을 표현하는 객체들을 이용해 시스템을 분해<br>
     도메인과 소프트웨어 사이의 표현적 차이를 최소화하는 것을 목적<br>
  2) 행위적 분해 (behavioral decomposition)

  <br>
  문제점) <br>
  모든 책임을 도메인 객체에게 할당하면 낮은 응집도, 높은 결합도, 재사용성 저하와 같은 문제에 봉착
  <br><br>
  해결법)<br>
  도메인 개념을 표현한 객체가 아닌 편의를 위해 가공의 객체로 책임을 할당<br>
  <br>
  
  >**_PURE FABRIVATION (순수한 가공물)_** <br> 책임을 할당하기 위해 창조되는 도메과 무관한 인공적인 객체
<br>

객체지향 애플리케이션에서는 일반적으로 (인공적으로 창조한 객체들) > (도메인 개념을 반영하는 객체들) <br><br>
PURE FABRICATION은 INFORMATION EXPERT 패턴에 따라 책임 할당 결과가 바람직하지 않은 경우 대안으로 사용된다.<br>
INFORMATION EXPERT에 책임을 할당할 경우 응집도가 낮아지고 결합도가 높아지면 가공의 객체를 추가해 책임을 할당해야한다.<br>
##### INFORMATION EXPERT : 수행하는 데 필요한 정보를 가장 많이 일고있는 객체
     
