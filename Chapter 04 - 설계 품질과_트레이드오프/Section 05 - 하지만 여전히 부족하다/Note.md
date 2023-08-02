# 하지만 여전히 부족하다
> 작성자: 최선규

## 목차
- [하지만 여전히 부족하다](#하지만-여전히-부족하다)
  - [목차](#목차)
  - [캡슐화 위반](#캡슐화-위반)
    - [DiscountCondition](#discountcondition)
    - [Movie](#movie)
  - [높은 결합도](#높은-결합도)
    - [Movie](#movie-1)
  - [낮은 응집도](#낮은-응집도)
    - [Screening](#screening)

## 캡슐화 위반
> 두 번째 설계가 첫 번째 설계보다 좋아진 것은 사실이나 여전히 데이터 중심의 설계이다.

### DiscountCondition
> 파급 효과(ripple effect)의 존제는 캡슐화가 부족하다는 증거이다.

```java
public class DiscountCondition {
  private DiscountConditionType type;
  private int sequence;
  private DayOfWeek dayOfWeek;
  private LocalTime startTime;
  private LocalTime endTime;

  public DiscountConditionType getType() { ... }

  public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime time) { ... }
  
  public boolean isDiscountable(int sequence) { ... }
}
```

- isDiscountable(DayOfWeek dayOfWeek, LocalTime time)
  - 객체 내부에 DayOfWeek, LocalTime이 인스턴스 변수로 포함돼 있다는 사실을 인터페이스를 통해 외부에 노출한다.
- DiscountCondition의 속성을 변경할 경우
  - 두 isDiscountable 메서드의 파라미터를 수정하고 해당 메서드를 사용하는 모든 클라이언트도 함께 수정해야 할 것이다.
  - 내부 구현의 변경이 외부로 퍼져나가는 파급 효과가 발생한다.

### Movie
```java
class Movie {
  private String title;
  private Duration runningTime;
  private Money fee;
  private List<DiscountCondition> discountConditions;

  private MovieType movieType;
  private Money discountAmount;
  private double discountPercent;

  public MovieType getMovieType() { ... }
  public Money calculateAmountDiscountedFee() { ... }
  public Money calculatePercentDiscountedFee() { ... }
  public Money calculateNoneDiscountedFee() { ... }
}
```

- 할인 정책의 종류를 인터페이스에 노출 시킨다.
  - 금액 할인 정책, 비율 할인 정책, 미적용
- 만약 새로운 할인 정책이 추가되거나 제거되는 경우
  - 이 메서드에 의존하는 모든 클라이언트가 영향을 받게 된다 = 파급 효과

## 높은 결합도
> A의 인터페이스가 아니라 '구현'을 변경하는 경우에도 이를 의존하는 B를 변경해야 한다는 것은 두 객체 사이의 결합도가 높다는 증거이다.

### Movie
```java
class Movie {
  public boolean isDiscountable(LocalDateTime whenScreened, int sequence) {
    for (DiscountCondition condition : discountConditions) {
      if (condition.getType() == DiscountConditionType.PERIOD) {
        if (condition.isDiscountable(whenScreened.getDayOfWeek(), whenScreened.toLocalTime())) {
          return true;
        }
      } else {
        if (condition.isDiscountable(sequence)) {
          return true;
        }
      }
    }
    return false;
  }
}
```

- DiscountCondition에 대한 변경이 Movie에 끼치는 영향
  - DiscountCondition의 기간 할인 조건의 명칭이 PERIOD에서 다른 값으로 변경되면 Movie를 수정해야 한다.
  - DiscountCondition의 종류가 추가되거나 삭제된다면 Movie안의 if - else 구문을 수정해야 한다.
  - condition.isDiscountable 메소드의 인자가 변경될 경우, Movie의 isDiscountable 인자도 변경되어야 하고 결과적으로 Screening까지 변경을 초래하게 된다.

## 낮은 응집도
> 하나의 변경을 위해 코드의 여러 곳을 동시에 수정해야 한다면 응집도가 낮다는 증거이다.

### Screening
```java
class Screening {
  public Money calculateFee(int audienceCount) {
    switch (movie.getMovieType()) {
      case AMOUNT_DISCOUNT:
        if (movie.isDiscountable(whenScreened.getDayOfWeek(), whenScreened.toLocalTime())) {
          return movie.calculateAmountDiscountedFee().times(audienceCount);
        }
        break;
      case PERCENT_DISCOUNT:
        if (movie.isDiscountable(whenScreened.getDayOfWeek(), whenScreened.toLocalTime())) {
          return movie.calculatePercentDiscountedFee().times(audienceCount);
        }
        break;
      case NONE_DISCOUNT:
        return movie.calculateNoneDiscountedFee().times(audienceCount);
    }
    return movie.calculateNoneDiscountedFee().times(audienceCount);
  }
}
```

- DiscountCondition의 isDiscountable의 인자가 바뀌면 Movie도 바뀌어야 하고 Screening도 변경되어야 한다.
  - 즉, 하나의 변경을 수용하기 위해 코드의 여러 곳을 동시에 변경해야 한다.
