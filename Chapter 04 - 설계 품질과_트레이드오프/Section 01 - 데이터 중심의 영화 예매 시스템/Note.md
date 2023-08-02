# 데이터 중심의 영화 예매 시스템

> 작성자: 심세원


## 목차
- [데이터 중심의 영화 예매 시스템](#데이터-중심의-영화-예매-시스템)
  - [데이터 중심 설계, 데이터를 준비하자](#데이터-중심-설계,-데이터를-준비하자)
    - [영화에 필요한 데이터를 결정](#영화에-필요한-데이터를-결정)
    - [할인 정책의 종류를 정의](#할인-정책의-종류를-정의)
    - [캡슐화 시키기](#캡슐화-시키기)
    - [할인 조건 구현하기](#할인-조건-구현하기)
    - [Screening, Reservation 클래스 구현하기](#Screening,-Reservation-클래스-구현하기)
    - [영화 예매 절차 구현하기](#영화-예매-절차-구현하기)
<br><br>
객체 지향의 핵심

→ 역할, 책임, 협력

<br>

협력

> 기능을 구현하기 위해, 메시지를 주고받는 객체들 사이의 상호작용
> 

<br>

책임

> 객체가 다른 객체와 협력하기 위해 수행하는 행동
> 

<br>

역할

> 대체 가능한 책임의 집합
> 

<br>

**이들 중 제일 중요한 것은 “책임”**

### 객체 지향 설계란?

> 올바른 객체에게 책임을 할당하면서, 낮은 결합도와 높은 응집도를 가진 구조를 창조하는 활동
> 

<br>

설계는 변경을 위해 존재

→ 변경에는 비용이 발생

<br>

***훌륭한 설계란?***

### 합리적인 비용 안에서 변경을 수용할 수 있는 구조를 만드는 것

적절한 비용 안에서 쉽게 변경할 수 있는 설계를 해야 한다.

**→  낮은 결합도와 높은 응집도**

<br>

이를 위해서는 **객체의 행동이 아닌, 책임에 초점을 맞춰야 함.**

만약 데이터 중심으로 설계를 한다면?

# 데이터 중심의 영화 예매 시스템

데이터 중심의 관점에서 객체란?

**자신이 포함하고 있는 데이터를 조작하는 데 필요한 오퍼레이션을 정의**

<br>

책임 중심의 관점에서 객체란?

**다른 객체가 요청할 수 있는 오퍼레이션을 위해 필요한 상태를 보관**

<br>

| 데이터 중심 | 객체를 독립된 데이터 덩어리로 봄 |
| --- | --- |
| 책임 중심 | 객체를 협력하는 공동체의 일원으로 봄 |

<br>

‘객체의 상태’는 구현에 속함

‘구현’은 불안정하기에 변하기 쉬움

따라서

**상태를 중심축으로 삼으면 구현에 관한 세부사항이 객체의 인터페이스에 스며들게 됨**

→ 캡슐화의 원칙이 무너짐

→ 상태 변경이 인터페이스의 변경을 초래함

→ 인터페이스에 의존하는 모든 객체에게 변경의 영향이 퍼짐

### 따라서, 데이터 중심 설계는 변경에 취약

<br>

‘객체의 책임’은 인터페이스에 속함

→ 안정적인 인터페이스 뒤로 책임을 수행할 때 필요한 상태를 캡슐화

→ 구현 변경에 대한 파장이 외부로 퍼지는 것을 막음 

### 따라서, 책임 중심 설계는 변경에 안정적

## 데이터 중심 설계, 데이터를 준비하자

**데이터 중심 설계**

> **객체 내부에 저장되는 데이터를 기반으로 시스템을 분할하는 방법**
> 

→ “객체가 내부에 저장해야 하는 데이터가 무엇인가”로 시작

(책임 중심은 “책임이 무엇인가?”로 시작)

<br>

### 1. 영화에 필요한 데이터를 결정

영화 제목, 상영 시간, 기본 요금, 할인 조건 목록, 할인 금액, 할인 비율이 필요 

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;

//아래는 기존 설계에서 추가된 부분
    private List<DiscountCondition> discountConditions;

    private MovieType movieType;
    private Money discountAmount;
    private double discountPercent;
}
```

→ 할인 정책은 영화 별로 하나만 필요함

→ 영화 별로 사용된 할인 정책을 알아야함

### 2. 할인 정책의 종류를 정의

할인 정책의 종류를 enum으로 정의

```java
public enum MovieType {
    AMOUNT_DISCOUNT,    // 금액 할인 정책
    PERCENT_DISCOUNT,   // 비율 할인 정책
    NONE_DISCOUNT       // 미적용
}
```

<br>

위 과정은 데이터 중심 설계의 과정

1. movie가 할인 금액을 계산하는 데 필요한 데이터가 무엇인가?
    
    할인 금액과 할인 비율
    
    - 할인 금액과 할인 비율을 movie 클래스에 포함
2. 애매 가격을 계산하기 위해 필요한 데이터는 무엇인가?
    
    할인 정책
    
    - 할인 정책 타입의 인스턴스를 속성으로 포함

<br>

**객체가 포함해야 하는 데이터는 무엇인가?**

라는 질문의 반복이라면, 데이터 중심 설계를 하고 있는 것이 아닌지 의심해야함

### 3. 캡슐화 시키기

객체지향의 가장 중요한 원칙, 캡슐화를 위해

내부 데이터를 반환하는 **접근자**, 데이터를 변경하는 **수정자**를 추가

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;

    private MovieType movieType;
    private Money discountAmount;
    private double discountPercent;

    

    public MovieType getMovieType() {
        return movieType;
    }

    public void setMovieType(MovieType movieType) {
        this.movieType = movieType;
    }

    public Money getFee() {
        return fee;
    }

    public void setFee(Money fee) {
        this.fee = fee;
    }

    public List<DiscountCondition> getDiscountConditions() {
        return Collections.unmodifiableList(discountConditions);
    }

    public void setDiscountConditions(List<DiscountCondition> discountConditions) {
        this.discountConditions = discountConditions;
    }

    public Money getDiscountAmount() {
        return discountAmount;
    }

    public void setDiscountAmount(Money discountAmount) {
        this.discountAmount = discountAmount;
    }

    public double getDiscountPercent() {
        return discountPercent;
    }

    public void setDiscountPercent(double discountPercent) {
        this.discountPercent = discountPercent;
    }
}
```

→ 캡술화에 성공 했으니

→ 할인 조건을 구현해야함

### 4.할인 조건 구현하기

“할인 조건을 구현하는 데 필요한 데이터는 무엇인가?”

→ 할인 조건의 종류

```java
public enum DiscountConditionType {
    SEQUENCE,       // 순번조건
    PERIOD          // 기간 조건
}
```

“할인 조건에 따라 필요한 데이터는 무엇인가?”

→ 기간 조건: 요일, 시작 시간, 종료 시간

→ 순번 조건: 상영 순번

```java
public class DiscountCondition {
    private DiscountConditionType type; //할인 조건의 종류

    private int sequence; //순번 조건에 필요한 데이터

    private DayOfWeek dayOfWeek; //기간 조건에 필요한 데이터
    private LocalTime startTime;
    private LocalTime endTime;
```

캡슐화를 위해, getter, setter 메서드 추가하기

(코드 생략)

### 4. Screening, Reservation 클래스 구현하기

지금까지 한 것과 동일한 과정으로 구현

```java

public class Screening {
//상영에 필요한 데이터, 영화 종류, 상영 순서, 상영 시간
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;
		

//getter, setter 메서드
    public Movie getMovie() {
        return movie;
    }

    public void setMovie(Movie movie) {
        this.movie = movie;
    }

    public LocalDateTime getWhenScreened() {
        return whenScreened;
    }

    public void setWhenScreened(LocalDateTime whenScreened) {
        this.whenScreened = whenScreened;
    }

    public int getSequence() {
        return sequence;
    }

    public void setSequence(int sequence) {
        this.sequence = sequence;
    }
}
```

```java
public class Reservation {
//영화 예약에 필요한 데이터
//어떤 손님인지, 상영 정보, 금액, 표 개수
    private Customer customer; //고객이 정보를 포함하는 클래스
    private Screening screening;
    private Money fee;
    private int audienceCount;
	
    public Reservation(Customer customer, Screening screening, Money fee,
                       int audienceCount) {
        this.customer = customer;
        this.screening = screening;
        this.fee = fee;
        this.audienceCount = audienceCount;
    }

//getter, setter 메서드
    public Customer getCustomer() {
        return customer;
    }

    public void setCustomer(Customer customer) {
        this.customer = customer;
    }

    public Screening getScreening() {
        return screening;
    }

    public void setScreening(Screening screening) {
        this.screening = screening;
    }

    public Money getFee() {
        return fee;
    }

    public void setFee(Money fee) {
        this.fee = fee;
    }

    public int getAudienceCount() {
        return audienceCount;
    }

    public void setAudienceCount(int audienceCount) {
        this.audienceCount = audienceCount;
    }
}
```

<br><br>

영화 예매 시스템을 위한 모든 데이터를 클래스로 구현 완료함

<br>

이 데이터를 영화를 예매하기 위한 절차는?

예약 →고객

ㄴ>상영 → 영화 → 할인 조건

<br>

### 6. 영화 예매 절차 구현하기

```java
public class ReservationAgency {

    public Reservation reserve(Screening screening, Customer customer,
                               int audienceCount) {
        Movie movie = screening.getMovie();
				
				//할인 여부를 체크하는 for문
        boolean discountable = false; //할인 여부를 지역변수 discountable에 저장
        for(DiscountCondition condition : movie.getDiscountConditions()) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                        condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                        condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
            } else {
                discountable = condition.getSequence() == screening.getSequence();
            }

            if (discountable) {
                break;
            }
        }

				//할인 정책에 따라 예매 요금을 계산하는 if문
        Money fee;
        if (discountable) {
            Money discountAmount = Money.ZERO;
						//할인 정책 경우에 따라 요금을 계산
            switch(movie.getMovieType()) {
                case AMOUNT_DISCOUNT:
                    discountAmount = movie.getDiscountAmount();
                    break;
                case PERCENT_DISCOUNT:
                    discountAmount = movie.getFee().times(movie.getDiscountPercent());
                    break;
                case NONE_DISCOUNT:
                    discountAmount = Money.ZERO;
                    break;
            }
						//할인 금액 만큼 요금을 차감
            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        } else {
            fee = movie.getFee().times(audienceCount);
        }
				//계산된 요금은 Reservation을 생성할 때 이용
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```
