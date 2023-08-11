# 인터페이스와 설계의 품질
> 작성자:최연정

## 목차
# 인터페이스와 설계 품질

좋은 인터페이스 = 최소한의 인터페이스 + 추상적인 인터페이스

→ 책임 주도 설계 방법 따르기. 이를 위해 네 가지 원칙을 따르며 설계할 것.

# 디미터 법칙

객체의 내부 구조에 강하게 결합되지 않도록 제한해야 한다.

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        Movie movie = screening.getMovie();
        boolean discountable = false;
        
        for (DiscountCondition condition : movie.getDiscountConditions()) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek())
                    && condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0
                    && condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) > 0;
            } else {
                discountable = condition.getSequence() == screening.getSequence();
            }

            if (discountable) {
                break;
            }
        }
        
        // Your code continues here...
    }
}
```

ReservationAgency와 인자 Screening 사이의 결합도가 높음. 높은 응집도와 낮은 결합도를 가진 설계가 좋다고 여러 번 나옴.

<aside>
💡 객체끼리 끼치는 영향이 너무 많을 경우?

</aside>

ReservationAgency가 Screening에게 끼치는 영향이 너무 많을 경우, Movie와 DiscountCondition에게도 영향을 미치게 됨. 이러한 현상을 기차 충돌이라고 부르는데, 불필요한 코드가 노출될 경우 캡슐화가 무너지고 결합도가 높아짐. 따라서 디미터 법칙을 통해 필요한 코드와 협력만 제공해 낮은 결합도를 유지해야 함.

# 묻지 말고 시켜라

메시지 전송자(클라이언트)가 메시지 수신자(서버)의 상태를 보고 서버의 상태를 바꾸려고 결정하면 안 됨. 연관성이 높은(함께 변경될 확률이 높은) 정보와 행동은 하나로(같은 클래스로) 통합하여 위치하게 만들면 높은 응집도를 가질 수 있음.

# 의도를 드러내는 인터페이스

메서드의 이름에는 무엇을 하는지 (O) 어떻게 하는지 (X) 드러내야 함. 코드 이름만으로도 이해하기 쉽게 만들며 유연한 코드를 작성할 수 있음.

<aside>
💡 그렇지 않을 경우?

1. 메서드에 대해 제대로 커뮤니케이션 할 수 없음.
2. 캡슐화에 위반
</aside>

따라서 객체가 협력 안에서 수행해야 하는 책임에 관해 고민해야 함. 객체가 메시지를 전송하는 목적을 먼저 생각하게 하고, 클라이언트의 의도에 따라 메서드의 이름을 지어야 함.
