# 클래스
> 작성자: 최선규

## 목차
- [클래스](#클래스)
  - [목차](#목차)
  - [클래스는 추상 데이터 타입인가?](#클래스는-추상-데이터-타입인가)
    - [타입 추상화](#타입-추상화)
    - [객체 지향](#객체-지향)
  - [추상 데이터 타입에서 클래스로](#추상-데이터-타입에서-클래스로)
    - [클래스: Employee](#클래스-employee)
  - [변경을 기준으로 선택하라](#변경을-기준으로-선택하라)
    - [추상 데이터 타입 위반 사항 check list](#추상-데이터-타입-위반-사항-check-list)
    - [객체 지향 위반 사항 check list](#객체-지향-위반-사항-check-list)
    - [추상 데이터 타입 vs 객체지향](#추상-데이터-타입-vs-객체지향)
  - [협력이 중요하다](#협력이-중요하다)

## 클래스는 추상 데이터 타입인가?
> 대부분의 프로그래밍 서적에서는 클래스를 추상 데이터 타입으로 설명한다. <br>
> 그러나 명확한 의미에서 추상 데이터 타입과 클래스는 동일하지 않다.

<center>

|객체지향 프로그래밍 (Object-Oriented Programming)|객체기반 프로그래밍(Object-Based Programming)|
|:---|:---|
|- 상속과 다형성을 지원하는 프로그래밍 기법|- 상속과 다형성을 지원하지 않는 추상 데이터 타입 기반의 프로그래밍 기법|
|- 클래스: 절차를 주상화한 것|- 추상 데이터 타입: Type을 추상화한 것|

</center>

### 타입 추상화

<center>

![image](https://github.com/luke0408/study_for_object/assets/98688494/239e5426-10ab-4823-95cd-32a7cef1a564)

</center>

- 개별 오퍼레이션이 모든 개념적인 타입에 대한 구현을 포괄 하도록 함으로써 하나의 물리적인 타입 안에 전체 타임을 감춘다.
- 타입 추상화는 오퍼레이션을 기준으로 타입을 통합하는 데이터 추상화 기법이다.

### 객체 지향

<center>

![image](https://github.com/luke0408/study_for_object/assets/98688494/07e8a3b6-188d-40af-97f2-2a7f7737cc0e)

</center>

- 타입을 기준으로 오퍼레이션을 묶는다.
- 두 가지 이상의 클래스로 분리할 경우 공통로직을 어디에 둘 것인지가 이슈
  - 공통 로직을 제공하기 위한 간단한 방법은 공통 로직을 포함할 부모 클래스를 정의하고 상속 시킨다.
- 클라이언트는 부모 클래스 참조자에 대해 메세지를 전송하면 실제 클래스가 무엇인지에 따라 다른 메소드가 실행된다.
- 실제로 내부에서 수행되는 절차는 다르지만 클래스를 이용한 다형성은 절차에 대한 차이점을 감춘다.
  - 따라서 객체지향은 절차 추상화(procedural abstraction)이다.

## 추상 데이터 타입에서 클래스로

### 클래스: Employee

- 다음과 같은 Employee 클래스를 정의한다고 가정하자.
  - Employee 클래스는 이름과 급여를 가진다.
  - Employee 클래스는 정규 직원과 아르바이트 지원 타입이 공통적으로 가져야 하는 속성과 메소드를 정의한다.

```java
public abstract class Employee {
    private String name;
    private int basePay;

    public Employee(String name, int basePay) {
        this.name = name;
        this.basePay = basePay;
    }

    abstract int calculatePay(int taxRate);

    abstract int monthlyPay();
}
```

- 정규 직원

```java
public class SalariedEmployee extends Employee {
    SalariedEmployee(String name, int basePay) {
        super(name, basePay);
    }

    @Override
    int calculatePay(int taxRate) {
        return basePay - (basePay * taxRate);
    }

    @Override
    int monthlyPay() {
        return basePay
    }
}
```

- 아르바이트 직원

```java
public class HourlyEmployee extends Employee {
    private int timeCard;

    HourlyEmployee(String name, int basePay, int timeCard) {
        super(name, basePay);
        this.timeCard = timeCard;
    }

    @Override
    int calculatePay(int taxRate) {
        return (basePay * timeCard) - ((basePay * timeCard) * taxRate);
    }

    @Override
    int monthlyPay() {
        return 0;
    }
}
```

- 인스턴스 생성

```java
Employee[] employees = {
    new SalariedEmployee("John", 1000),
    new SalariedEmployee("Mary", 1200),
    new SalariedEmployee("Steve", 1400),
    new HourlyEmployee("John", 1000, 160),
    new HourlyEmployee("Mary", 1200, 180),
    new HourlyEmployee("Steve", 1400, 200)
};
```

## 변경을 기준으로 선택하라
> 단순히 클래스를 구현 단위로 사용한다는 것이 객체지향 프로그래밍을 하는 것이 아니다. <br>
> 타입을 기준으로 절차를 추상화지 않았다면 그것은 객체지향 분해가 아니다.

### 추상 데이터 타입 위반 사항 check list

- 클래스 내부에 인스턴스의 타입을 표현하는 변수가 있는가?
  - 인스턴스 변수에 저장된 값을 기반으로 메서드 내에서 타입을 명시적으로 구분하는 방식은 객체 지향을 위반하는 것으로 간주

### 객체 지향 위반 사항 check list

<center>

![image](https://github.com/luke0408/study_for_object/assets/98688494/ea3605fe-92c8-4150-953e-766569e9f202)

</center>

- 타입 변수를 이용한 조건문으로 구분하는가?
  - 클라이언트가 객체의 타입을 확인하고 메서드를 호출하게 해서는 안됨
  - 객체가 메세지를 처리할 적절한 메서드를 처리하게 해야함

- OCP(Open-Closed Principle) 개방-폐쇄 원칙을 위반하는가?
  - OCP란 기존 코드에 아무런 영향도 미치지 않고 새로운 객체 유형과 행위를 추가할 수 있는 객체지향의 특성이다.
  - 기존코드에서 특성 변수에 대한 분기 로직이 있었다면, 새로운 요구사항이 있는 경우 코드를 수정해야 한다.
  - 하지만 객체지향을 이용하면 코드 수정없이도 새로운 유형과 행위를 추가할 수 있다.

### 추상 데이터 타입 vs 객체지향

||추상 데이터 타입|객체지향|
|:---:|:---|:---|
|선택 기준|오퍼레이션 추가가 빈번한 경우|타입 추가가 빈번한 경우|
|특징|- 추상데이터 타입의 경우 일일이 새로운 타입에 대해 체크하는 클라이언트 코드를 수정해야 한다.<br>- 객체지향의 경우 코드 수정없이 새로운 클래스를 상속 계층에 추가하면 된다.|- 객체지향의 경우 새로운 오퍼레이션을 추가하기 위해서는 상속 계층에 속하는 모든 클래스를 한번에 수정해야 한다.<br>- 추상 데이터 타입의 경우에는 전체 타입에 대한 구현 코드가 하나의 구현체에 포함되어 있다.<br>- 따라서 새로운 오퍼레이션을 추가하는 작업이 상대적으로 간단하다|

## 협력이 중요하다
> 객체지향에서 중요한 것은 역할, 책임, 협력이다.

- 단순하게 오퍼레이션과 타입을 표에 적어놓고 클래스 계층에 오퍼레이션의 구현 방법을 분배한다고 해서 객체지향적인 애플리케이션을 설계하는 것은 아니다.

- 협력이라는 문맥을 고려하지 않고 객체를 고립시킨채 오퍼레이션의 구현 방식을 타입별로 분배하는 것은 올바른 접근법이 아니다.

- 타입 계층과 다형성은 협ㅇ력이라는 문맥안에서 책임을 수행하는 방법에 관해 고민한 결과물이어야 하며 그 자체가 목적이 되어서는 안된다.
