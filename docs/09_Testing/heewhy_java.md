# 단위테스트

- 코드가 의도대로 동작 하는지 확인
- 표준 타이밍 함수를 호출하는 대신 운영체제에서 코드를 분리
- 타이밍 함수를 직접 구현해 시간을 완전히 통제
- 테스트 코드도 배포용

## 필요한 배경지식

---

- assert
    - 프로그램에서 버그를 감지하고 수정하는 가장 빠르고 쉬운 방법
    - Assert는 개발/테스트 단계에서 파라미터가 제대로 넘어왔는지 계산이 제대로 됬는지 혹은 특정 메소드가 작동하는 한계상황(Null이 들어오면 작동안함)을 정하는 용도로 사용
    - `assert [Boolean]`
    - 실행될 때 참, 그렇지 않으면 거짓
    
    ```java
    public class JavaAssertCheck {
        public static void main (String args[]){
            String[] names = {"John", "Mary", "David"};
            assert names.length == 2;
            System.out.println("There are "+names.length + "  names in an array");
        }
    }
    ```
    
    - 출력 : `There are 3  names in an array`  → 비 활성화
    - 2면 비 활성화
    - assert 가 사용 가능하고 조건이 거짓이면 JVM은 즉시`AssertionError`
    를 발생시키고 프로그램을 중지
- **assertEquals**
    - expected 값과 actual 값 일치 여부

## TDD 법칙 세 가지

---

1. **첫째 법칙 :** 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. **둘째 법칙 :** 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. **셋째 법칙 :** 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.
- 실제 코드와 맞먹을 정도로 방대한 테스트 코드는 심각한 관리 문제를 유발

## 깨끗한 테스트 코드 유지하기

---

- ‘지저분 해도 빨리’ : 테스트 코드에 실제 코드와 동일한 품질 기준을 적용하지 않은 팀
    - 일회용 테스트 코드를 자기 방식으로 작성
    - 버전이 변경되었을 때 변경하기 어려움 → 테스트 코드 다시 작성
    - 테스트 케이스를 유지하고 보수하는 비용 상승
    - 테스트 슈트 폐기 → 결함율이 높아짐
- 테스트 코드는 실제 코드 못지않게 중요

### 테스트는 유연성, 유지보수성, 재사용성을 제공

- 코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 단위 테스트이다.
- 실제 코드를 점검하는 자동화된 단위 테스트 슈트는 설계와 아키텍처를 최대한 깨끗하게 보존하는 열쇠
- 테스트 케이스가 있으면 변경이 쉬워진다.

## 깨끗한 테스트 코드

---

- 가독성을 높일 때 필요한 세 가지
    - 명료성
    - 단순성
    - 풍부한 표현력
- 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.

```java
// 읽는 사람을 고려하지 않는 코드
public void testGetPageHieratchyAsXml() throws Exception {
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}

public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
  WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  PageData data = pageOne.getData();
  WikiPageProperties properties = data.getProperties();
  WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
  symLinks.set("SymPage", "PageTwo");
  pageOne.commit(data);

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
  assertNotSubString("SymPage", xml);
}

public void testGetDataAsHtml() throws Exception {
  crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");

  request.setResource("TestPageOne"); request.addInput("type", "data");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("test page", xml);
  assertSubString("<Test", xml);
}
```

- 테스트 코드는 진짜 필요한 자료유형과 함수만 사용한다.

### 도메인에 특화된 테스트 언어

```java
//BUILD-OPERATE-CHECK 패턴이 위와 같은 테스트 구조에 적합
//1. 테스트 자료를 생성
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}

//2. 테스트 자료를 조작
public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
  WikiPage page = makePage("PageOne");
  makePages("PageOne.ChildOne", "PageTwo");

  addLinkTo(page, "PageTwo", "SymPage");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
  assertResponseDoesNotContain("SymPage");
}

//3. 테스트 자료의 결과 유효검사
public void testGetDataAsXml() throws Exception {
  makePageWithContent("TestPageOne", "test page");

  submitRequest("TestPageOne", "type:data");

  assertResponseIsXML();
  assertResponseContains("test page", "<Test");
}
```

- 숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩터링해야 마땅하다.

### 이중 표준

- 테스트 API 코드에 적용하는 표준은 실제 코드에 적용하는 표준과 다르다.
    - 단순하고 간결하고 표현릭이 풍부해야하지만 실제 코드 만큼 효율적일 필요는 없다.

```java
//상태 이름과 상태 값을 확인하느라 가독성이 낮아짐
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
  hw.setTemp(WAY_TOO_COLD); 
  controller.tic(); 
  assertTrue(hw.heaterState());   
  assertTrue(hw.blowerState()); 
  assertFalse(hw.coolerState()); 
  assertFalse(hw.hiTempAlarm());       
  assertTrue(hw.loTempAlarm());
}
```

```java
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
//tic 함수를 wayTooCold에 숨김
  wayTooCold();
//대문자는 '켜짐'이고 소문자는 '꺼짐'
  assertEquals("HBchL", hw.getState()); 
}
```

```java
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
  tooHot();
  assertEquals("hBChl", hw.getState()); 
}
  
@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
  tooCold();
  assertEquals("HBchl", hw.getState()); 
}

@Test
public void turnOnHiTempAlarmAtThreshold() throws Exception {
  wayTooHot();
  assertEquals("hBCHl", hw.getState()); 
}

@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
  wayTooCold();
  assertEquals("HBchL", hw.getState()); 
}
```

- StringBuffer를 피한다.
    - 실제 환경에서는 문제가 될 수 있지만 테스트 환경은 자원이 제한적일 가능성이 낮다.
    - 메모리나 CPU 효율과 관련 있는 경우 → 코드의 깨끗함과는 철저히 무관하다.(테스트 환경)
    
    ```java
    public String getState() {
      String state = "";
      state += heater ? "H" : "h"; 
      state += blower ? "B" : "b"; 
      state += cooler ? "C" : "c"; 
      state += hiTempAlarm ? "H" : "h"; 
      state += loTempAlarm ? "L" : "l"; 
      return state;
    }
    ```
    

## 테스트 당 assert 하나

---

- JUnit으로 테스트 코드를 짤 때 함수마다 assert를 단 하나만 사용

```java
//테스트를 쪼개 각자가 assert를 수행
//given-when-then 이라는 관례를 사용
public void testGetPageHierarchyAsXml() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldBeXML(); 
}

public void testGetPageHierarchyHasRightTags() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldContain(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  ); 
}
```

- **하지만 불행하게도 위에서 보듯이 테스트를 분리하면 중복되는 코드가 많아진다.**
- 최고의 개념은 ****테스트 당 개념 하나****

### 테스트 당 개념 하나

```java
public void testAddMonths() {
	//5월처럼 31일로 끝나는 달의 마지막 날짜가 주어지는 경우
  SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

	//6월 처럼 30일로 끝나는 한 달을 더하면 날짜는 30일이 되어야지 31일이 되어서는 안됨
  SerialDate d2 = SerialDate.addMonths(1, d1); 
  assertEquals(30, d2.getDayOfMonth()); 
  assertEquals(6, d2.getMonth()); 
  assertEquals(2004, d2.getYYYY());
  
  SerialDate d3 = SerialDate.addMonths(2, d1); 
  assertEquals(31, d3.getDayOfMonth()); 
  assertEquals(7, d3.getMonth()); 
  assertEquals(2004, d3.getYYYY());
  
  SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1)); 
  assertEquals(30, d4.getDayOfMonth());
  assertEquals(7, d4.getMonth());
  assertEquals(2004, d4.getYYYY());
}
```

- **가장 좋은 규칙은 "개념 당 assert 문 수를 최소로 줄여라"와 "테스트 함수 하나는 개념 하나만 테스트하라"**

## F.I.R.S.T

---

> **빠르게Fast :** 테스트는 빨라야 한다. 테스트는 빨리 돌아야 한다는 말이다. 테스트가 느리면 자주 돌릴 엄두를 못 낸다. 자주 돌리지 않으면 초반에 문제를 찾아내 고치지 못한다. 코드를 마음껏 정리하지도 못한다. 결국 코드 품질이 망가지기 시작한다.
> 

> **독립적으로Independent :** 각 테스트를 서로 의존하면 안 된다. 한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안 된다. 각 테스트는 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다. 테스트가 서로에게 의존하면 하나가 실패할 때 나머지도 잇달아 실패하므로 원인을 진단하기 어려워지며 후반 테스트가 찾아내야 할 결함이 숨겨진다.
> 

> **반복가능하게Repeatable :** 테스트는 어떤 환경에서도 반복 가능해야 한다. 실제 환경, QA 환경, 버스를 타고 집으로 가는 길에 사용하는 노트북 환경(네트워크가 연결되지 않은)에서도 실행할 수 있어야 한다. 테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다. 게다가 환경이 지원되지 않기에 테스트를 수행하지 못하는 상황에 직면한다.
> 

> **자가검증하는Self-Validating :** 테스트는 bool값으로 결과를 내야 한다. 성공 아니면 실패다. 통과 여부를 알리고 로그 파일을 읽게 만들어서는 안 된다. 통과 여부를 보려고 텍스트 파일 두 개를 수작업으로 비교하게 만들어서도 안 된다. 테스트가 스스로 성공과 실패를 가늠하지 않는다면 판단은 주관적이 되며 지루한 수작업 평가가 필요하게 된다.
> 

> **적시에Timely :** 테스트는 적시에 작성해야 한다. 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다. 실제 코드를 구현한 다음에 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다는 사실을 발견할지도 모른다. 어떤 실제 코드는 테스트하기 너무 어렵다고 판명날지 모른다. 테스트가 불가능하도록 실제 코드를 설계할지도 모른다.
> 

## 결론

---

- 테스트 코드는 실제 코드만큼이나 프로젝트 건강에 중요
- 지속적으로 깨끗하게 관리
- 표현력을 높이고 간결하게 정리
- 테스트 API를 구현해 도메인 특화 언어를 만들자

## QUIZ

---

- 가독성을 높일 때 필요한 세 가지
    - 명료성
    - 단순성
    - 풍부한 표현력
- F.I.R.S.T 를 각각 말하시오.
    - Fast
    - Independent
    - Repeatable
    - **Self-Validating**
    - **Timely**