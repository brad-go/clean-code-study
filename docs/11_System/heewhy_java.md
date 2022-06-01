# 시스템

## 배경지식

---

## DI(DependencyInjection)

### 의존성 주입

- 제어 역전 기법을 의존성 관리에 적용한 메커니즘
- 한 객체가 맡은 보조 책임을 새로운 객체에게 전적으로 떠넘긴다.

### 의존관계란?

- A 클래스에서 B클래스의 객체를 멤버 변수로 가지고 있거나 B클래스 객체의 메소드를 호출하는 관계일 때, A객체는 B객체에 의존한다 라고 함

### DI란?

- 의존하는 객체 정보를 주입
- 객체를 생성하거나 객체 정보를 할당받는 코드를 없애주고 스프링 컨테이너에 의해 객체정보를 할당 받음

### DI 장점

- 사용자와 구현 객체 간의 결합도를 더욱 더 낮춰준다.
    - 유지보수 용이

## AbstractPattern

- 다양한 구성 요소 별로 객체의 집합을 생성해야 할 때 유용

## Bean

### Bean이란?

- Spring 컨테이너에서 관리하는 자바 객체
- 의존성 주입은 스프링 컨테이너에서 해주는 일이므로 스프링 컨테이너에선 어떤 객체를 주입해야하는 정보를 갖고 있어야 함
    - 이를 위한 것이 빈 등록
        - 스프링 컨테이너 자바 객체 정보를 등록하는 것

### Bean 등록 방법

1. XML
2. Annotation
3. Java Config

# AOP(Aspect Oriented Programming) - 관점 지향 프로그래밍

## AOP란?

- 핵심 로직과 공통 관심 로직을 분리

## 용어

- JointPoint
    - 공통 로직이 적용될 수 있는 위치
- PointCut
    - 어디에 공통 로직을 적용할 지
- Advice
    - 언제 공통 로직을 수행할 지
    - 종류
        - @Before - 메소드 실행 전
        - @After - 메소드 실행 후 (메소드 실행결과와 상관없이 수행)
        - @AfterReturning - 메소드 실행 후(메소드가 성공적으로 수행 됐을 때만 수행)
        - @AfterThrowing - 메소드 수행 중 예외 발생 시
        - @Around
            - 메소드 실행 전과 실행 후를 아우름
            - ProceedingJoinPoint 객체를 통해 proceed() 수행
                - 해당 메소드 수행 전과 후에 특정한 처리 수행 가능
- 위빙(weaving)
    - 지정된 클래스에 새로운 내용을 추가해서 Proxy 객체를 생성하는 과정

### AOP 장점

- 중복 코드 제거
- 유지보수 용이

## DSL(도메인 특화 언어)

- 특정한 도메인을 적용하는데 특화된 언어
- HTML

### DSL 장점

- 도메인 특화 언어는 도메인 수준에서 검증, 확인이 가능하다. 언어의 구조가 안정적이라면, 그 언어에서 쓰여진 문장은 그 분야의 사람들이 이해하는데 불편함이 없다.
- 도메인 특화 언어는 비즈니스 정보 체계의 개발을 전통적인 소프트웨어 개발자들에게서 도메인에 깊은 지식을 가지고 있는 더 큰 도메인 전문가 그룹으로 옮기는데 도움을 준다.

### DSL 단점

- 새로운 언어를 배워야 한다는 초기 비용과 매우 좁은 적용분야.
- 도메인 특화 언어를 설계, 구현, 유지 하는데 드는 비용. 또한 그것으로 개발하기 위한 툴 개발 비용.

## 도시를 세운다면?

---

- 도시가 돌아가는 이유는 적절한 추상화와 모듈화 때문이다.
- 큰 그림을 이해하지 못할지라도 개인과 개인이 관리하는 구성 요소는 효율적으로 돌아간다.

## 시스템 제작과 시스템 사용을 분리하라

---

- 제작은 사용과 다르다.
- 소프트웨어 시스템
    - 준비 과정 : 애플리케이션 객체를 제작하고 의존성을 서로 연결하는 과정
    - 런타임 로직
- 시작 단계는 모든 애플리케이션이 풀어야 할 관심사 ⇒ 관심사 분리

```java
//초기화 지연 or 계산 지연
public Service getService() {
      if (service == null)
          service = new MyServiceImpl(...); // Good enough default for most cases?
      return service;
  }
```

- 장점 
1. 실제로 필요할 때까지 객체를 생성하지 않으므로 불필요한 부하가 걸리지 않는다
2. 애플리케이션을 시작하는 시간이 그만큼 빨라진다.
3. 어떤 경우에도 null 포인터를 반환하지 않는다.
- 단점
    1. 런타임 로직에서 MyServiceImpl 객체를 전혀 사용하지 않더라도 의존성을 해결하지 않으면 해결이 안 된다.
    2. MyServiceImpl 이 무거운 객체라면 단위 테스트에서 getService 메서드를 호출하기 전에 적절한 테스트 전용 객체를 service 필드에 할당해야 한다.
    3. 모든 실행 결로도 테스트 해야한다.
    4. 단일 책임 원칙을 위반한다.
    5. 모듈성은 저조하며 대개 중복이 심각하다.

### Main 분리

- 생성과 관련한 코드는 모두 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정

![Untitled](%E1%84%89%E1%85%B5%E1%84%89%E1%85%B3%E1%84%90%E1%85%A6%E1%86%B7%20aa28564ff7a3403796215ffa576fa0d4/Untitled.png)

- main 함수에서 시스템에 필요한 객체를 생성한 후 이를 애플리케이션에 넘긴다.
- 애플리케이션은 main이나 객체가 생성되는 과정을 전혀 모른다.

### 팩토리

- 객체가 생성되는 시점을 애플리케이션이 결정할 필요도 생긴다.
- ABSTRACT FACTORY 패턴을 사용한다.

![Untitled](%E1%84%89%E1%85%B5%E1%84%89%E1%85%B3%E1%84%90%E1%85%A6%E1%86%B7%20aa28564ff7a3403796215ffa576fa0d4/Untitled%201.png)

### 확장

- 애자일 방식
    - 반복적이고 점진적이다.
    - 테스트 주도 개발, 리팩터링으로 얻어지는 깨끗한 코드는 코드 수준에서 시스템을 조정하고 확장하기 쉽게 만든다.

```java
package com.example.banking;
import java.util.Collections;
import javax.ejb.*;

public interface BankLocal extends java.ejb.EJBLocalObject {
    String getStreetAddr1() throws EJBException;
    String getStreetAddr2() throws EJBException;
    String getCity() throws EJBException;
    String getState() throws EJBException;
    String getZipCode() throws EJBException;
    void setStreetAddr1(String street1) throws EJBException;
    void setStreetAddr2(String street2) throws EJBException;
    void setCity(String city) throws EJBException;
    void setState(String state) throws EJBException;
    void setZipCode(String zip) throws EJBException;
    Collection getAccounts() throws EJBException;
    void setAccounts(Collection accounts) throws EJBException;
    void addAccount(AccountDTO accountDTO) throws EJBException;
}
```

```java
package com.example.banking;
import java.util.Collections;
import javax.ejb.*;

public abstract class Bank implements javax.ejb.EntityBean {
    // Business logic...
    public abstract String getStreetAddr1();
    public abstract String getStreetAddr2();
    public abstract String getCity();
    public abstract String getState();
    public abstract String getZipCode();
    public abstract void setStreetAddr1(String street1);
    public abstract void setStreetAddr2(String street2);
    public abstract void setCity(String city);
    public abstract void setState(String state);
    public abstract void setZipCode(String zip);
    public abstract Collection getAccounts();
    public abstract void setAccounts(Collection accounts);
    
    public void addAccount(AccountDTO accountDTO) {
        InitialContext context = new InitialContext();
        AccountHomeLocal accountHome = context.lookup("AccountHomeLocal");
        AccountLocal account = accountHome.create(accountDTO);
        Collection accounts = getAccounts();
        accounts.add(account);
    }
    
    // EJB container logic
    public abstract void setId(Integer id);
    public abstract Integer getId();
    public Integer ejbCreate(Integer id) { ... }
    public void ejbPostCreate(Integer id) { ... }
    
    // The rest had to be implemented but were usually empty:
    public void setEntityContext(EntityContext ctx) {}
    public void unsetEntityContext() {}
    public void ejbActivate() {}
    public void ejbPassivate() {}
    public void ejbLoad() {}
    public void ejbStore() {}
    public void ejbRemove() {}
}
```

- 문제점
    1. 비지니스 로직이 EJB2 컨테이너에 타이트하게 연결되어 있다. Entity를 만들기 위해 컨테이너 타입을 subclass하고 필요한 lifecycle 메서드를 구현해야 한다.
    2. 실제로 사용되지 않을 테스트 객체의 작성을 위해 mock 객체를 만드는 데에도 무의미한 노력이 많이 든다. EJB2 구조가 아닌 다른 구조에서 재사용할 수 없는 컴포넌트를 작성해야 한다.
    3. OOP 또한 등한시되고 있다. 상속도 불가능하며 쓸데없는 DTO(Data Transfer Object)를 작성하게 만든다.

### 횡단(cross-cutting) 관심사

- 이론적으로는 독립된 형태로 구분될 수 있지만 실제로는 코드에 산재하기 쉬운 부분들을 뜻한다.

### 자바 프록시

---

```java
// Bank.java (suppressing package names...)
import java.utils.*;

// The abstraction of a bank.
public interface Bank {
    Collection<Account> getAccounts();
    void setAccounts(Collection<Account> accounts);
}

// BankImpl.java
import java.utils.*;

// The “Plain Old Java Object” (POJO) implementing the abstraction.
public class BankImpl implements Bank {
    private List<Account> accounts;

    public Collection<Account> getAccounts() {
        return accounts;
    }
    
    public void setAccounts(Collection<Account> accounts) {
        this.accounts = new ArrayList<Account>();
        for (Account account: accounts) {
            this.accounts.add(account);
        }
    }
}
// BankProxyHandler.java
import java.lang.reflect.*;
import java.util.*;

// “InvocationHandler” required by the proxy API.
public class BankProxyHandler implements InvocationHandler {
    private Bank bank;
    
    public BankHandler (Bank bank) {
        this.bank = bank;
    }
    
    // Method defined in InvocationHandler
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String methodName = method.getName();
        if (methodName.equals("getAccounts")) {
            bank.setAccounts(getAccountsFromDatabase());
            
            return bank.getAccounts();
        } else if (methodName.equals("setAccounts")) {
            bank.setAccounts((Collection<Account>) args[0]);
            setAccountsToDatabase(bank.getAccounts());
            
            return null;
        } else {
            ...
        }
    }
    
    // Lots of details here:
    protected Collection<Account> getAccountsFromDatabase() { ... }
    protected void setAccountsToDatabase(Collection<Account> accounts) { ... }
}

// Somewhere else...
Bank bank = (Bank) Proxy.newProxyInstance(
    Bank.class.getClassLoader(),
    new Class[] { Bank.class },
    new BankProxyHandler(new BankImpl())
);
```

1. Java Proxy API를 위한 Bank 인터페이스를 작성한다.
2. 위에서 작성한 Bank 인터페이스를 사용한 BankImpl(POJO aka Plane Old Java Object)를 구현한다. 여기에는 순수한 데이터만 들어가며 비지니스 로직은 포함되지 않는다.(모델과 로직의 분리)
3. InvocationHandler를 구현하는 BankProxyHandler를 작성한다. 이 핸들러는 Java Reflection API를 이용해 Bank 인터페이스를 구현하는 객체들의 메서드콜을 가로챌 수 있으며 추가적인 로직을 삽입할 수 있다. 본 예제에서 비지니스 로직(persistant stack logic)은 이 곳에 들어간다.
4. 마지막으로 코드의 마지막 블럭과 같이 BankImpl 객체를 BankProxyHandler에 할당, Bank 인터페이스를 사용해 프록시된 인터페이스를 사용해 모델과 로직이 분리된 코드를 작성할 수 있다. 이로써 모델과 로직의 분리를 이뤄낸 코드를 작성할 수 있게 되었다.
- 단점 : 코드 양과 크기

## 순수 자바 AOP 프레임 워크

---

```java
<beans>
    ...
    <bean id="appDataSource"
        class="org.apache.commons.dbcp.BasicDataSource"
        destroy-method="close"
        p:driverClassName="com.mysql.jdbc.Driver"
        p:url="jdbc:mysql://localhost:3306/mydb"
        p:username="me"/>
    
    <bean id="bankDataAccessObject"
        class="com.example.banking.persistence.BankDataAccessObject"
        p:dataSource-ref="appDataSource"/>
    
    <bean id="bank"
        class="com.example.banking.model.Bank"
        p:dataAccessObject-ref="bankDataAccessObject"/>
    ...
```

![Untitled](%E1%84%89%E1%85%B5%E1%84%89%E1%85%B3%E1%84%90%E1%85%A6%E1%86%B7%20aa28564ff7a3403796215ffa576fa0d4/Untitled%202.png)

- Bank객체는 BankDataAccessObject가, BankDataAccessObject는 BankDataSource가 감싸 프록시하는 구조로 되어 각각의 bean들이 러시아 인형의 한 부분처럼 구성되었다. 클라이언트는 Bank에 접근하고 있다고 생각하지만 사실은 가장 바깥의 BankDataSource에 접근하고 있는 것이다.

```java
XmlBeanFactory bf = new XmlBeanFactory(new ClassPathResource("app.xml", getClass()));
Bank bank = (Bank) bf.getBean("bank");
```

## 테스트 주도 시스템 아키텍처 구축

---

- 코드 수준에서 아키텍처 관심사 분리 → 테스트 주도 아키텍처 구축
- 소프트웨어 역시 나름대로 형체가 있지만, 소프트웨어 구조가 관점을 효과적으로 분리한다면 극적인 변화가 경제적으로 가능하다. → 확장성

## Quiz

- 코드 수준에서 시스템을 조정하고 확장하기 쉽게 만드는 방법은?
    - 테스트 주도 개발, 리팩터링으로 얻어지는 깨끗한 코드
- 반복적이고 점진적으로 단계를 정해놓고 확장하는 개발 방식은?
    - 애자일 방식