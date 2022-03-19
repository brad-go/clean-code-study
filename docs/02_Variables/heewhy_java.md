# 의미 있는 이름

## 들어가면서

- 소프트웨어에서 이름은 어디에나 쓰인다.
    - 변수, 함수, 인수, 클래스, 패키지, 소스파일, 디렉터리, ...

## 의도를 분명히 밝혀라

- 의도가 분명한 이름이 정말로 중요

```java
int d; // 경과 시간(단위: 날짜)

int elapsedUmeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

- 코드 이해와 변경이 쉬워 진다.

```java
public List<int[]> getThem(){
	List<int[]> list1 = new ArrayList<int[]>();
	for(int[] x : theList)
		if(x[0] == 4)
			list1.add(x)
	return list1;
}

// 1. theList에 무엇이 들었는가?
// 2. theList에서 0번째 값이 어째서 중요한가?
// 3. 값 4는 무슨 의미인가?
// 4. 함수가 반환하는 리스트 list1을 어떻게 사용하는가?

public List<int[]> getFlaggedCells(){
	List<int[]> flaggedCells = new ArrayList<int[]>();
	for(int[] cell : gameBoard)
		if(cell[STATUS_VALUE] == FLAGGED)
			flaggedCells .add(cell)
	return flaggedCells ;
}

//int[] 대신 간단한 클래스로 구현
//isFlagged라는 좀 더 명시적인 함수를 사용해 감싸기

public List<Cell> getFlaggedCells(){
	List<Cell> flaggedCells = new ArrayList<Cell>();
	for(Cell cell : gameBoard)
		if(cell.isFlagged())
			flaggedCells .add(cell)
	return flaggedCells ;
}
```

## 그릇된 정보를 피하라

- 나름대로 널리 쓰이는 의미가 있는 단어는 다른 의미로 사용해서는 안된다.
    - ex) hp, aix, sco(유닉스 플랫폼)
- 여러 계정을 그룹으로 묶을 때, 실제 List가 아니라면 쓰지 않는다.
    - accountList → Accounts, accountGroup
- 일관성이 떨어지는 표기법은 그릇된 정보다.
- l,1 O,0 을 조심한다.

## 의미 있게 구분하라

- 연속된 숫자를 덧붙이는 방식은 적절치 못하다.

```java
//저자 의도가 전혀 드러나지 않음
public static void copyChars(char a1[], char a2[]){
	for(int i=0; i< a1.length; i++){
		a2[i] = a1[i];
	}
}

//함수 인수 이름으로 source와 destination
public static void copyChars(char source[], char destination[]){
	for(int i=0; i< source.length; i++){
		destination[i] = source[i];
	}
}
```

- 불용어를 추가한 이름 역시 아무런 정보도 제공하지 못한다.
    - ProductInfo, ProductData
    - 불용어는 중복이다
    - 변수 이름에 variable, 테이블 이름에 table (x)
- 읽는 사람이 차이를 알도록 이름을 지어라

## 발음하기 쉬운 이름을 사용하라

- 발음하기 쉬운 이름을 선택 → 코드를 보고 대화를 나누기 위해서

```java
class DtaRcrd102{
	private Date genymdhms;
	private Date modymdhms;
	private final String pszqint = "102";
	/* ... */
};

class Customer{
	private Date generationTimestamp;
	private Date modificationTimestamp;
	private final String recordId= "102";
	/* ... */
};
```

## 검색하기 쉬운 이름을 사용하라

- 이름 길이는 범위 크기에 비례해야 한다

```java
for(int j=0; j<34; j++){
	 += (t[j]*4)/5;
}

int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for( int j=0; j<NUMBER_OF_TASKS; j++){
	int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
	int realTaskWeeks = (realTaksDays / WORK_DAYS_PER_WEEK);
	sum += realTaskWeeks;
}
```

## 인코딩을 피하라

- 발음하기 어려우며 오타가 생기기도 쉽다.
- 헝가리식 표기법
    - 당시는 컴파일러가 타입을 점검하지 않았으므로 프로그래머에게 타입을 기억할 단서가 필요
    - 이제는 방해가 된다
- 멤버 변수 접두어
    - 클래스와 함수는 접두어가 필요없을 정도로 작아야 마땅
    
    ```java
    public class Part{
    	private String m_dsc; //설명 문자열
    	void setName(String name){
    		m_dsc = name;
    	}
    }
    
    public class Part{
    	String description;
    	void setDescription(String description){
    		this.description=description;
    	}
    }
    ```
    
    - 옛날에 작성한 구닥다리 코드라는 징표
- 인터페이스 클래스와 구현 클래스
    - ShapeFactoryImp >> CShapeFactory, IShapeFactory

## 자신의 기억력을 자랑하지 마라

- 루프에서 반복 횟수를 세는 변수 i, j, k 를 제외(l은 사용금지)하고 문자 하나만 사용하는 변수 이름은 문제가 있다.
- 명료함이 최고

 

## 클래스 이름

- 클래스 이름과 객체 이름은 명나사 명사구가 적합
    - Customer, WikiPager

## 메서드 이름

- 메서드 이름은 동사나 동사구가 적합
    - postPayment, deletePage
- 접근자, 변경자, 조건자는 javabean 표준에 따라 get, set, is 를 붙인다.
- 생성자를 중복정의 할 때는 정적 팩토리 메서드를 사용한다.

```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0);

Complex fulcrumPoint = new Complex(23.0;
```

## 기발한 이름은 피하라

- 의도를 분명하고 솔직하게 표현하라

## 한 개념에 한 단어를 사용하라

- 똑같은 메서드를 클래스마다 fetch, retrieve, get으로 제각각 부르면 혼란스럽다.
- 일관성 있는 어휘는 코드를 사용할 프로그래머가 반갑게 여길 선물이다.

## 말장난을 하지 마라

- 같은 맥락이 아닌데도 일관성을 고려해 같은 이름으로 명명한다.
- 의도를 해독할 책임이 독자에게 있는 논문 모델이 아니라 의도를 밝힐 책임이 저자에게 있는 잡지 모델이 바람직하다.

## 해법 영역에서 가져온 이름을 사용하라

- 코드를 읽는 사람도 프로그래머
    - 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 사용
    - 기술 개념에는 기술 이름이 가장 적합한 선택

## 문제 영역에서 가져온 이름을 사용하라

- 적절한 프로그래머 용어가 없다면 문제 영역에서 이름을 가져온다
- 우수한 프로그래머와 설계자라면 해법 영역과 문제 영역을 구분할 줄 알아야한다.

## 의미 있는 맥락을 추가하라

- 클래스, 함수, 이름 공간에 넣어 맥락을 부여한다.
    - 모든 방법이 실패하면 마지막 수단으로 접두어를 붙인다.
    
    ```java
    private void printGuessStateistics(char candidate, int count){
    	String number;
    	String verb;
    	String pluralModifier;
    	if(count ==0){
    		number = "no";
    		verb = "are";
    		pluralModifier = "s";
    	}else if(count ==1){
    		number = "1";
    		verb = "is";
    		pluralModifier = "";
    	} else{
    		number = Integer.toString(count);
    		verb = "are";
    		pluralModifier = "s";
    	}
    
    	String guessMessage = String.format(
    	"There %s %s %s%s",verb,number,candidate,pluralModifier
    	);
    	
    	print(guessMessage);
    }
    
    // 맥락을 분명히 바꾸기
    private class printGuessStateistics{
    	private String number;
    	private String verb;
    	private String pluralModifier;
    	
    	public String make(char candidate, int count){
    		createPluralDependentMessageParts(count);
    		return String.format(
    		"There %s %s %s%s",verb,number,candidate,pluralModifier
    		);
    	}
    
    	private void createPluraDependentMessageParts(int count){
    		if(count ==0){
    			thereAreNoLetters();
    		}else if(count ==1){
    			thereisOneLetter();
    		} else{
    			thereAreManyLetters(count);
    		}
    	}
    
    	private void thereAreManyLetters(int count){
    		number = Integer.toString(count);
    		verb = "are";
    		pluralModifier = "s";
    	}
    	
    	private void thereisOneLetter(){
    		number = "1";
    		verb = "is";
    		pluralModifier = "";
    	}
    
    	pribate void thereAreNoLetters(){
    		number = "no";
    		verb = "are";
    		pluralModifier = "s";
    	}
    }
    ```
    
    ## 불필요한 맥락을 없애라
    
    - 일반적으로는 짧은 이름이 긴 이름보다 좋다. 단, 의미가 분명한 경우에 한해서다. 이름에 불필요한 맥락을 추가하지 않도록 주의한다.
    
    ## Quiz
    
    - 클래스 이름과 메서드 이름은 어떤 구가 들어가야 적합한가?
        
        클래스 : 명사구
        
        메서드 : 동사구
        
    - javabean 표준에서 접근자, 변경자, 조건자는 어떻게 명명할까?
        
        get, set, is