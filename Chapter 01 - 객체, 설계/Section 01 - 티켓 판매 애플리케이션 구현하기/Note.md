# 티켓 판매 애플리케이션 구현하기
> 작성자: 최선규

## 목차
- [티켓 판매 애플리케이션 구현하기](#티켓-판매-애플리케이션-구현하기)
  - [목차](#목차)
  - [구현](#구현)
    - [Invitation](#invitation)
    - [Ticket](#ticket)
    - [Bag](#bag)
    - [Audience](#audience)
    - [TicketOffice](#ticketoffice)
    - [TicketSeller](#ticketseller)
    - [Theater](#theater)
    - [클래스 다이어그램](#클래스-다이어그램)

## 구현

### Invitation

```java
public class Invitation {
    private LocalDateTime when;
}
```

### Ticket

```java
public class Ticket {
    private Long fee;

    public Long getFee() {
        return fee;
    }
}
```

### Bag

```java
public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;

    public Bag(Long amount) {
        this(null, amount);
    }

    public Bag(Invitation invitation, Long amount) {
        this.invitation = invitation;
        this.amount = amount;
    }
    // 초대장을 가지고 있는지 확인
    private boolean hasInvitation() {
        return invitation != null;
    }

    // 티켓을 가지고 있는자 확인
    private boolean hasTicket() {
        return ticket != null;
    }

    // 티켓을 부여하는 메서드
    private void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    private void minusAmount(Long amount) {
        this.amount -= amount;
    }

    private void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

### Audience

```java
public class Audience {
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Bag getBag() {
        return bag;
    }
}
```

### TicketOffice

```java
public class TicketOffice {
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(Long amount, Ticket ...tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }

    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

### TicketSeller

```java 
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }
}
```


### Theater
> 절차지향적인 코드의 모습을 하고 있음

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

### 클래스 다이어그램
> 아래의 그림은 본문에 있는 티켓 판매 애플리케이션의 클래스 다이어그램이다.

![1.1 클래스 다이어그램](https://github.com/luke0408/study_for_object/assets/98688494/a0bf84d2-4195-48ff-8e03-6385fc1cae1b)