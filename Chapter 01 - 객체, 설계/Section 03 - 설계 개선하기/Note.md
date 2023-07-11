# 설계 개선하기
> 작성자: 심세원


## 목차
- [소프트웨어 모듈이 가져야 하는 3가지 기능](#소프트웨어모듈이가져야하는3가지기능)
  - [예시 코드의 문제점](#예시코드의문제점)
- [해결방안](#해결방안)
  - [자율성을 높이자](#자율성을높이자)
  - [남은 문제점](#남은문제점)
- [설계란?](#설계란?)
- [캡슐화와 응집도](#캡슐화와응집도)
- [객체 지향](#객체지향)

# Chapter1 객체, 설계 - 설계 개선하기

### 소프트웨어 모듈이 가져야 하는 3가지 기능

> 로버트 마틴
> 
1. 실행 중에 제대로 동작
2. 간단한 작업만으로 변경이 가능
3. 개발자가 코드를 쉽게 읽고 이해할 수 있어야 한다

해당 예제 코드 (티켓 판매 코드)는 1번은 만족

but, 2번, 3번은 불만족

**이 두가지를 불만족 시키는 문제가 서로 엮여 있다**

### 예시 코드의 문제점

- 코드가 이해하기 어려운 이유
    - Theater가 관람객의 가방과, 매표소에 직접 접근함
        - 관람객, 티켓 판매원이 일을 스스로 처리한다는 일반적 직관에 벗어남
- 코드가 변경에 취약한 이유
    - Theater가 손님, 티켓 판매원에 접근하는 것 뿐만 아니라, 손님의 가방, 티켓 판매원의 사무실까지 마음대로 접근이 가능하다.
        - 어떠한 기능이 변경되면 Theater의 코드도 변경 해줘야 함
        

## 해결방안

관람객, 티켓 판매원이 직접 자신의 일을 처리 하도록 코드를 변경

→ **자율성을 높아자**

### 자율성을 높이자

Theater 코드

```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

### **enter 메서드에서 TicketOffice에 접근하는 코드를 TicketSeller 내부로 옮기기**

→ **티켓 판매원이 직접 가방과 사무실을 처리하는 자율적인 존재로 바꾸기**

**→ 캡슐화하기**

**→ Theater에서 TicketOffice로의 의존성을 제거하기**

**캡슐화**

> 객체 내부의 세부적인 사항을 숨김
> 
- 캡슐화를 통해 객체 내부로의 접근을 제한
    - 객체 사이의 결합도가 낮아짐
    - 설계를 쉽게 변경 가능

변경된 TicketSeller 코드 (sellTo 메서드 추가

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

   //해당 코드 삭제
/* public TicketOffice getTicketOffice() {
        return ticketOffice;
    }*/

	//SellTo메서드 추가
	public void sellTo(Audience audience){
			if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
	}
}
```

Theater의 enter 메서드는 sellTo를 호출하는 간단한 코드로 바뀌게 됨

```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        ticketSeller.sellTo(audience);
    }
}
```

- Theater은 TicketSeller의 **인터페이스**에만 의존
- TicketSeller는 내부에 코드를 **구현**

> *객체를 인터페이스와 구현으로 나누고 **인터페이스만을 공개**하는 것은 객체 사이의 **결합도를 낮추고 변경하기 쉬운 코드**를 작성하기 위해 따라야 하는 **가장 기본적 설계 원칙***
> 

### 남은 문제점

**티켓 판매원이 손님에 가방에 직접 접근함**

- 손님이 직접 가방에 접근하는 메서드를 만들어 주기
    - Audience의 **캡슐화를 개선하기**
    - TicketSeller가 **Audience의 인터페이스에만 의존**하도록 수정하기

**Bag을 자율적인 존재로 바꿔주기**

- Bag에 직접 접근하는 Audience의 모든 로직을 **캡슐화하여 응집도를 높여주기**

**`TicketOffice`도 자율적인 존재로 만들어주기?**

바꾼 후 `TicketOffice`, `TicketSeller`의 ****코드

```java
public class TicketOffice {
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount, Ticket... tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }

    public void sellTicketTo(Audience audience) {
        plusAmount(audience.buy(getTicket()));
    }

    private Ticket getTicket() { //TicketOffice 내부에서만 사용, private로 변경
        return tickets.remove(0);
    }

    private void plusAmount(Long amount) { //TicketOffice 내부에서만 사용, private로 변경
        this.amount += amount;
    }
}
```

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }
	 public void sellTo(**Audience audience**) {
				//sellTicketTo만 호출 함으로써 원하는 목적 달성 
        ticketOffice.sellTicketTo(**audience**);
    }
}
```

하지만,

**`TicketOffice`가 `Audience`에 대해 직접 알고 있어야 함**

**→ 새로운 의존성이 추가됨**

1. 새로운 의존성 추가
2. 결합도가 높아짐
3. 변경하기 어려운 설계가 됨
4. `TicketOffice`의 자율성은 높였지만, 전체 관점에서 결합도가 상승함

이 예제의 경우  `TicketOffice`의 자율성보다는 결합도를 낮추는 것이 이득이라는 결론이 남

## 설계란?

위 예제를 통해 알 수 있는 사실

### 1. **어떤 기능을 설계하는 방법은 한가지 이상일 수 있다**

### 2. **1번에 따라 결국 설계는 트레이드오프의 산물이다**

**트레이드오프:** 하나가 증가하면 다른 하나는 무조건 감소한다.

## 캡슐화와 응집도

자신과 밀접하게 연관된 작업만을 수행하고, 연관성 없는 작업은 다른 객체에게 위임하는 객체

→ **응집도가 높다**

***응집도를 높이기 위한 방법***

- 객체 스스로 자신의 데이터를 책임지기
    - 자신의 데이터를 스스로 처리하는 자율적인 존재여야 함
- 객체 내부의 상태를 캡슐화하고 인터페이스를 통해 상호작용 하도록 만들기

*자율적인 객체의 공동체를 만드는 것이 객체 지향 설계의 첫걸음*

## 객체 지향

**프로세스** : Data를 처리하는 과정

**책임**: 기능을 가리키는 객체지향 세계의 용어

변경 전의 `Theater` 코드

- **책임이 Theater에 몰려있음**
- 판매원, 사무실, 손님, 가방 모두에 의존하고 있음
    - 판매원, 사무실, 손님, 가방 하나라도 변경될 경우 `Theater`을 변경해야 함
    - 판매원, 사무실, 손님, 가방은 **데이터의 역할만 수행**

→ **절차 지향적 프로그래밍 방식 코드**

전형적인 의존성 구조를 보여주고 있음

변경 후 `Theater` 코드

- 자신의 데이터를 스스로 처리하도록 프로세스의 적절한 단계를 이동시킴
- 책임이 분산 되어 있음 (**책임의 이동**)
    - Theater에 몰려 있던 책임이 각 객체에 적절하게 분배됨
        - 각 객체가 자신을 스스로 책임짐

→ **데이터와 프로세스가 동일한 묘듈 내부에 위치하도록 프로그래밍**

→ ***객체 지향 프로그래밍***

***객체 지향 설계의 핵심***

> 최소한의 의존성만 남기기
> 
- 설계를 어렵게 만드는 것은 의존성
- 불필요한 의존성 없애서 결합도 낮추기
- 캡슐화를 통해 의존성을 적절히 관리 (예제의 경우)
    - 객체 사이의 결합도를 낮춤
        - 절차 지향에 비해 변경에 좀 더 유연하다고 말하는 이유
- 불필요한 세부사항을 객체 내부로 캡슐화
    - 자율성을 높임
    - 응집도 높은 객체들의 공동체 생성
