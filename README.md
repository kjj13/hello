## 클래스 체계

- **클래스 작성 순서**
1. 정적 공개 상수
2. 비공개 변수
3. 비공개 인스턴스 변수
4. 함수(공개 - 비공개)

``` java
public class test { 
  // 정적 공개 상수 
  static public final String a = 'a'; 
  
  // 정적 비공개 상수 
  static private final String b = 'b'; 
  
  // 정적 비공개 변수 
  static private String c = 'c'; 
  
  // 비공개 인스턴스 변수 
  private String d = 'd'; 
  
  // 공개 함수 
  public String text() { 
    return 'text'; 
  } 
  
  // 비공개 함수 
  private String text2() { 
    return 'text2'; 
  } 
}
```

따라서 추상화 단계가 순차적으로 내려감.


- **캡슐화**

  - 반드시 비공개를 하라는 법은 없지만 비공개 상태를 유지할 온갖 방법을 강구하자.
  - 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.
    
## 클래스는 작아야 한다!

> 클래스를 설계할 때도, 함수와 마찬가지로 '작게'가 기본 규칙이다.
>
**크기 측정 척도**
- 함수 : 물리적인 행 수
- 클래스 : 클래스가 맡은 책임

**클래스 이름 설계**
- 해당 클래스 책임을 기술
- 간결한 이름이 떠오르지 않는다면 해당 클래스의 책임이 많아서이다.

### 단일 책임 원칙(SRP) ###
> 클래스나 모듈을 변경할 이유가 하나, 단 하나뿐이어야 한다는 원칙

- Before
  ``` java
  public class SuperDashboard extends JFrame implements MetaDataUser {
    public Component getLastFocusedComponent()    
    public void setLastFocused(Component lastFocused)    
    public int getMajorVersionNumber()    
    public int getMinorVersionNumber()    
    public int getBuildNumber()
  }
  ```
  - 스윙 코드를 변경할 때마다 버전 번호가 달라진다고 함. => SRP를 위반


- After
  ``` java
  public class Version {
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber()
  }
  ```

> 큰 클래스 몇 개가 아니라 작은 클래스 여럿을 이뤄진 시스템이 더 바람직하다.
> 작은 클래스는 각자 맡은 책임이 하나며, 변경할 이유가 하나며, 다른 작은 클래스와 협력해 시스템에 필요한 동작을 수행한다.

(회색) 프로그램이 돌아간다고 끝내지 말고 '깨끗하고 체계적인 소프트웨어'를 만들기 위해 노력하자.

### 응집도 ###
(회색) 높은 응집도 낮은 결합력!
https://velog.io/@ljinsk3/%EC%A2%8B%EC%9D%80-%EC%84%A4%EA%B3%84%EB%9E%80-feat.-%EA%B2%B0%ED%95%A9%EB%8F%84-%EC%9D%91%EC%A7%91%EB%8F%84

- **파일이 없으면 예외를 던지는지 알아보는 단위 테스트**
    
    ```java
    @Test(expected = StorageException.class)
     public void retrieveSectionShouldThrowOnInvalidFileName() {
         sectionStore.retrieveSection("invalid - file");
     }
    ```
    
- **예외 안던지는 코드 → 실패**
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
         // 실제로 구현할 때까지 비어 있는 더미를 반환한다.
         return new ArrayList<RecordedGrip>();
     }
    ```
    
- **잘못된 파일 접근 시도하는 코드(예외 던짐) → 성공**
    
    ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
         try {
             FileInputStream stream = new FileInputStream(sectionName);
         } catch (Exception e) {
             throw new StorageException("retrieval error", e);
         }
         return new ArrayList<RecordedGrip>();
     }
    ```
    
    - 리팩터링
    
    Exception → FileNotFoundException 으로 예외 유형을 좁힘.
    
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
    

## 미확인 예외를 사용하라

---

### 미확인 예외 vs 확인된 예외

- **미확인 예외 (UnChecked Exception)**
    - 프로그램 로직의 오류로 인한 예외.
    - **런타임** 시점에 예외처리 여부를 확인.
    - ArrayIndexOutOfBoundsException, IllegalArgumentException, NullPointerException
- **확인된 예외 (Checked Exception)**
    - 프로그램 제어 밖에 있는 예외.
    - **컴파일** 시점에 예외처리 여부를 확인.
    - FileNotFoundException, SQLException, IOException, ClassNotFoundException

참고한 사이트 : [https://velog.io/@sangmin7648/throws는-언제-사용해야할까](https://velog.io/@sangmin7648/throws%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EC%95%BC%ED%95%A0%EA%B9%8C)

<aside>
💡 **확인된 예외는 OCP를 위반한다.**

</aside>

- **OCP란?**
    
    소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장에 대해서는 열려 있어야 하지만 변경에 대해서는 닫혀 있어야 한다.
    
    **즉. OCP는 해당 클래스의 기존 동작을 변경하지 않고 클래스의 동작을 확장하는 것을 목표로 함.**
    
- 확인된 예외를 사용하면 결과적으로 최**하위 단계에서 최상위 단계까지 연쇄적인 수정**이 일어남.
- throws 경로에 위치하는 모든 함수가 최하위 함수에서 던지는 예외를 알아야 하므로 **캡슐화가 깨짐.**

## 예외에 의미를 제공하라

---

<aside>
💡 **오류가 발생한 원인과 위치를 찾기 쉽게 전후 상황을 충분히 덧붙이기.**

</aside>

- 자바에서 호출 스택 제공하지만 부족함.
- 오류 메시지에 실패한 연산 이름과 실패 유형과 같은 정보를 담아 예외와 함께 던지기.

## 호출자를 고려해 예외 클래스를 정의하라

---

<aside>
💡 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 **오류를 잡아내는 방법**이 되어야 한다.

</aside>

- **외부 라이브러리가 던질 예외를 모두 잡은 코드**
    
    ```java
    ACMEPort port = new ACMEPort(12);
    
     try {
         port.open();
     } catch (DeviceResponseException e) {
         reportPortError(e);
         logger.log("Device response exception", e);
     } catch (ATM1212UnlockedException e) {
         reportPortError(e);
         logger.log("Unlock exception", e);
     } catch (GMXError e) {
         reportPortError(e);
         logger.log("Device response exception");
     } finally {
         ...
     }
    ```
    
- **호출하는 라이브러리 API를 감싸면서 예외 유형 하나를 반환하는 코드**
    
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
    ```
    
    ```java
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
    

- 외부 API를 사용할 때는 감싸기 기법이 최선이다.
- 감싸기 기법을 사용하면 해당 API를 설계한 방식에 발목 잡히지 않음. (의존 X)

## 정상 흐름을 정의하라

---

<aside>
💡 오류 처리로 인한 중단이 때로는 적합하지 않은 때도 있다.

</aside>

- **특수 사례 패턴 적용 전**
    
    ```java
    try {
    	MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    	m_total += expenses.getTotal();
    } catch(MealExpensesNotFound e) {
    	m_total += getMealPerDiem();
    }
    ```
    
- **특수 사례 패턴 적용**
    
    ```java
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
    ```
    
    ```java
    public class PerDiemMealExpenses implements MealExpenses {
    	public int getTotal() {
    		// 기본값으로 일일 기본 식비를 반환한다.
    	}
    }
    ```
    
    - getTotal 함수에 예외가 절대 반환할 수 없도록 코드를 수정
    - 클래스를 만들거나 객체를 조작해 예외적인 상황을 캡슐화해서 처리.

## null을 반환하지 마라

---

<aside>
💡 **null을 반환하는 습관은 나쁘다.**

</aside>

- 호출자에게 null을 체크할 의무를 줌
- null 체크를 빼먹으면 NullPointerException 발생할 수 있음

- **null 체크하는 코드**
    
    ```java
    List<Employee> employees = getEmployees();
    if(employees != null) {
    	for(Employee e : employees) {
    		totalPay += e.getPay();
    	}
    }
    ```
    
- **null 체크안해도 되는 코드 (특수 사례 객체 반환)**
    
    ```java
    List<Employee> employees = getEmployees();
    for(Employee e : employees) {
    	totalPay += e.getPay();
    }
    
    public List<Employee> getEmployees() {
    	if (..직원이 없다면..)
    		return Collections.emptyList();
    }
    ```
    
    - 애초에 null 반환하지 않으므로 null 체크 안해도 됨.
    - 코드도 깔끔해지고 NullPointerException 발생할 가능성 줄어듬

## null을 전달하지 마라

---

<aside>
💡 null 반환하는 방식도 나쁘지만 **null 전달하는 방식은 더 나쁘다.**

</aside>

- 애초에 null을 전달하지 못하게 해서 null 처리하는 것도 없게 하는게 좋음.

## 결론

---

<aside>
💡 **깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.

오류 처리를 프로그램 논리와 분리해 튼튼하고 깨끗한 코드를 작성하면 코드 유지보수성도 높아진다.**

</aside>
