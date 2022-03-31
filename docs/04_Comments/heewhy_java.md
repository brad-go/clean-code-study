# 주석

> 나쁜 코드에 주석을 담지마라. 새로 짜라 - 브라이언 W> 커니핸, P.J.플라우거
> 
- 잘 달린 주석은 어떤 정보보다 유용
- 주석은 오래될수록 코드에서 멀어짐
    - 주석을 유지하고 보수하기란 현실적으로 불가능
- 주석이 필요 없는 방향으로 가능한 줄이도록 꾸준히 노력

## 주석은 나쁜 코드를 보완하지 못한다

---

- 코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문

## 코드로 의도를 표현하라!

---

```java
//직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if((employee.flags & HOURLY_FLAG) && (employee.age >65))

if(employee.isEligibleForFullBenefits())
```

- 함수 명으로 의도를 표현하면 주석을 달 필요가 없다.

## 좋은 주석

---

- 좋은 주석은 주석을 달지 않을 방법을 찾아낸 주석

### 법적인 주석

- 각 소스 파일 첫머리에 주석으로 들어가는 저작권 정보와 소유권 정보는 필요하고도 타당하다.
- `// Copyright (C) 2003, 2004, 2005 by Object Montor, Inc. All right reserved.`
- `// GNU General Public License`

### 정보를 제공하는 주석

- 기본적인 정보를 주석으로 제공
    - ex) 추상메서드가 반환할 값을 설명
    
    ```java
    // 테스트 중인 Responder 인스턴스를 반환
    protected abstract Responder responderInstance();
    ```
    
    - 가능하다면 함수 이름에 정보를 담자

### 의도를 설명하는 주석

- 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명

```java
// 스레드를 대량 생성하는 방법으로 어떻게든 경쟁 조건을 만들려 시도한다. 
for (int i = 0; i > 2500; i++) {
    WidgetBuilderThread widgetBuilderThread = 
        new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
    Thread thread = new Thread(widgetBuilderThread);
    thread.start();
}
```

### 결과를 경고하는 주석

- 다른 프로그래머에게 결과를 경고하는 목적으로 주석 사용

```java
// 여유 시간이 충분하지 않다면 실행하지 마십시오.
public void _testWithReallyBigFile() {}
```

### TODO 주석

- 앞으로 구성할 계획을 주석으로 표시해두고 작성하며 지우기
- 동료와의 협업 시 소통의 주석
    - 더 이상 필요 없는 기능을 삭제하라는 알림
    - 누군가에게 문제를 봐달라는 요청
    - 더 좋은 이름을 떠올려달라는 부탁
    - 앞으로 발생할 이벤트에 맞춰 코드를 고치라는 주의

### 중요성을 강조하는 주석

- 자칫 대수롭지 않다고 여겨질 뭔가의 중요성을 강조

```java
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다. 
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

### 공개 API에서의 Javadocs

- 공개 API를 구현한다면 반드시 훌륭한 Javadocs를 작성
- Javadocs
    - Java로 만든 어플리케이션(또는 서비스)의 소스를 협업자 또는 제3자에게 제공하기 위해 주석을 달 때 JavaDoc의 도움을 받아 좀 더 체계적으로 작업할 수 있도록 하기 위한 주석법
    - /** 로 시작해서 */ 로 끝난다.
    
    ```java
    /**
    * @author 클래스나 인터페이스의 제작자 표시
    * @version 클래스나 인터페이스에서의 버전 정보
    * ...
    ```
    
    ```java
    /**
    * 메서드가 하는 일
    * @param 어떤 값을 가진 파라미터 인지
    * @return 어떤 값을 리턴하는 지 
    ```
    

## 나쁜 주석

---

- 대다수의 주석
    - 허술한 코드를 지탱
    - 엉성한 코드를 변명
    - 미숙한 결정을 합리화

### 주절거리는 주석

- 특별한 이유 없이 달리는 주석

```java
public void loadProperties() {
    try {
        String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
        FileInputStream propertiesStream = new FileInputStream(propertiesPath);
        loadedProperties.load(propertiesStream);
    } catch (IOException e) {
        // 속성 파일이 없다면 기본값을 모두 메모리로 읽어 들였다는 의미다. 
    }
}
```

- 주석을 달기로 결정했다면 충분한 시간을 들여 최고의 주석을 달도록 노력

### 같은 이야기를 중복하는 주석

- 코드 내용을 그대로 중복

```java
// this.closed가 true일 때 반환되는 유틸리티 메서드다.
// 타임아웃에 도달하면 예외를 던진다. 
public synchronized void waitForClose(final long timeoutMillis) throws Exception {
    if (!closed) {
        wait(timeoutMillis);
        if (!closed) {
            throw new Exception("MockResponseSender could not be closed");
        }
    }
}
```

- 변수마다 주석은 피한다. → 변수 명에 정보 담기

### 오해할 여지가 있는 주석

- `// this.closed가 true일 때 반환되는 유틸리티 메서드다.`
- this.closed가 true로 변하는 순간에 메서드는 반환되지 않는다.

### 의무적으로 다는 주석

- 모든 함수에 Javadocs를 달거나 모든 변수에 주석을 달아야 한다는 규칙은 어리석기 그지없다.

```java
/**
 *
 * @param title CD 제목
 * @param author CD 저자
 * @param tracks CD 트랙 숫자
 * @param durationInMinutes CD 길이(단위: 분)
 */
public void addCD(String title, String author, int tracks, int durationInMinutes) {
    CD cd = new CD();
    cd.title = title;
    cd.author = author;
    cd.tracks = tracks;
    cd.duration = durationInMinutes;
    cdList.add(cd);
}
```

### 이력을 기록하는 주석

- 소스 코드 관리 시스템을 이용한다. → 완전히 제거

```java
* 변경 이력 (11-Oct-2001부터)
* ------------------------------------------------
* 11-Oct-2001 : 클래스를 다시 정리하고 새로운 패키징
* 05-Nov-2001: getDescription() 메소드 추가
* 이하 생략
```

- 소스 코드 관리 시스템(VCS,SCM) `ex)git`
    - 협업을 위한 코드 공유
    - 접근 제한
    - 다양한 버전 관리
    - 특정 시점 추적
    - 변경 추적

### 있으나 마나 한 주석

```java
/*
 * 기본 생성자
 */
protected AnnualDateRule() {}
```

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

```java
// 전역 목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (module.getDependSubsystems().contains(subSysMod.getSubSystem()))

//변경
ArrayList moduleDependencies = smodule.getDependSubSystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))
```

### 위치를 표시하는 주석

- `// Actions —————————————————-`

### 닫는 괄호에 다는 주석

```java
main(){
	for tc: TestCase(
	 //내용
	)//End tc
}//End main
```

### 공로를 돌리거나 저자를 표시하는 주석

- 소스 코드 관리 시스템은 변경 추적 가능
- `/*릭이 추가함 */`

### 주석으로 처리한 코드

```java
this.bytePos = writeBytes(pngIdBytes, 0);
//hdrPos = bytePos;
writeHeader();
writeResolution();
//dataPos = bytePos;
if (writeImageData()) {
    wirteEnd();
    this.pngBytes = resizeByteArray(this.pngBytes, this.maxPos);
} else {
    this.pngBytes = null;
}
return this.pngBytes;
```

- 다른 개발자가 중요한 코드로 착각하고 지우기 어려움 → 쌓여서 나쁜 코드가 만들어짐
- 소스 코드 관리 시스템의 버전 관리를 이용

### HTML 주석

- 주석에 HTML 태그를 삽입해야 하는 책임은 프로그래머가 아니라 도구가 져야 한다.

### 전역정보

```java
/**
 * 적합성 테스트가 동작하는 포트: 기본값은 <b>8082</b>.
 *
 * @param fitnessePort
 */
public void setFitnessePort(int fitnessePort) {
    this.fitnewssePort = fitnessePort;
}
```

- 주석을 달아야 한다면 근처에 있는 코드만 기술

### 너무 많은 정보

- 주석에 흥미로운 역사나 관련 없는 정보를 담지 않기

### 모호한 관계

- 주석과 주석이 설명하는 코드는 둘 사이 관계가 명확해야한다.

### 함수 헤더

- 짧은 함수는 긴 설명이 필요가 없고 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 훨씬 좋다.

### 비공개 코드에서 Javadocs

- 공개 API가 아니라면 가독성이 떨어진다.

## Quiz

- Javadocs는 어떠한 때에 사용해야 할까요?
    - 공개 API
- 소스 코드 관리 시스템의 기능을 말해보세요.
    - 협업을 위한 코드 공유
    - 접근 제한
    - 다양한 버전 관리
    - 특정 시점 추적
    - 변경 추적