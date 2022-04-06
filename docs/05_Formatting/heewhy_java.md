# 형식 맞추기

> 프로그래머라면 규칙을 정해서 형식을 깔끔하게 맞춰 코드를 짜야 한다.
팀으로 일한다면 팀이 합의해 규칙을 정하고 모두가 그 규칙을 따라야 한다.
> 

## 형식을 맞추는 목적

---

- 코드 형식은 의사소통의 일환이다.
- 버전이 바뀌어도 초기의 스타일과 가독성 수준은 유지보수 용이성과 확장성에 영향을 미친다.

 

## 적절한 자바 소스 파일의 크기 : 평균 65줄

- 500줄을 넘지 않고 대부분 200줄 정도인 파일로도 커다란 시스템을 구축할 수 있다.

## 적절한 행 길이 : 평균 45자, 120자 제한

## 신문 기사처럼 작성하라

---

1. 메서드 이름 = 신문 기사 제목
    - 이름은 간단하면서도 설명이 가능하게 짓는다.
2. 메서드 내용 시작 부분
    - 첫 부분은 고차원 개념과 알고리즘을 설명한다.
    - 아래로 내려갈수록 의도를 세세하게 묘사한다.
3. 메서드 내용 끝 부분
    - 가장 저 차원 함수와 세부 내역이 나온다.

## 개념은 빈 행으로 분리하라.

---

- 각 행은 수식이나 절을 나타내고, 일련의 행 묶음은 완결된 생각 하나를 표현한다.

## 세로 밀집도 : 함수 호출 종속성을 아래 방향으로 유지

---

- 서로 밀접한 코드 행은 세로로 가까이 놓여야 한다.
    - protected 변수를 피해야 하는 이유
    - protected : 자식 클래스가 아닌 다른 패키지에 소속된 클래스에 접근 제한
- 변수 선언
    - 사용하는 위치에 최대한 가까이 선언
- 인스턴스 변수
    - 클래스 맨 처음에 선언
    - C++ : 가위규칙(클래스 마지막에 선언)
    - Java : 클래스 맨 처음
- 종속 함수
    - 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.
        - 호출되는 함수를 찾기가 쉬워져 모듈 전체의 가독성 향상
- 개념적 유사성
    - 개념적인 친화도가 높을수록 가까이 배치한다.

## 가로 형식 맞추기

---

- 가로 공백과 밀집도
    - 할당 연산자를 강조하는 경우
    - 괄호 안 인수
    - 연산자 우선순위 강조  `return b*b - 4*a*c;`
    
    ```java
    private void measureLine(String line) { 
    	lineCount++;
    	
    	//할당 연산자 강조
    	int lineSize = line.length();
    	totalChars += lineSize; 
    	
    	//괄호 안 인수
    	lineWidthHistogram.addLine(lineSize, lineCount);
    	recordWidestLine(lineSize);
    }
    ```
    
- 가로 정렬은 하지 않는다.
    - 정렬이 필요할 정도로 목록이 길다면 문제는 목록 길이지 정렬 부족이 아니다.
    
    ```java
    //하지 않는다.
    public class FitNesseExpediter implements ResponseSender {
    	private		Socket		  socket;
    	private 	InputStream 	  input;
    	private 	OutputStream 	  output;
    	private 	Reques		  request; 		
    	private 	Response 	  response;	
    	private 	FitNesseContex	  context; 
    	protected 	long		  requestParsingTimeLimit;
    	private 	long		  requestProgress;
    	private 	long		  requestParsingDeadline;
    	private 	boolean		  hasError;
    	
    	...
    ```
    
- 들여 쓰기 : 시각적으로 표현
    - 계층에서 각 수준은 이름을 선언하는 범위
    - 선언문과 실행문을 생각하는 해석하는 범위
    - 짧은 조건문과 반복문도 들여쓰기를 통해서 표현하기
    
    ```java
    int[][] info = new int[N][M];
    
    for(int[] i : info) System.out.println(Arrays.toString(i)); //X
    
    if(depth == maxdepth) return; //X
    ```
    

## 클린 코드 표본

---

```java
public class CodeAnalyzer implements JavaFileAnalysis { 
	private int lineCount;
	private int maxLineWidth;
	private int widestLineNumber;
	private LineWidthHistogram lineWidthHistogram; 
	private int totalChars;
	
	public CodeAnalyzer() {
		lineWidthHistogram = new LineWidthHistogram();
	}
	
	public static List<File> findJavaFiles(File parentDirectory) { 
		List<File> files = new ArrayList<File>(); 
		findJavaFiles(parentDirectory, files);
		return files;
	}
	
	private static void findJavaFiles(File parentDirectory, List<File> files) {
		for (File file : parentDirectory.listFiles()) {
			if (file.getName().endsWith(".java")) 
				files.add(file);
			else if (file.isDirectory()) 
				findJavaFiles(file, files);
		} 
	}
	
	public void analyzeFile(File javaFile) throws Exception { 
		BufferedReader br = new BufferedReader(new FileReader(javaFile)); 
		String line;
		while ((line = br.readLine()) != null)
			measureLine(line); 
	}
	
	private void measureLine(String line) { 
		lineCount++;
		int lineSize = line.length();
		totalChars += lineSize; 
		lineWidthHistogram.addLine(lineSize, lineCount);
		recordWidestLine(lineSize);
	}
	
	private void recordWidestLine(int lineSize) { 
		if (lineSize > maxLineWidth) {
			maxLineWidth = lineSize;
			widestLineNumber = lineCount; 
		}
	}

	public int getLineCount() { 
		return lineCount;
	}

	public int getMaxLineWidth() { 
		return maxLineWidth;
	}

	public int getWidestLineNumber() { 
		return widestLineNumber;
	}

	public LineWidthHistogram getLineWidthHistogram() {
		return lineWidthHistogram;
	}
	
	public double getMeanLineWidth() { 
		return (double)totalChars/lineCount;
	}

	public int getMedianLineWidth() {
		Integer[] sortedWidths = getSortedWidths(); 
		int cumulativeLineCount = 0;
		for (int width : sortedWidths) {
			cumulativeLineCount += lineCountForWidth(width); 
			if (cumulativeLineCount > lineCount/2)
				return width;
		}
		throw new Error("Cannot get here"); 
	}
	
	private int lineCountForWidth(int width) {
		return lineWidthHistogram.getLinesforWidth(width).size();
	}
	
	private Integer[] getSortedWidths() {
		Set<Integer> widths = lineWidthHistogram.getWidths(); 
		Integer[] sortedWidths = (widths.toArray(new Integer[0])); 
		Arrays.sort(sortedWidths);
		return sortedWidths;
	} 
}
```

## Quiz

- 형식을 맞추는 목적은?
    - 버전이 바뀌어도 초기의 스타일과 가독성 수준은 유지보수 용이성과 확장성에 영향을 미치기 때문에
- 함수 호출 종속성은 어느 방향으로 유지하는가?
    - 아래방향