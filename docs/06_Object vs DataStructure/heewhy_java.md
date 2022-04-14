# 객체와 자료구조

> 변수를 비공개로 정의하는 이유 : 남들이 변수에 의존하지 않게 만들고 싶어서
Q. 그렇다면 get(조회), set(설정) 함수를 당연하게 공개해 비공개 변수를 외부에 노출하는 이유?
> 
- 나의 의견 : 내가 설계한 변수의 범위, 값으로 제어하기 위해서 (예외처리를 위해서)

```java
public class Person{

	private String name;
	private int age;
	
	public Person(String name, int age) {
		setName(name);
		setAge(age);
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	
	//나이가 음수일 순 없음
	//생성자에 추가하기엔 너무 멤버변수가 많다면 길어짐
	public void setAge(int age) {
		this.age = age;
	}
```

## 자료 추상화

---

- 인터페이스는 자료구조를 명백하게 표현
- 구현을 감추려면 추상화가 필요

```java
//구체적
public interface Vehicle{
	double getFuelTankCapacityInGallons();
	double getGallonsOfGasoline();
}

//추상적
public interface Vehicle{
	double getPercentFuelRemaining();
}
```

- 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 클래스

```java
Stack<Integer> SList = new Stack<Integer>();
LinkedList<Integer> LList = new LinkedList<Integer>();

SList.push(1);
SList.pop();

LList.offer(1);
LList.poll();

List<Integer> list = new new Stack<Integer>();
List<Integer> list = new LinkedList<Integer>();

list.add(1);
list.remove(1);
```

## 자료/객체 비대칭

---

- (자료 구조를 사용하는) 절차적인 코드는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다
- 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
- 객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며 절차적인 코드에서 어려운 변경은 객체 지향 코드에서 쉽다.

```java
/*
새 함수를 추가하기는 쉽지만,
새 도형을 추가하려면 Geometry 클래스 전부를 고쳐야 한다.
*/
public class Square { 
  public Point topLeft; 
  public double side;
}

public class Rectangle { 
  public Point topLeft; 
  public double height; 
  public double width;
}

public class Circle { 
  public Point center; 
  public double radius;
}

public class Geometry {
  public final double PI = 3.141592653589793;
  
  public double area(Object shape) throws NoSuchShapeException {
    if (shape instanceof Square) { 
      Square s = (Square)shape; 
      return s.side * s.side;
    } else if (shape instanceof Rectangle) { 
      Rectangle r = (Rectangle)shape; 
      return r.height * r.width;
    } else if (shape instanceof Circle) {
      Circle c = (Circle)shape;
      return PI * c.radius * c.radius; 
    }
    throw new NoSuchShapeException(); 
  }
}
```

```java
/*
새 도형을 추가하기는 쉽지만,
새 함수를 추가하려면 도형 클래스 전부를 고쳐야 한다.
*/
public class Square implements Shape { 
  private Point topLeft;
  private double side;
  
  public double area() { 
    return side * side;
  } 
}

public class Rectangle implements Shape { 
  private Point topLeft;
  private double height;
  private double width;

  public double area() { 
    return height * width;
  } 
}

public class Circle implements Shape { 
  private Point center;
  private double radius;
  public final double PI = 3.141592653589793;

  public double area() {
    return PI * radius * radius;
  } 
}
```

## 디미터 법칙

---

- 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙
    - Class C
    - f가 생성한 객체
    - f 인수로 넘어온 객체
    - C 인스턴스 변수에 저장된 객체

### 기차 충돌 X

- `final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();`

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

- 자료 구조는 무조건 함수 없이 공개 변수만 포함, 객체는 비공개 변수와 공개 함수를 포함

### 잡종 구조 X

- 절반은 객체, 절반은 자료 구조인 구조
- 프로그래머가 함수나 타입을 보호할지 공개할지 확신하지 못해 어중간하게 내놓은 설계

## 자료 전달 객체

---

- DTO(Data Transfer Object)
- 공개 변수만 있고 함수가 없는 클래스
- DB와 통신할 때 유용

```java
public class Address { 
  public String street; 
  public String streetExtra; 
  public String city; 
  public String state; 
  public String zip;
}
```

### 활성 레코드

- DTO의 특수한 형태
- 공개 변수가 있거나 비공개 변수에 조회/설정 함수가 있는 자료 구조
- save / find 같은 탐색 함수도 제공
- 바람직하지 않으므로 자료구조를 취급

 

## 결론

---

- 객체는 동작을 공개하고 자료를 숨긴다.
- 자료 구조는 별다른 동작 없이 자료를 노출 한다.
- 새로운 자료 타입을 추가하는 유연성이 필요 → 객체
- 새로운 동작을 추가하는 유연성이 필요 → 자료구조
- 직면한 문제에 최적인 해결책을 선택

## Quiz

- 디미터 법칙이란?
    - 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙
- 객체 지향 코드와 (자료구조를 사용하는) 절차적인 코드에서 추가하기 쉬운 부분은 무엇일까요?
    - 객체 지향 코드 : 새로운 자료 타입
    - 절차적인 코드 : 새로운 동작