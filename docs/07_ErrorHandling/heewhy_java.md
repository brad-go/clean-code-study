# 오류 처리

> 오류 처리 코드로 인해 프로그램 논리를 이해하기 어려워진다면 깨끗한
코드라 부르기 어렵다.
흩어진 오류 처리 코드 때문에 실제 코드가 하는 일을 파악하기가 거의
불가능 하기 때문이다.
> 

## 오류 코드보다 예외를 사용하라

---

- 오류 : 시스템이 종료되어야 할 수준의 상황과 같이 수습할 수 없는 심각한 문제 (컴파일X)
- 예외 : 개발자가 구현한 로직에서 발생한 실수나 사용자의 영향에 의해 발생
- 오류 플래그를 설정하거나 호출자에게 오류코드를 반환하는 방법
    - 호출자 코드가 복잡
    - 함수를 호출한 즉시 오류를 확인

→ 오류가 발생하면 예외를 던지는 방법

```java
public class DeviceController {
  ...
  public void sendShutDown() {
    try {
      tryToShutDown();
    } catch (DeviceShutDownError e) {
      logger.log(e);
    }
  }
    
  private void tryToShutDown() throws DeviceShutDownError {
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);
    pauseDevice(handle); 
    clearDeviceWorkQueue(handle); 
    closeDevice(handle);
  }
  
  private DeviceHandle getHandle(DeviceID id) {
    ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
  }
  ...
}
```

## Try-Catch-Finally 문 부터 작성하라

---

- 예외는 프로그램 안에다 범위를 정의한다.
- 트랜잭션과 비슷하다.
- 예외가 발생할 코드를 짤 때는 try-catch-finally 문으로 시작하는 편이 낫다.
1. 에러가 발생하는 부분을 찾는다
    
    ```java
    @Test(expected = StorageException.class)
    public void retrieveSectionShouldThrowOnInvalidFileName() {
      sectionStore.retrieveSection("invalid - file");
    }
    
    public List<RecordedGrip> retrieveSection(String sectionName) {
      // dummy return until we have a real implementation
      return new ArrayList<RecordedGrip>();
    }
    ```
    
2. try-catch문으로 감싸서 예외를 던진다.
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
        try {
          FileInputStream stream = new FileInputStream(sectionName)
        } catch (Exception e) {
          throw new StorageException("retrieval error", e);
        }
      return new ArrayList<RecordedGrip>();
    }
    ```
    
3. catch 블록에서 예외 유형을 좁혀 예외 유형을 찾아 잡아낸다.
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
      try {
        FileInputStream stream = new FileInputStream(sectionName);
        stream.close();
      } catch (FileNotFoundException e) {
        throw new StorageException("retrieval error", e);
      }
      return new ArrayList<RecordedGrip>();
    }
    ```
    
4. TDD를 사용해 필요한 나머지 논리를 추가한다.

## 미확인(unchecked) 예외를 사용하라

---

- Before
    - 코드가 메서드를 사용하는 방식이 메서드 선언과 일치하지 않으면 아예 컴파일 X
- checked Exception : 반드시 에러 처리를 해야하는 특징(try/catch or throw)
- unchecked Exception : 실행 중에(runtime) 발생할 수 있는 예외

```java
int[] list = {1, 2, 3, 4, 5};
System.out.println(list[5]);
```

- 안정적인 소프트웨어를 제작하는 요소로 확인된 예외가 반드시 필요하지 않다.
    - 확인된 예외는 OCP를 위반
        1. 메서드에서 확인된 예외를 던짐
        2. 하위 단계에서 코드를 변경하면 상위 단계 메서드 선언부를 전부 고쳐야함
            1. Mapper.interface
                
                ```java
                public interface WorldMapper {
                	List<Country> selectAllCountries() throws SQLException;
                }
                ```
                
            2. 단위테스트.java
                
                ```java
                @Test
                	public void joinTest() throws SQLException{
                		List<Country> countries = worldMapper.selectAllCountries();
                		for(Country country: countries) {
                			log.debug(country.toString());
                		}
                	}
                ```
                
            3. try-catch 중첩
                
                ```java
                @Autowired
                	DataSource ds;
                	
                	@Override
                	public List<Book> selectAll() throws SQLException {
                		List<Book> bookList = new ArrayList<>();
                		
                		String sql = "select isbn, title, author, price from book";
                		try(
                				//connection 객체 얻어오기
                				Connection conn = ds.getConnection();
                				//pstmt 객체 얻어오기
                				PreparedStatement pstmt = conn.prepareStatement(sql);
                				){
                			//sql 날리기
                			ResultSet rs = pstmt.executeQuery();
                			
                			//데이터 받아오기
                			while(rs.next()) {
                				Book book = new Book();
                				book.setIsbn(rs.getString(1));
                				book.setTitle(rs.getString(2));
                				book.setAuthor(rs.getString(3));
                				book.setPrice(rs.getInt(4));
                				
                				
                				//////////////파일 정보 추가
                
                				sql = "select saveFolder, originFile, saveFile from fileInfo where isbn=?";
                				try(
                						PreparedStatement pstmt2 = conn.prepareStatement(sql);
                					)
                				{
                					pstmt2.setString(1, book.getIsbn());
                					try(
                						ResultSet rs2 = pstmt2.executeQuery();
                						)
                					{
                						//파일 정보 목록 담을 공간
                						List<FileInfo> fileInfos = new ArrayList<FileInfo>();
                						while(rs2.next()) {
                							//파일 정보 담기
                							FileInfo fileinfo = new FileInfo();
                							fileinfo.setSaveFolder(rs2.getString(1));
                							fileinfo.setOriginFile(rs2.getString(2));
                							fileinfo.setSaveFile(rs2.getString(3));
                							
                							//해당 파일 정보 목록에 추가
                							fileInfos.add(fileinfo);
                						}
                						//book 객체에 해당 파일 정보 목록 추가
                						book.setFileInfos(fileInfos);
                					}
                				}
                				
                				///////////////////
                							
                				bookList.add(book);
                			}
                			
                			
                		}
                		
                	return bookList;
                }
                ```
                
        3. 대규모 시스템에서 불리
    - 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 큼
- 아주 중요한 라이브러리를 작성한다면 모든 예외를 잡아야 한다.

## 예외에 의미를 제공하라

---

- 애플리케이션이 로깅 기능을 사용한다면 catch 블록에서 오류를 기록하도록 충분한 정보를 넘겨준다.

```java
	@Test
	public void test() {
		log.debug("test() 실행");
	}
	
	@Test
	public void insertTest() throws SQLException {
		City city = new City();
		city.setName("테스트도시");
		city.setCountryCode("KOR");
		city.setDistrict("테스트도시");
		city.setPopulation(3000);
				
		log.debug("쿼리 수행 전 도시 정보 : {}",city.toString());
		int result = worldMapper.insertCity(city);
		log.debug("쿼리 수행 후 도시 정보 : {}",city.toString());
		log.debug("쿼리 반환 결과 : {}",result);
				
		int select = worldMapper.selectLastCityId();
		log.debug("쿼리 반환 결과 : {}",select);
}
```

## 호출자를 고려해 예외 클래스를 정의하라

---

- 애플리케이션에서 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 오류를 잡아내는 방법이 되어야한다.
    - 외부 API 사용 시
        - 감싸기 기법을 사용
        - 특정 업체가 API를 설계한 방식에 발목을 잡히지 않는다.
    - 흔히 예외 클래스가 하나만 있어도 충분한 코드가 많다.
        - 예외 클래스에 포함된 정보로 오류를 구분해도 괜찮은 경우

```java
LocalPort port = new LocalPort(12);
  try {
    port.open();
  } catch (PortDeviceFailure e) {
    reportError(e);
    logger.log(e.getMessage(), e);
  } finally {
    ...
  }
  
  public class LocalPort {
    private ACMEPort innerPort;
    public LocalPort(int portNumber) {
      innerPort = new ACMEPort(portNumber);
    }
    
    public void open() {
      try {
        innerPort.open();
      } catch (DeviceResponseException e) {
        throw new PortDeviceFailure(e);
      } catch (ATM1212UnlockedException e) {
        throw new PortDeviceFailure(e);
      } catch (GMXError e) {
        throw new PortDeviceFailure(e);
      }
    }
    ...
  }
```

## 정상 흐름을 정의하라

---

- 특수 사례 패턴 : 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식

```java
...
MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
m_total += expenses.getTotal();
...

public class PerDiemMealExpenses implements MealExpenses {
  public int getTotal() {
    // return the per diem default
  }
}
```

## null을 반환하지 마라

---

- 메서드에서 null을 반환하고픈 유혹이 든다면 그 대신 예외를 던지거나 특수 사례 객체를 반환한다.

```java
List<Employee> employees = getEmployees();
  for(Employee e : employees) {
    totalPay += e.getPay();
  }
  
  public List<Employee> getEmployees() {
    if( .. there are no employees .. ) {
      return Collections.emptyList();
    }
}
```

## null을 전달하지 마라

---

- 정상적인 인수로 null을 기대하는 API가 아니라면 메서드로 null을 전달하는 코드는 최대한 피한다.
    - `NullPointerException`
        
        ```java
        public class MetricsCalculator {
          public double xProjection(Point p1, Point p2) {
            assert p1 != null : "p1 should not be null";
            assert p2 != null : "p2 should not be null";
            
            return (p2.x – p1.x) * 1.5;
          }
        }
        ```
        
    - `InvalidArgumentException`
        
        ```java
        public class MetricsCalculator {
          public double xProjection(Point p1, Point p2) {
            if(p1 == null || p2 == null){
              throw InvalidArgumentException("Invalid argument for MetricsCalculator.xProjection");
            }
            return (p2.x – p1.x) * 1.5;
          }
        }
        ```
        
    

## 결론

---

- 깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.
- 오류 처리를 프로그램 논리와 분리해 독자적인 사안으로 고려하면 튼튼하고 깨끗한 코드를 작성할 수 있다.
- 오류 처리를 프로그램 논리와 분리하면 독립적인 추론이 가능해지며 코드 유지보수성도 크게 높아진다.

## Quiz

---

- 오류와 예외를 각각의 의미는?
    - 오류 : 시스템이 종료되어야 할 수준의 상황과 같이 수습할 수 없는 심각한 문제 (컴파일X)
    - 예외 : 개발자가 구현한 로직에서 발생한 실수나 사용자의 영향에 의해 발생
- Unchecked Exception 이란?
    - 실행 중에(runtime) 발생할 수 있는 예외
        - 컴파일 시점에 예외를 catch 하는지 확인하지 않는다.
        - 컴파일 시점에 예외가 발생하는지 여부를 판단할 수 없다.