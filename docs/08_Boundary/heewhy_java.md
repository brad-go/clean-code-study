# 경계

> 프로그래머는 어떤 식으로든 외부코드(패키지, 오픈소스, 다른 부서가 제공하는 컴포넌트)를 사용한다.
> 

## 외부 코드 사용하기

---

- 인터페이스 제공자와 사용자 사이에는 특유의 긴장이 존재
    - 인터페이스 제공자 : 적용성을 최대한 넓힘(더 많은 고객 유치를 위해)
    - 인터페이스 사용자 : 자신의 요구에 집중하는 인터페이스 요구
- java.util.Map
    - Map 인터페이스는 데이터를 저장하기 위해서 다른 객체를 키 값으로 지정하여 key-value를 하나의 쌍으로 하여 저장하여 관리하는 Collection
        - key는 중복 허용 X, value는 중복 허용
    - `Map sensors = new HashMap(); //Semsor 객체를 담는 Map`
    - `Sensor s = (Sensor)sensors.get(sensorId); // Sensor 객체 가져오기`
    - Map이 반환하는 Object를 올바른 유형으로 변환할 책임은 Map을 사용하는 클라이언트에 있음
    - 제네릭스 사용 Generics → 가독성 증가, 필요하지 않은 기능까지 제공
        - `Map<String, Sensor> sensors = new HashMap<Sensors>();`
        - `Sensor s = sensors.get(sensorId);`
    - Map을 Sensors 안으로 숨기기
        
        ```jsx
        public class Sensors{
        	private Map Sensors = new HashMap();
        
        	public Sensor getById(String id){
        		return (Sensor) sensors.get(id);
        	}
        }
        ```
        
    - 경계 인터페이스를 이용할 때는 이를 이용하는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의
        - 인스턴스를 공개 API의 인수로 넘기거나 반환 값으로 사용하지 않는다.
    
    ## 경계 살피고 익히기
    
    ---
    
    - 외부 코드를 사용하면 적은 시간에 더 많은 기능을 출시하기 쉽다.
    - 외부 코드를 익히거나 통합하기 어렵다.
        - 학습 테스트(짐 뉴커크[Jim Newkirk])
            - 우리쪽 코드를 작성해 외부 코드를 호출하는 대신 먼저 간단한 테스트 케이스를 작성해 외부 코드를 익히는 것
    
    ## 패키지 익히기
    
    ---
    
    1. 패키지를 내려 받아 소개 페이지를 열기
    2. 문서를 자세히 읽기 전에 첫번째 테스트 케이스 작성하기
        
        ```jsx
        @Test
        public void testLogCreate() {
            Logger logger = Logger.getLogger("MyLogger");
            logger.info("hello");
        }
        //Appender 필요하다는 오류 발생
        ```
        
    3. 오류 확인하고 없애주기
        
        ```jsx
        @Test
        public void testLogAddAppender() {
            Logger logger = Logger.getLogger("MyLogger");
            ConsoleAppender appender = new ConsoleAppender();
            logger.addAppender(appender);
            logger.info("hello");
        }
        //출력 스트림X
        ```
        
    4. 오류 확인하고 없애주기
        
        ```jsx
        @Test
        public void testLogAddAppender() {
            Logger logger = Logger.getLogger("MyLogger");
            logger.removeAllAppenders();
            logger.addAppender(new ConsoleAppender(
                new PatternLayout("%p %t %m%n"),
                ConsoleAppender.SYSTEM_OUT));
            logger.info("hello");
        }
        ```
        
    5. 문서를 다시 읽고 돌아가는 방식 파악하기
        
        ```jsx
        public class LogTest {
            private Logger logger;
            
            @Before
            public void initialize() {
                logger = Logger.getLogger("logger");
                logger.removeAllAppenders();
                Logger.getRootLogger().removeAllAppenders();
            }
            
            @Test
            public void basicLogger() {
                BasicConfigurator.configure();
                logger.info("basicLogger");
            }
            
            @Test
            public void addAppenderWithStream() {
                logger.addAppender(new ConsoleAppender(
                    new PatternLayout("%p %t %m%n"),
                    ConsoleAppender.SYSTEM_OUT));
                logger.info("addAppenderWithStream");
            }
            
            @Test
            public void addAppenderWithoutStream() {
                logger.addAppender(new ConsoleAppender(
                    new PatternLayout("%p %t %m%n")));
                logger.info("addAppenderWithoutStream");
            }
        }
        ```
        
        ## 학습 테스트는 공짜 이상이다
        
        ---
        
        - 학습 테스트는 이해도를 높여주는 정확한 실험이다.
        - 새 버전이 우리 코드와 호환되지 않으면 학습 테스트가 이 사실을 곧바로 밝혀낸다.
        
        ## 아직 존재하지 않는 코드를 사용하기
        
        ---
        
        - 아직 구현하지 않은 코드를 사용해야 할 때 인터페이스를 정의한다.
        - 추상 메서드를 추가하고 간단한 구현체를 만든다.
        - 우리가 바라는 인터페이스를 구현하면 우리가 인터페이스를 전적으로 통제하는 장점이 생긴다.
        - 코드 가독성은 높아지고 코드 의도도 분명해진다.
        
        ## 깨끗한 경계
        
        ---
        
        - 소프트웨어 설계가 우수하다면 변경하는데 많은 투자와 재 작업이 필요하지 않다.
        - 경계에 위치하는 코드는 깔끔히 분리한다.
        - 기대치를 정의하는 테스트 케이스를 작성한다.
        - 통제가 가능한 우리 코드에 의존하여 안에 숨긴다.
        - 패키지를 호출하는 코드를 줄인다.
        - 추후 패키지가 변했을 때 변경할 코드가 적도록 작성한다.
        
        ## Quiz
        
        - 인터페이스 제공자와 사용자 사이에는 특유의 긴장이 무엇인가?
            - 의견 또는 관점 차이로 발생하는 것
            - 인터페이스 제공자 : 적용성을 최대한 넓힘(더 많은 고객 유치를 위해)
            - 인터페이스 사용자 : 자신의 요구에 집중하는 인터페이스 요구
        - 학습 테스트란?
            - 우리쪽 코드를 작성해 외부 코드를 호출하는 대신 먼저 간단한 테스트 케이스를 작성해 외부 코드를 익히는 것