# 프로시저 추상화와 기능 분해
> 작성자: 고민정

## 목차
- [메인 함수로서의 시스템](#메인-함수로서의-시스템)
- [급여 관리 시스템](#급여-관리-시스템)
- [급여 관리 시스템 구현](#급여-관리-시스템-구현)
- [하향식 기능 분해의 문제점](#하향식-기능-분해의-문제점)
  - [하나의 메인 함수](#하나의-메인-함수)
  - [메인 함수의 빈번한 재설계](#메인-함수의-빈번한-재설계)
  - [비즈니스 로직과 사용자 인터페이스의 결합](#비즈니스-로직과-사용자-인터페이스의-결합)
  - [성급하게 결정된 실행 순서](#성급하게-결정된-실행-순서)
  - [데이터 변경으로 인한 파급효과](#데이터-변경으로-인한-파급효과)
- [하향식 분해가 유용한 시점](#하향식-분해가-유용한-시점)

<br>

## 메인 함수로서의 시스템
> **_프로시저_**  <br> 반복적으로 실행되거나 유사하게 실행되는 작업들을 하나의 장소에 모아놓음으로써 로직의 재사용과 중복을 방지하는 방법<br>

프로시저는 잠재적으로 정보은닉의 가능성을 제시하지만 프로시저만으로는 효과적인 정보은닉 체계를 구축하는 데는 한계가 있다. <br>
<br>
> **_하향식 접근법 (Top-Down Approach)_**  <br> 시스템을 구성하는 가장 최상위 기능을 정의하고, 이 최상위 기능을 좀 더 작은 단계의 하위 기능으로 분해해 나가는 방법

상위 기능은 하나 이상의 더 간단하고 더 구체적이며 덜 추상적인 하위 기능의 집합으로 분해된다.<br><br>
<br>
## 급여 관리 시스템
#### 급여 = 기본급 - (기본급 * 소득세율)<br>
메인 프로시저 → **직원의 급여를 계산한다.** <br>
메인 프로시저를 세분화 해보자.<br>
<br>
**직원의 급여를 계산한다.** <br>
   **사용자로부터 소득세율을 입력받는다.** <br>
     "세율을 입력하세요 : " 라는 문장을 화면에 출력한다.<br>
      키보드를 통해 세율을 입력받는다.<br>
   **직원의 급여를 계산한다.** <br>
     전역 변수에 저장된 직원의 기본급 정보를 얻는다.<br>
      급여를 계산한다.<br>
   **양식에 맞게 결과를 출력한다.** <br>
     "이름 : {직원명}, 급여 : {계산된 금액}" 형식에 따라 출력 문자열을 생성한다.<br>
<br>     
  ## 급여 관리 시스템 구현
  각 단계를 세분화 시켜 ruby로 구현한다.<br>

  
  **직원의 급여를 계산한다**
  ```ruby
def main(name)
end
```
<br>

**사용자로부터 소득세율을 입력받는다.** <br>
     "세율을 입력하세요 : " 라는 문장을 화면에 출력한다.<br>
      키보드를 통해 세율을 입력받는다.<br>
  ```ruby
def getTaxRate()
  print("세율을 입력하세요 : ")
  return gets().chomp().to_f()
end
```
<br>

**직원의 급여를 계산한다.** <br>
     전역 변수에 저장된 직원의 기본급 정보를 얻는다.<br>
      급여를 계산한다.<br>
  ```ruby
def caculatePatFor(name, taxRate)
  index = $employees.index(name)
  basePay = $basePays[index]
  return basePay - (basePay * taxRate)
end
```
<br>

**양식에 맞게 결과를 출력한다.** <br>
     "이름 : {직원명}, 급여 : {계산된 금액}" 형식에 따라 출력 문자열을 생성한다.<br>
  ```ruby
def describeResult(name,pay)
  return "이름 : #{name}, 급여 : #{pay}"
end
```
<br>

만약 이름이 "직원 C"인 직원의 급여를 계산하고 싶다면 다음과 같이 호출하면 된다.
  ```ruby
main("직원 C")
```

<br>

* 현재까지 구현한 급여 관리 시스템의 트리구조
<img src="https://github.com/luke0408/study_for_object/assets/112948730/d592db71-2f20-4f04-817b-d239c2d43b27">

<br>
<br>

## 하향식 기능 분해의 문제점
하향식 기능 분해 방법은 겉으로는 이상적인 방법으로 보일 수 있지만 실제로 설계에 적용하다 보면 다양한 문제에 직면한다.
1. 시스템은 하나의 메인 함수로 구성돼 있지 않다.
2. 기능 추가나 요구사항 변경으로 인해 메인 함수를 빈번하게 수정해야 한다.
3. 비즈니스 로직이 사용자 인터페이스와 강하게 결합된다.
4. 하향식 분해는 너무 이른 시기에 함수들의 실행 순서를 고정시키기 때문에 유연성과 재사용성이 저하된다.
5. 데이터 형식이 변경될 경우 파급효과를 예측할 수 없다.
<br>

### 하나의 메인 함수
**어떤 시스템도 최초에 릴리스됐던 당시의 모습을 그대로 유지하지는 않는다.** <br>
시간이 지나고 사용자를 만족시키기 위한 새로운 요구사항을 도출해나가면서 지속적으로 새로운 기능을 추가하게 된다.<br>
이는 시스템이 오직 하나의 메인 함수만으로 구현된다는 개념과는 모순된다.<br>
<br>
하향식 접근법은 하나의 알고리즘을 구현하거나 배치 처리를 구현하기에는 적합하지만 현대적인 상호작용 시스템을 개발하는 데는 적합하지 않다.<br>
현대적인 시스템은 동등하 수준의 다양한 기능으로 구성된다.<br>
<br>
### 메인 함수의 빈번한 재설계
하나의 메인 함수를 유일한 정상으로 간주하는 하향식 기능 분해의 경우에는 새로운 기능을 추가할 때마다 매번 메인 함수를 수정해야 한다.<br>
<br>
급여 관리 시스템에 직원들의 기본급 총합을 구하는 기능을 추가해 달라는 요구사항이 들어왔다. <br>
전역변수 $basePays에 저장돼 있는 직원들의 모든 기본급을 더하면 되는 작업이다.
  ```ruby
def sumOfBasePays()
  result = 0
  for basePay in $basePays
  result += basePay
end
  puts(result)
end
```
sumOfBasePays 함수와 개별 직원의 급여를 계산하는 main 함수는 개념적으로 동등한 수준의 작업을 수행한다.<br>
따라서 현재의 main 함수 안에서 sumOfBasePays 함수를 호출할 수 없다.<br>
<br>
**즉 main 함수를 재설계 해줘야 한다.** 
  ```ruby
def main(operation, args={})
  case(opertaion)
  when :pay then calculatePay(args[:name])
  when :basePays then sumOfBasePays()
  end
end
```
기존 코드의 빈번한 수정으로 인한 버그 발생 확률이 높아지기 때문에 시스템은 변경에 취약해진다.
<br>
### 비즈니스 로직과 사용자 인터페이스의 결합
비즈니스 로직과 인터페이스가 변경되는 빈도가 다르다.<br>
사용자 인터페이스는 시스템 내에서 가장 자주 변경되는 부분인반면 비느지스 로직은 변경이 적다.<br>
하향식 접근법은 사용자 인터페이스 로직과 비즈니스 로직을 한데 섞기 때문에 사용자 인터페이스를 변경하는 경우 비즈니스로직까지 변경에 영향을 받게 된다.<br>
따라서 하향식 접근법은 근존적으로 변경에 불안정한 아키텍처를 낳는다.<br>
<br>
### 성급하게 결정된 실행 순서
- 실행 순서를 정의하는 시간 제약(temporal constraint)을 강조한다.<br>
실행 순서나 조건, 반복과 같은 제어 구조를 미리 결정하지 않고는 분해를 진행할 수 없기때문에 분해 방식은 중앙집중 제어 스타일의 형태<br>

- 함수의 제어 구조가 빈번한 변경의 대상<br>
기능이 추가되거나 변경될 때마다 초기에 결정된 함수들의 제어 구조가 올바르지 않다고 판명하여 결과적으로 매번 함수의 제어구조를 변경해야 한다.<br>
→ 해결방안)<br>
논리적 제약을 설계의 기준으로 삼는다.<br>
객체지향은 객체 사이의 논리적인 관계를 중심으로 설계를 이끌어 나가기에 한 구성요소로 제어가 집중되지 않고 여러 객체들 사이로 제어 주체가 분산된다.<br>

- 재사용이 어렵다.<br>
함수가 재사용 가능하려면 상위함수보다 더 일반적이어야 한다.<br>
분해된 하위 함수는 항상 상위 함수보다 문맥에 더 종속적이다.<br>

- 하향식 설계의 모든 문제 원인은 **결합도**다.<br>
함수는 상위 함수가 강요하는 문맥에 강하게 결합된다.<br>
현재의 문맥에 강하게 결합된 시스템은 다른 문맥으로 옮겨갔을 때 재사용하기 힘들다.<br>
<br>

### 데이터 변경으로 인한 파급효과
> 가장 큰 문제점은 어떤 데이터를 어떤 함수가 사용하고 있는지 추적하기 어렵다.

데이터 변경으로 인한 영향을 최소화 하려면 데이터와 함께 변경되는 부분과 그렇지 않은 부분을 명확하게 분리해야한다.<br>
잘 정의된 퍼블릭 인터페이스를 통해 데이터에 대한 접근을 통제해야 한다.<br>
→ 정보 은닉과 모듈<br>
<br>
## 하향식 분해가 유용한 시점
**작은 프로그램과 개별 알고리즘을 위해서는 유용한 패러다임으로 남아있다.** <br>
특히 프로그래밍 과정에서 **_이미 해결된 알고리즘을 문서화하고 서술_** 하는 데는 훌륭한 기법이다.<br>
단, 커다란 소프트웨어를 설계하는 데 적합한 방법은 아니다.<br>
