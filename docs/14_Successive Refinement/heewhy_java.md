# 점진적인 개선

## Java의 args 매개 변수

---

```java
public class SimpleTesting{ 
    public static void main(String[] args) {
        for(int i = 0; i < args.length; i++) {
            System.out.println(args[i]);
        }  
    }
}
```

- 입력 : `Java SimpleTesting Hello DelftStack`
- 출력 :

```java
Hello
DelftStack
```

## 간단한 Args 사용법

---

```java
public static void main(String[] args) {
  try {
    Args arg = new Args("l,p#,d*", args);
    boolean logging = arg.getBoolean('l');
    int port = arg.getInt('p');
    String directory = arg.getString('d');
    executeApplication(logging, port, directory);
  } catch (ArgsException e) {
    System.out.print("Argument error: %s\n", e.errorMessage());
  }
}
```

1. 매개변수 두 개로 Args 클래스의 클래스의 인스턴스를 만든다.
2. 첫째 매개변수는 형식 또는 스키마를 지정한다.
3. "l,p#,d*"은 명령행 인수 세 개를 정의한다.
    1. 첫 번째 -l은 부울 인수다. 두 번째 -p는 정수 인수다. 세 번째 -d는 문자열 인수다.
4. 두 번째 매개변수는 main으로 넘어온 명령행 인수 배열 자체다.

## Args 구현

---

### 1차 초안

```java
import java.text.ParseException; 
import java.util.*;

public class Args {
  private String schema;
  private String[] args;
  private boolean valid = true;
  private Set<Character> unexpectedArguments = new TreeSet<Character>(); 
  private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
  private Map<Character, String> stringArgs = new HashMap<Character, String>(); 
  private Map<Character, Integer> intArgs = new HashMap<Character, Integer>(); 
  private Set<Character> argsFound = new HashSet<Character>();
  private int currentArgument;
  private char errorArgumentId = '\0';
  private String errorParameter = "TILT";
  private ErrorCode errorCode = ErrorCode.OK;
  
  private enum ErrorCode {
    OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, UNEXPECTED_ARGUMENT}
    
  public Args(String schema, String[] args) throws ParseException { 
    this.schema = schema;
    this.args = args;
    valid = parse();
  }
  
  private boolean parse() throws ParseException { 
    if (schema.length() == 0 && args.length == 0)
      return true; 
    parseSchema(); 
    try {
      parseArguments();
    } catch (ArgsException e) {
    }
    return valid;
  }
  
  private boolean parseSchema() throws ParseException { 
    for (String element : schema.split(",")) {
      if (element.length() > 0) {
        String trimmedElement = element.trim(); 
        parseSchemaElement(trimmedElement);
      } 
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ParseException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (isBooleanSchemaElement(elementTail)) 
      parseBooleanSchemaElement(elementId);
    else if (isStringSchemaElement(elementTail)) 
      parseStringSchemaElement(elementId);
    else if (isIntegerSchemaElement(elementTail)) 
      parseIntegerSchemaElement(elementId);
    else
      throw new ParseException(String.format("Argument: %c has invalid format: %s.", 
        elementId, elementTail), 0);
    } 
  }
    
  private void validateSchemaElementId(char elementId) throws ParseException { 
    if (!Character.isLetter(elementId)) {
      throw new ParseException("Bad character:" + elementId + "in Args format: " + schema, 0);
    }
  }
  
  private void parseBooleanSchemaElement(char elementId) { 
    booleanArgs.put(elementId, false);
  }
  
  private void parseIntegerSchemaElement(char elementId) { 
    intArgs.put(elementId, 0);
  }
  
  private void parseStringSchemaElement(char elementId) { 
    stringArgs.put(elementId, "");
  }
  
  private boolean isStringSchemaElement(String elementTail) { 
    return elementTail.equals("*");
  }
  
  private boolean isBooleanSchemaElement(String elementTail) { 
    return elementTail.length() == 0;
  }
  
  private boolean isIntegerSchemaElement(String elementTail) { 
    return elementTail.equals("#");
  }
  
  private boolean parseArguments() throws ArgsException {
    for (currentArgument = 0; currentArgument < args.length; currentArgument++) {
      String arg = args[currentArgument];
      parseArgument(arg); 
    }
    return true; 
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      unexpectedArguments.add(argChar); 
      errorCode = ErrorCode.UNEXPECTED_ARGUMENT; 
      valid = false;
  }
  
  private boolean setArgument(char argChar) throws ArgsException { 
    if (isBooleanArg(argChar))
      setBooleanArg(argChar, true); 
    else if (isStringArg(argChar))
      setStringArg(argChar); 
    else if (isIntArg(argChar))
      setIntArg(argChar); 
    else
      return false;
    
    return true; 
  }
  
  private boolean isIntArg(char argChar) {
    return intArgs.containsKey(argChar);
  }
  
  private void setIntArg(char argChar) throws ArgsException { 
    currentArgument++;
    String parameter = null;
    try {
      parameter = args[currentArgument];
      intArgs.put(argChar, new Integer(parameter)); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_INTEGER;
      throw new ArgsException();
    } catch (NumberFormatException e) {
      valid = false;
      errorArgumentId = argChar; 
      errorParameter = parameter;
      errorCode = ErrorCode.INVALID_INTEGER; 
      throw new ArgsException();
    } 
  }
  
  private void setStringArg(char argChar) throws ArgsException { 
    currentArgument++;
    try {
      stringArgs.put(argChar, args[currentArgument]); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_STRING; 
      throw new ArgsException();
    } 
  }
  
  private boolean isStringArg(char argChar) { 
    return stringArgs.containsKey(argChar);
  }
  
  private void setBooleanArg(char argChar, boolean value) { 
    booleanArgs.put(argChar, value);
  }
  
  private boolean isBooleanArg(char argChar) { 
    return booleanArgs.containsKey(argChar);
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public String errorMessage() throws Exception { 
    switch (errorCode) {
      case OK:
        throw new Exception("TILT: Should not get here.");
      case UNEXPECTED_ARGUMENT:
        return unexpectedArgumentMessage();
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
    }
    return ""; 
  }
  
  private String unexpectedArgumentMessage() {
    StringBuffer message = new StringBuffer("Argument(s) -"); 
    for (char c : unexpectedArguments) {
      message.append(c); 
    }
    message.append(" unexpected.");
    
    return message.toString(); 
  }
  
  private boolean falseIfNull(Boolean b) { 
    return b != null && b;
  }
  
  private int zeroIfNull(Integer i) { 
    return i == null ? 0 : i;
  }
  
  private String blankIfNull(String s) { 
    return s == null ? "" : s;
  }
  
  public String getString(char arg) { 
    return blankIfNull(stringArgs.get(arg));
  }
  
  public int getInt(char arg) {
    return zeroIfNull(intArgs.get(arg));
  }
  
  public boolean getBoolean(char arg) { 
    return falseIfNull(booleanArgs.get(arg));
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public boolean isValid() { 
    return valid;
  }
  
  private class ArgsException extends Exception {
  } 
}
```

- 첫 버전이던 Boolean 인수만 지원하던 초기 버전에서 String과 Integer 인수 유형을 추가하면서 부터 재앙이 시작됐다.

### 점진적 개선

- 테스트 주도 개발(Test-Drive Development,TDD) 기법 사용
    - 언제 어느 때라도 시스템이 돌아가야한다는 원칙
- 단위 테스트 슈트와 인수 테스트 생성
- [Args.java](http://Args.java) 끝에 추가

```java
private class ArgumentMarshaler { 
  private boolean booleanValue = false;

  public void setBoolean(boolean value) { 
    booleanValue = value;
  }
  
  public boolean getBoolean() {return booleanValue;} 
}

private class BooleanArgumentMarshaler extends ArgumentMarshaler { }
private class StringArgumentMarshaler extends ArgumentMarshaler { }
private class IntegerArgumentMarshaler extends ArgumentMarshaler { }
```

- Boolean 인수를 저장하는 HashMap에서 Boolean 인수 유형을 ArgumentMarshaler 유형으로 바꾸기
- `private Map<Character, ArgumentMarshaler> boolean Args = new HashMap<Character, ArgumentMarshaler>();`

```java
...

private void parseBooleanSchemaElement(char elementId) {
  booleanArgs.put(elementId, new BooleanArgumentMarshaler());
}
  
...

private void setBooleanArg(char argChar, boolean value) {
  booleanArgs.get(argChar).setBoolean(value);
}
  
...

public boolean getBoolean(char arg) {
  Args.ArgumentMarshaler am = booleanArgs.get(arg);
  return am != null && am.getBoolean();
}
```

### String 인수

- HashMap을 변경한 후 parse, set, get 함수를 고치기
- 각 인수 유형을 처리하는 코드를 모두 ArgumentMarshaler 클래스에 넣고 나서 그 파생 클래스를 만들어 코드를 분리할 계획을 세우고 진행
- 해당 부분을 리팩토링하고 난 후 남는 문제는 예외 코드 부분
    - 모든 예외를 하나로 모아 ArgsException 클래스를 만든 후 독자 모듈로 옮긴다
- ArgsException.java
    
    ```java
    public class ArgsException extends Exception { 
      private char errorArgumentId = '\0'; 
      private String errorParameter = "TILT"; 
      private ErrorCode errorCode = ErrorCode.OK;
      
      public ArgsException() {}
      
      public ArgsException(String message) {super(message);}
      
      public ArgsException(ErrorCode errorCode) { 
        this.errorCode = errorCode;
      }
      
      public ArgsException(ErrorCode errorCode, String errorParameter) { 
        this.errorCode = errorCode;
        this.errorParameter = errorParameter;
      }
      
      public ArgsException(ErrorCode errorCode, char errorArgumentId, String errorParameter) {
        this.errorCode = errorCode; 
        this.errorParameter = errorParameter; 
        this.errorArgumentId = errorArgumentId;
      }
      
      public char getErrorArgumentId() { 
        return errorArgumentId;
      }
      
      public void setErrorArgumentId(char errorArgumentId) { 
        this.errorArgumentId = errorArgumentId;
      }
      
      public String getErrorParameter() { 
        return errorParameter;
      }
      
      public void setErrorParameter(String errorParameter) {  
        this.errorParameter = errorParameter;
      }
      
      public ErrorCode getErrorCode() { 
        return errorCode;
      }
      
      public void setErrorCode(ErrorCode errorCode) { 
        this.errorCode = errorCode;
      }
      
      public String errorMessage() throws Exception { 
        switch (errorCode) {
          case OK:
            throw new Exception("TILT: Should not get here.");
          case UNEXPECTED_ARGUMENT:
            return String.format("Argument -%c unexpected.", errorArgumentId);
          case MISSING_STRING:
            return String.format("Could not find string parameter for -%c.", errorArgumentId);
          case INVALID_INTEGER:
            return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
          case MISSING_INTEGER:
            return String.format("Could not find integer parameter for -%c.", errorArgumentId);
          case INVALID_DOUBLE:
            return String.format("Argument -%c expects a double but was '%s'.", errorArgumentId, errorParameter);
          case MISSING_DOUBLE:
            return String.format("Could not find double parameter for -%c.", errorArgumentId);
        }
        return ""; 
      }
      
      public enum ErrorCode {
        OK, INVALID_FORMAT, UNEXPECTED_ARGUMENT, INVALID_ARGUMENT_NAME, MISSING_STRING,
        MISSING_INTEGER, INVALID_INTEGER,
        MISSING_DOUBLE, INVALID_DOUBLE
      }
    }
    ```
    
- Args.java

```java
public class Args {
  private String schema;
  private Map<Character, ArgumentMarshaler> marshalers = new HashMap<Character, ArgumentMarshaler>();
  private Set<Character> argsFound = new HashSet<Character>(); 
  private Iterator<String> currentArgument;
  private List<String> argsList;
  
  public Args(String schema, String[] args) throws ArgsException { 
    this.schema = schema;
    argsList = Arrays.asList(args);
    parse();
  }
  
  private void parse() throws ArgsException { 
    parseSchema();
    parseArguments();
  }
  
  private boolean parseSchema() throws ArgsException {
    for (String element : schema.split(",")) { 
      if (element.length() > 0) {
        parseSchemaElement(element.trim()); 
      }
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ArgsException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
      marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (elementTail.equals("*")) 
      marshalers.put(elementId, new StringArgumentMarshaler());
    else if (elementTail.equals("#"))
      marshalers.put(elementId, new IntegerArgumentMarshaler());
    else if (elementTail.equals("##")) 
      marshalers.put(elementId, new DoubleArgumentMarshaler());
    else
      throw new ArgsException(ArgsException.ErrorCode.INVALID_FORMAT, elementId, elementTail);
      
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId)) {
      throw new ArgsException(ArgsException.ErrorCode.INVALID_ARGUMENT_NAME, elementId, null);
    } 
  }
  
  private void parseArguments() throws ArgsException {
    for (currentArgument = argsList.iterator(); currentArgument.hasNext();) {
      String arg = currentArgument.next();
      parseArgument(arg); 
    }
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      throw new ArgsException(ArgsException.ErrorCode.UNEXPECTED_ARGUMENT, argChar, null);
  } 
  
  private boolean setArgument(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null)
      return false; 
    try {
      m.set(currentArgument);
      return true;
    } catch (ArgsException e) {
      e.setErrorArgumentId(argChar);
      throw e; 
    }
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public boolean getBoolean(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    boolean b = false;
    try {
      b = am != null && (Boolean) am.get(); 
    } catch (ClassCastException e) {
      b = false; 
    }
    return b; 
  }
  
  public String getString(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? "" : (String) am.get(); 
    } catch (ClassCastException e) {
      return ""; 
    }
  }
  
  public int getInt(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? 0 : (Integer) am.get(); 
    } catch (Exception e) {
      return 0; 
    }
  }
  
  public double getDouble(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? 0 : (Double) am.get(); 
    } catch (Exception e) {
      return 0.0; 
    }
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  } 
}
```

- **소프트웨어 설계는 분할만 잘해도 품질이 크게 높아진다. 적절한 장소를 만들어 코드만 분리해도 설계가 좋아진다. 관심사를 분리하면 코드를 이해하고 보수하기 훨씬 더 쉬워진다.**

## 결론

---

- 나쁜 코드보다 더 오랫동안 더 심각하게 개발 프로젝트에 악영향을 미치는 요인도 없다
- 나쁜 일정은 다시 짜면 된다. 나쁜 요구사항은 다시 정의하면 된다
- 나쁜 팀 역학은 복구하면 된다.
- **하지만 나쁜 코드는 썩어 문드러진다.**

## Quiz

- 코드를 리팩터링 할 시에 가장 먼저 해야할 일은?
    - 테스트 케이스 작성