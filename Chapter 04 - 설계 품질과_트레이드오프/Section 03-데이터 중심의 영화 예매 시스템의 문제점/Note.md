# 데이터 중심 영화 예매 시스템의 문제점

> 작성자: 심세원


## 목차
- [캡슐화 위반](#캡슐화-위반)
- [높은 결합도](#높은-결합도)
- [낮은 응집도](#낮은-응집도)
  <br><br>

  캡슐화, 응집도, 결합도의 관점에서 영화 예매 시스템 설계를 평가하기

# 데이터 중심 영화 예매 시스템의 문제점

기능은 책임 중심 설계와 완전히 동일

하지만, 

**캡슐화를 위반, 객체의 내부를 인터페이스의 일부로 만들었다**

## 캡슐화 위반

직접 객체 내부에 접근할 수 없기 때문에 캡슐화가 잘되어 있는 것처럼 보임

```java
public class Movie {
		private Money fee;

		public Money getFee() {
        return fee;
    }

    public void setFee(Money fee) {
        this.fee = fee;
    }
}
```

`**public Money getFee()` , `public void setFee(Money fee)`** 

**→ 퍼블릭 인터페이스에 `Money` 라는 타입의 fee라는 이름의 인스턴스 변수가 있다는 사실을 노골적으로 드러냄**

<br>

왜 이렇게 되었을까?

- 객체가 내부에 저장할 데이터에 초점을 맞춤
- 객체가 사용될 문맥을 추측함
    - 해당 객체가 사용될 수 있게 최대한 많은 접근자 메서드를 추가해야함
    - 추측에 의한 설계 전략을 사용
        - 접근자와 수정자에 과도하게 의존하는 방식
        - 객체가 사용될 상황을 고려하지 않고 다양한 상황에서 객체를 사용할 것이라 생각
    
    **→ 결과적으로, 내부 구현이 퍼블릭 인터페이스에 그대로 노출될 수 밖에 없음**
    

<br>

객체에게 가장 중요한 것은 책임

이 책임은 협력이라는 문맥을 고려할 때 얻을 수 있는 것

## 높은 결합도

데이터 중심 설계의 영화 예매 시스템은 캡슐화를 위반함

**캡슐화 위반→ 높은 결합도**

1. 객체 내부 구현이 인터페이스에 드러남
2. 클라이언트가 구현에 강하게 결합
3. 내부 구현을 변경한다면, 이 인터페이스를 사용하는 모든 클라이언트들도 같이 변경해야 함
4. 높은 결합도

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer,
                               int audienceCount) {
        ...

        Money fee;
        if (discountable) {
            Money discountAmount = Money.ZERO;

            ...

            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        } else {
            fee = movie.getFee().times(audienceCount);
        }

        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```

1. `movie.getFee()` 를 호출
2. 계산된 결과를 `Money` 타입의 `fee`에 저장

<br>

`fee` 의 타입을 변경한다고 가정

→ `**.getFee()` 메서드**의 **변환 타입을 수정**해야함

→ `**ReservationAgency` 의 구현**도 **변경된 타입에 맞춰 수정**해야함

<br>

`.getFee()` 메서드를 사용하는 것은 `fee` 의 가시성을 사실상 `public` 로 변경하는 것과 동일

<br><br>

또한, **`ReservationAgency` 에 대부분의 제어 로직을 가지고 있음**

→ `ReservationAgency` 는 모든 의존성이 모이는 결합도의 집결지

→ 어느 것을 변경해도**,** `ReservationAgency` 를 변경해야 함

### 전체 시스템을 하나의 거대한 의존성 덩어리로 만들어 버림

→ 어떤 변경이라도 시스템 전체가 요동침

## 낮은 응집도

서로 다른 이유로 변경되는 코드가 하나의 모듈 안에 공존할 때, 응집도가 낮다고 말함

<br>

데이터 중심 영화 예매 시스템 설계에

새로운 할인 정책, 할인 조건을 추가하다면?

→   ****`ReservationAgency` 를 변경해야 함

→ 할인 정책, 조건 값을 추가해야함

→ `Movie` 를 변경해야 함

- 변경에 이유에 아무 상관 없는 코드가 영향을 받음
- 하나의 요구 사항 변경을 위해, 여러 모듈을 수정해야 함

<br>

### 단일 책임 원칙

> 클래스는 단 한 가지의 변경 이유만 가져야 한다.
> 

모듈의 응집도 변경과 연관이 있다는 사실을 강조하기 위해 단일 책임 원칙이라는 설계 원칙이 제시됨
