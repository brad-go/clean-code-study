# 함수

> 가장 기본적인 단위가 함수
> 

## 작게 만들어라!

---

- 모든 함수가 2줄, 3줄, 4줄로 표현
- 각 함수가 이야기 하나를 표현

### 블록과 들여쓰기

- if 문/else 문 /while 문 등에 들어가는 블록은 한줄
- 중첩 구조가 생길만큼 함수가 커져서는 안 된다는 뜻

## 한 가지만 해라!

---

> 함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.
> 
- 단순히 다른 표현이 아니라 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.

### 함수 내 섹션

- 한 가지 작업만 하는 함수는 자연스럽게 섹션으로 나누기 어렵다.

## 함수 당 추상화 수준은 하나로!

---

- 한 가지 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.
- 근본 개념과 세부사항을 뒤섞지 않는다.

### 위에서 아래로 코드읽기: 내려가기 규칙

- 코드는 위에서 아래로 이야기처럼 읽혀야 좋다. 한 함수 다음에는 추상화 수준이 한 단계 낮은 함수가 온다.

## Swithch 문

---

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType {
	switch (e.type) { 
		case COMMISSIONED:
			return calculateCommissionedPay(e); 
		case HOURLY:
			return calculateHourlyPay(e); 
		case SALARIED:
			return calculateSalariedPay(e); 
		default:
			throw new InvalidEmployeeType(e.type); 
	}
}
```

- 다형성으로 해결
- switch문은 한 번 정도 허용 (다형성 객체를 생성하는 코드 안에서만)

```java
public abstract class Employee {
	public abstract boolean isPayday();
	public abstract Money calculatePay();
	public abstract void deliverPay(Money pay);
}
-----------------
public interface EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType; 
}
-----------------
public class EmployeeFactoryImpl implements EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
		switch (r.type) {
			case COMMISSIONED:
				return new CommissionedEmployee(r) ;
			case HOURLY:
				return new HourlyEmployee(r);
			case SALARIED:
				return new SalariedEmploye(r);
			default:
				throw new InvalidEmployeeType(r.type);
		} 
	}
}
```

## 서술적인 이름을 사용하라!

---

> “코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 되겠다” - 워드
> 
- 함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다.
- 함수 기능을 잘 표현하는 이름을 선택 → 설계가 뚜렷해짐 → 코드를 개선하기 쉬워짐
- 이름을 붙일 때는 일관성이 있어야 함
    - 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.
    - ex) add, append, plus

## 함수 인수

---

- 함수에서 이상적인 인수 개수는 0개 > 1개 > 2개, 3개 이상 사용하지 않는다.

### 많이 쓰는 단항 형식

- 함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지
    1. 인수에 질문을 던지는 경우
        - `boolean fileExists(“MyFile”);`
    2. 인수를 뭔가로 변환해 결과를 반환하는 경우
        - `InputStream fileOpen(“MyFile”);`
- 이벤트 함수는 조심해서 사용한다.
    - 이벤트라는 사실이 코드에 명확히 드러나도록

### 플래그 인수

- 함수로 부울 값을 넘기는 사례는 피한다.

### 이항, 삼항 함수 함수

- 순서, 주춤, 무시로 야기되는 문제가 2배 이상 늘어나서 위험이 따른다.
- 인수가 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다.
- 인수 개수가 가변적인 함수인 경우 단항, 이항, 삼항 함수로 취급할 수 있다.
- 동사와 키워드
    - 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.
    - ex) `write(name)`
    - 함수 이름에 키워드를 추가하는 형식
    - ex) `assertExceptionEqualsActual(expected,actual)`
    

## 부수 효과를 일으키지 마라!

---

- 함수에서 한 가지가 아닌 작업을 여러 개 할 경우 시간적인 결합이나 순서 종속성을 초래한다.
- 출력 인수
    - 인수를 함수 입력으로 해석 → 인수를 출력으로 사용하는 함수는 어색함
    - 일반적으로 출력 인수는 피한다.
    - 함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택한다.
    

## 명령과 조회를 분리하라!

---

- 명령과 조회를 섞을 경우 개발자는 혼란에 빠진다.
- 명령과 조회를 분리해 혼란을 애초에 뿌리뽑는 방법이 해결책

## 오류 코드보다 예외를 사용하라!

---

- if문에서 명령을 표현식으로 사용하면 여러 단계로 중첩되는 코드를 야기함
- try/catch 문으로 감싼 후 → try/catch문 뽑아내기

```java
if (deletePage(page) == E_OK) {
	if (registry.deleteReference(page.name) == E_OK) {
		if (configKeys.deleteKey(page.name.makeKey()) == E_OK) {
			logger.log("page deleted");
		} else {
			logger.log("configKey not deleted");
		}
	} else {
		logger.log("deleteReference from registry failed"); 
	} 
} else {
	logger.log("delete failed"); return E_ERROR;
}
```

```java
public void delete(Page page) {
	try {
		deletePageAndAllReferences(page);
  	} catch (Exception e) {
  		logError(e);
  	}
}

private void deletePageAndAllReferences(Page page) throws Exception { 
	deletePage(page);
	registry.deleteReference(page.name); 
	configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) { 
	logger.log(e.getMessage());
}
```

- 오류 처리도 한 가지 작업이다. → 분리
- 오류를 처리하는 곳곳에서 오류 코드를 사용한다면 enum class를 쓰게 되는데 이런 클래스는 의존성 자석이므로, 새 오류 코드를 추가하거나 변경할 때 코스트가 많이 필요하다. 그러므로 예외를 사용하는 것이 더 안전하다.

## 반복하지 마라!

---

- 중복은 소프트웨어에서 모든 악의 근원이다.
- 구조적 프로그래밍, AOP, COP, 모두 어떤 면에서 중복 제거 전략이다.

## 구조적 프로그래밍

---

- 함수는 return 문이 하나여야 한다.
- 루프 안에서 break나 continue는 함수를 작게 만들 때만 허용한다.

## 함수를 어떻게 짜죠?

---

1. 초안을 작성한다.
2. 테스트하는 단위 테스트 케이스 생성
3. 코드 다듬기, 함수 생성, 이름 바꾸기, 중복 제거
    - 항상 단위 테스트 통과
4. 메서드를 줄이고 순서 바꾸기

## 결론

---

- 모든 시스템은 특정 응용 분야 시스템을 기술할 목적으로 프로그래머가 설계한 도메인 특화 언어로 만들어진다.
    - 함수는 그 언어에서 동사며, 클래스는 명사다.