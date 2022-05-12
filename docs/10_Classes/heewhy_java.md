# 클래스

## 배경지식

---

- SOLID(SRP,OCP,LSP,ISP,DIP)
    - SRP(단일 책임 원칙, *****Single Responsibility Principle*****)
        - 클래스는 단 한개의 책임을 가져야 한다.
        - 클래스를 변경하는 이유는 단 한개여야 한다.
    - OCP(개방-폐쇄 원칙, ***Open-Closed Principle***)
        - 확장에는 열려 있어야하고, 변경에는 닫혀 있어야 한다.
        - 기능을 변경하거나 확장할 수 있으면서 그 기능을 사용하는 코드는 수정하지 않는다.
    - LSP(리스코프 치환 원칙, ***Liskov Substitution Principle***)
        - 상위 타입의 객체를 하위 타입의 객체로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 한다.
        - 특정 메소드가 상위 타입을 인자로 사용한다고 할 때, 그 타입의 하위 타입도 문제 없이 정상적으로 작동해야 한다.
    - ISP(인터페이스 분리 원칙, ***Interface Segregation Principle***)
        - 클라이언트는 자신이 사용하는 메소드에만 의존해야 한다.
        - 인터페이스는 그 인터페이스를 사용하는 클라이언트를 기준으로 분리해야 한다.
    - DIP(의존 역전 원칙, ***Dependency Inversion Principle***)
        - 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안 된다. 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야 한다.
        - 자신보다 변하기 쉬운 것에 의존하지 않는다.
        - 구현보다는 추상화에 의존한다.
- 모듈 : 크기와 상관없이 클래스나 패키지, 라이브러리와 같이 프로그램을 구성하는 임의의 요소
    1. 제대로 실행되어야 한다.
    2. 변경이 용이해햐 한다.
    3. 이해하기 쉬워야 한다.
    - 응집도
        - 모듈이 하나의 목적을 수행하는 요소들간의 연관성 척도
        - 모듈 내부의 기능적인 응집 정도를 나타냄
        - 응집도가 높으면 변경 대상과 범위가 명확해지는 장점이 있어서 변경 용이
    - 결합도
        - 모듈이 다른 모듈의 의존하는 정도의 척도
        - 모듈과 모듈 간의 상호 결합 정도를 나타낸다.
        - 낮을 수록 검토해야되는 소스의 수가 적어져서 변경 용이

## 클래스 체계

---

- 정적 공개 상수(Static public)
- 정적 비공개 변수(private)
- 비공개 인스턴스 변수
- 공개 함수
    - 비공개 함수(자신을 호출하는 공개 함수 직후)

## 캡슐화

---

- 변수와 유틸리티 함수는 가능한 공개하지 않는 편이 낫지만 반드시 숨겨야 한다는 법칙도 없다.
- protected를 선언하는 경우 ⇒ 테스트 코드에 접근을 허용해주기 위해서
- 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.

## 클래스는 작아야 한다!

---

- 클래스를 만들 때 첫 번째 규칙은 크기다.(작아야함)
- 크기의 기준은 클래스가 맡은 책임으로 한다.

```java
public class SuperDashboard extends JFrame implements MetaDataUser {
	public String getCustomizerLanguagePath()
	public void setSystemConfigPath(String systemConfigPath) 
	public String getSystemConfigDocument()
	public void setSystemConfigDocument(String systemConfigDocument) 
	public boolean getGuruState()
	public boolean getNoviceState()
	public boolean getOpenSourceState()
	public void showObject(MetaObject object) 
	public void showProgress(String s)
	public boolean isMetadataDirty()
	public void setIsMetadataDirty(boolean isMetadataDirty)
	public Component getLastFocusedComponent()
	public void setLastFocused(Component lastFocused)
	public void setMouseSelectState(boolean isMouseSelected) 
	public boolean isMouseSelected()
	public LanguageManager getLanguageManager()
	public Project getProject()
	public Project getFirstProject()
	public Project getLastProject()
	public String getNewProjectName()
	public void setComponentSizes(Dimension dim)
	public String getCurrentDir()
	public void setCurrentDir(String newDir)
	public void updateStatus(int dotPos, int markPos)
	public Class[] getDataBaseClasses()
	public MetadataFeeder getMetadataFeeder()
	public void addProject(Project project)
	public boolean setCurrentProject(Project project)
	public boolean removeProject(Project project)
	public MetaProjectHeader getProgramMetadata()
	public void resetDashboard()
	public Project loadProject(String fileName, String projectName)
	public void setCanSaveMetadata(boolean canSave)
	public MetaObject getSelectedObject()
	public void deselectObjects()
	public void setProject(Project project)
	public void editorAction(String actionName, ActionEvent event) 
	public void setMode(int mode)
	public FileManager getFileManager()
	public void setFileManager(FileManager fileManager)
	public ConfigManager getConfigManager()
	public void setConfigManager(ConfigManager configManager) 
	public ClassLoader getClassLoader()
	public void setClassLoader(ClassLoader classLoader)
	public Properties getProps()
	public String getUserHome()
	public String getBaseDir()
	public int getMajorVersionNumber()
	public int getMinorVersionNumber()
	public int getBuildNumber()
	public MetaObject pasting(MetaObject target, MetaObject pasted, MetaProject project)
	public void processMenuItems(MetaObject metaObject)
	public void processMenuSeparators(MetaObject metaObject) 
	public void processTabPages(MetaObject metaObject)
	public void processPlacement(MetaObject object)
	public void processCreateLayout(MetaObject object)
	public void updateDisplayLayer(MetaObject object, int layerIndex) 
	public void propertyEditedRepaint(MetaObject object)
	public void processDeleteObject(MetaObject object)
	public boolean getAttachedToDesigner()
	public void processProjectChangedState(boolean hasProjectChanged) 
	public void processObjectNameChanged(MetaObject object)
	public void runProject()
	public void setAçowDragging(boolean allowDragging) 
	public boolean allowDragging()
	public boolean isCustomizing()
	public void setTitle(String title)
	public IdeMenuBar getIdeMenuBar()
	public void showHelper(MetaObject metaObject, String propertyName) 
	
	// ... many non-public methods follow ...
}
```

- 개념은 빈 행으로 분리 한다.
- 메서드가 작다고 크기가 작은 것이 아니다.

```java
public class SuperDashboard extends JFrame implements MetaDataUser {
	public Component getLastFocusedComponent()
	public void setLastFocused(Component lastFocused)
	public int getMajorVersionNumber()
	public int getMinorVersionNumber()
	public int getBuildNumber() 
}
```

- 클래스 이름은 해당 클래스 책임을 기술한다.
    - Processor, Manager, Super 등과 같이 모호한 단어가 있다면 여러 책임을 떠안겼다는 증거다.

### 단일 책임 원칙

- SRP : 클래스나 모듈을 변경할 이유가 하나, 단 하나뿐이어야 한다는 원칙
- 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기도 쉬워진다.
- 보통 ‘깨끗하고 체계적인 소프트웨어’보다 ‘돌아가는 소프트웨어’에 초점을 맞춘다.
    - 올바른 태도이긴 하나 관심사를 분리하는 작업은 프로그래밍 활동에서도 중요하다.
    - 프로그램이 동작하면 다음 관심사인 ‘깨끗하고 체계적인 소프트웨어’로 전환한다.
- 작은 클래스가 많은 시스템이든 큰 클래스가 몇 개 뿐인 시스템이든 돌아가는 부품은 그 수가 비슷하다.
- 규모가 어느 수준에 이르는 시스템은 논리가 많고 복잡하다 ⇒ 복잡성을 다루기 위해 체계적인 정리가 필수다.
- 그래서 큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.

### 응집도

- 클래스는 인스턴스 변수 수가 작아야 한다.
- 응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미이다.

```java
// 응집도가 높은 스택을 구현한 코드

public class Stack {
	private int topOfStack = 0;
	List<Integer> elements = new LinkedList<Integer>();

	public int size() { 
		return topOfStack;
	}

	public void push(int element) { 
		topOfStack++; 
		elements.add(element);
	}
	
	public int pop() throws PoppedWhenEmpty { 
		if (topOfStack == 0)
			throw new PoppedWhenEmpty();
		int element = elements.get(--topOfStack); 
		elements.remove(topOfStack);
		return element;
	}
}
```

### 응집도를 유지하면 작은 클래스 여럿이 나온다.

- 클래스가 응집력을 잃는다면 쪼개야 한다.
- 큰 함수를 작은 함수 여럿으로 쪼개다 보면 종종 작은 클래스 여럿으로 쪼갤 기회가 생긴다.
- 프로그램에 점점 더 체계가 잡히고 구조가 투명해진다.

### 리팩터링 법칙

1. 리팩터링한 프로그램은 좀 더 길고 서술적인 변수 이름을 사용한다.
2. 리팩터링한 프로그램은 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용한다.
3. 가독성을 높이고자 공백을 추가하고 형식을 맞춘다.

### 쪼개기 순서

1. 원래 프로그램의 정확한 동작을 검증하는 테스트 슈트를 작성한다.
2. 한 번에 하나씩 수 차례에 걸쳐 조금씩 코드를 변경한다.
3. 코드를 변경할 때마다 테스트를 수행해 원래프로그램과 동일하게 동작하는지 확인한다.
    
    ⇒ 테스트는 동작 단위로 작성한다.
    
4. 최종 프로그램

## 변경하기 쉬운 클래스

---

- 대다수 시스템은 지속적인 변경이 가해진다.
- 클래스 일부에서만 사용되는 비공개 메서드는 코드를 개선할 잠재적인 여지를 시사한다.
- SRP, OCP를 지원하는지 확인한다.
    - OCP : 클래스는 확장에 개방적이고 수정에 폐쇄적이어야 한다는 원칙
    
    ```java
    public class Sql {
    	public Sql(String table, Column[] columns)
    	public String create()
    	public String insert(Object[] fields)
    	public String selectAll()
    	public String findByKey(String keyColumn, String keyValue)
    	public String select(Column column, String pattern)
    	public String select(Criteria criteria)
    	public String preparedInsert()
    	private String columnList(Column[] columns)
    	private String valuesList(Object[] fields, final Column[] columns) private String selectWithCriteria(String criteria)
    	private String placeholderList(Column[] columns)
    }
    ```
    
    ```java
    // 공개 인터페이스를 전부 SQL 클래스에서 파생하는 클래스로 만들고, 비공개 메서드는 해당 클래스로 옮기고,
    // 공통된 인터페이스는 따로 클래스로 뺐다.
    // 이렇게 하면 update문 추가 시에 기존의 클래스를 건드릴 이유가 없어진다.
    
    	abstract public class Sql {
    		public Sql(String table, Column[] columns) 
    		abstract public String generate();
    	}
    	public class CreateSql extends Sql {
    		public CreateSql(String table, Column[] columns) 
    		@Override public String generate()
    	}
    	
    	public class SelectSql extends Sql {
    		public SelectSql(String table, Column[] columns) 
    		@Override public String generate()
    	}
    	
    	public class InsertSql extends Sql {
    		public InsertSql(String table, Column[] columns, Object[] fields) 
    		@Override public String generate()
    		private String valuesList(Object[] fields, final Column[] columns)
    	}
    	
    	public class SelectWithCriteriaSql extends Sql { 
    		public SelectWithCriteriaSql(
    		String table, Column[] columns, Criteria criteria) 
    		@Override public String generate()
    	}
    	
    	public class SelectWithMatchSql extends Sql { 
    		public SelectWithMatchSql(String table, Column[] columns, Column column, String pattern) 
    		@Override public String generate()
    	}
    	
    	public class FindByKeySql extends Sql public FindByKeySql(
    		String table, Column[] columns, String keyColumn, String keyValue) 
    		@Override public String generate()
    	}
    	
    	public class PreparedInsertSql extends Sql {
    		public PreparedInsertSql(String table, Column[] columns) 
    		@Override public String generate() {
    		private String placeholderList(Column[] columns)
    	}
    	
    	public class Where {
    		public Where(String criteria) public String generate()
    	}
    	
    	public class ColumnList {
    		public ColumnList(Column[] columns) public String generate()
    	}
    ```
    

### 변경으로부터 격리

- 객체 지향 프로그래밍 = 구체적인 클래스 + 추상클래스
    - 구체적인 클래스 = 상세한 구현(코드)
    - 추상 클래스 = 개념
- 상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험해진다.(변경이 어렵다.)

⇒ 인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리

- 결함도를 최소로 줄이면 자연스럽게 또 다른 클래스 설계 원칙인 DIP를 따르는 클래스가 나온다.
    - DIP : 클래스가 상세한 구현이 아니라 추상화에 의존해야 한다는 원칙
    

## Quiz

- 클래스의 크기의 기준은 무엇인가?
    - 크기의 기준은 클래스가 맡은 책임으로 한다.
- 클래스 쪼개기의 순서는?
    1. 원래 프로그램의 정확한 동작을 검증하는 테스트 슈트를 작성한다.
    2. 한 번에 하나씩 수 차례에 걸쳐 조금씩 코드를 변경한다.
    3. 코드를 변경할 때마다 테스트를 수행해 원래프로그램과 동일하게 동작하는지 확인한다.
        
        ⇒ 테스트는 동작 단위로 작성한다.
        
    4. 최종 프로그램