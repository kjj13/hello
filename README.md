# 10장. 클래스

## 클래스 체계

- **클래스 작성 순서**
1. 정적 공개 상수
2. 비공개 변수
3. 비공개 인스턴스 변수
4. 함수(공개 - 비공개)

**예시**

```java
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

추상화 단계가 순차적으로 내려감.

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

### 단일 책임 원칙(SRP)

> 클래스나 모듈을 변경할 이유가 하나, 단 하나뿐이어야 한다는 원칙
> 
- Before
    
    ```java
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
    
    ```java
    public class Version {
      public int getMajorVersionNumber()
      public int getMinorVersionNumber()
      public int getBuildNumber()
    }
    
    ```
    

> 큰 클래스 몇 개가 아니라 작은 클래스 여럿을 이뤄진 시스템이 더 바람직하다.
작은 클래스는 각자 맡은 책임이 하나며, 변경할 이유가 하나며, 다른 작은 클래스와 협력해 시스템에 필요한 동작을 수행한다.
> 

프로그램이 돌아간다고 끝내지 말고 '깨끗하고 체계적인 소프트웨어'를 만들기 위해 노력하자.

### 응집도

(회색) 높은 응집도 낮은 결합력!

[https://velog.io/@ljinsk3/좋은-설계란-feat.-결합도-응집도](https://velog.io/@ljinsk3/%EC%A2%8B%EC%9D%80-%EC%84%A4%EA%B3%84%EB%9E%80-feat.-%EA%B2%B0%ED%95%A9%EB%8F%84-%EC%9D%91%EC%A7%91%EB%8F%84)

> 클래스는 인스턴스 변수 수가 작아야 하고, 메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 높다.
"응집도가 높다" : 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미.
> 
- **바람직한 예**
    
    ```java
    // Stack을 구현한 코드, 응집도가 높은 편이다.
    // 각 Method마다 클래스 변수를 다 사용하였다.
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

- 큰 함수를 작은 함수 여럿으로 나누만 해도 클래스 수가 많아짐.
- 클래스가 응집력을 잃는다면 쪼개라.
- 프로그램에 점점 더 체계가 잡히고 구조가 투명해짐.
- **안좋은 예**
    
    ```java
    public class PrintPrimes {
        public static void main(String[] args) {
            final int M = 1000;
            final int RR = 50;
            final int CC = 4;
            final int WW = 10;
            final int ORDMAX = 30;
            int P[] = new int[M + 1];
            int PAGENUMBER;
            int PAGEOFFSET;
            int ROWOFFSET;
            int C;
            int J;
            int K;
            boolean JPRIME;
            int ORD;
            int SQUARE;
            int N;
            int MULT[] = new int[ORDMAX + 1];
    
            J = 1;
            K = 1;
            P[1] = 2;
            ORD = 2;
            SQUARE = 9;
    
            while (K < M) {
                do {
                    J = J + 2;
                    if (J == SQUARE) {
                        ORD = ORD + 1;
                        SQUARE = P[ORD] * P[ORD];
                        MULT[ORD - 1] = J;
                    }
                    N = 2;
                    JPRIME = true;
                    while (N < ORD && JPRIME) {
                        while (MULT[N] < J)
                            MULT[N] = MULT[N] + P[N] + P[N];
                        if (MULT[N] == J)
                            JPRIME = false;
                        N = N + 1;
                    }
                } while (!JPRIME);
                K = K + 1;
                P[K] = J;
            }
            {
                PAGENUMBER = 1;
                PAGEOFFSET = 1;
                while (PAGEOFFSET <= M) {
                    System.out.println("The First " + M + " Prime Numbers --- Page " + PAGENUMBER);
                    System.out.println("");
                    for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++) {
                        for (C = 0; C < CC;C++)
                            if (ROWOFFSET + C * RR <= M)
                                System.out.format("%10d", P[ROWOFFSET + C * RR]);
                        System.out.println("");
                    }
                    System.out.println("\\f"); PAGENUMBER = PAGENUMBER + 1; PAGEOFFSET = PAGEOFFSET + RR * CC;
                }
            }
        }
    }
    
    ```
    
- **리팩터링**
    - **PrimePrinter**
        
        main 함수 하나를 포함하며 실행 환경을 책임짐
        
        ```java
           public class PrimePrinter {
            public static void main(String[] args) {
                final int NUMBER_OF_PRIMES = 1000;
                int[] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);
        
                final int ROWS_PER_PAGE = 50;
                final int COLUMNS_PER_PAGE = 4;
                RowColumnPagePrinter tablePrinter =
                    new RowColumnPagePrinter(ROWS_PER_PAGE,
                                COLUMNS_PER_PAGE,
                                "The First " + NUMBER_OF_PRIMES + " Prime Numbers");
                tablePrinter.print(primes);
            }
        }
        
        ```
        
    - **RowColumnPagePrinter**
        
        숫자 목록을 주어진 행과 열에 맞춰 페이지에 출력
        
        ```java
          public class RowColumnPagePrinter {
            private int rowsPerPage;
            private int columnsPerPage;
            private int numbersPerPage;
            private String pageHeader;
            private PrintStream printStream;
        
            public RowColumnPagePrinter(int rowsPerPage, int columnsPerPage, String pageHeader) {
                this.rowsPerPage = rowsPerPage;
                this.columnsPerPage = columnsPerPage;
                this.pageHeader = pageHeader;
                numbersPerPage = rowsPerPage * columnsPerPage;
                printStream = System.out;
            }
        
            public void print(int data[]) {
                int pageNumber = 1;
                for (int firstIndexOnPage = 0 ;
                    firstIndexOnPage < data.length ;
                    firstIndexOnPage += numbersPerPage) {
                    int lastIndexOnPage =  Math.min(firstIndexOnPage + numbersPerPage - 1, data.length - 1);
                    printPageHeader(pageHeader, pageNumber);
                    printPage(firstIndexOnPage, lastIndexOnPage, data);
                    printStream.println("\\f");
                    pageNumber++;
                }
            }
        
            private void printPage(int firstIndexOnPage, int lastIndexOnPage, int[] data) {
                int firstIndexOfLastRowOnPage =
                firstIndexOnPage + rowsPerPage - 1;
                for (int firstIndexInRow = firstIndexOnPage ;
                    firstIndexInRow <= firstIndexOfLastRowOnPage ;
                    firstIndexInRow++) {
                    printRow(firstIndexInRow, lastIndexOnPage, data);
                    printStream.println("");
                }
            }
        
            private void printRow(int firstIndexInRow, int lastIndexOnPage, int[] data) {
                for (int column = 0; column < columnsPerPage; column++) {
                    int index = firstIndexInRow + column * rowsPerPage;
                    if (index <= lastIndexOnPage)
                        printStream.format("%10d", data[index]);
                }
            }
        
            private void printPageHeader(String pageHeader, int pageNumber) {
                printStream.println(pageHeader + " --- Page " + pageNumber);
                printStream.println("");
            }
        
            public void setOutput(PrintStream printStream) {
                this.printStream = printStream;
            }
        }
        
        ```
        
    - **PrimeGenerator**
        
        소수 목록을 생성
        
        ```java
          public class PrimeGenerator {
            private static int[] primes;
            private static ArrayList<Integer> multiplesOfPrimeFactors;
        
            protected static int[] generate(int n) {
                primes = new int[n];
                multiplesOfPrimeFactors = new ArrayList<Integer>();
                set2AsFirstPrime();
                checkOddNumbersForSubsequentPrimes();
                return primes;
            }
        
            private static void set2AsFirstPrime() {
                primes[0] = 2;
                multiplesOfPrimeFactors.add(2);
            }
        
            private static void checkOddNumbersForSubsequentPrimes() {
                int primeIndex = 1;
                for (int candidate = 3 ; primeIndex < primes.length ; candidate += 2) {
                    if (isPrime(candidate))
                        primes[primeIndex++] = candidate;
                }
            }
        
            private static boolean isPrime(int candidate) {
                if (isLeastRelevantMultipleOfNextLargerPrimeFactor(candidate)) {
                    multiplesOfPrimeFactors.add(candidate);
                    return false;
                }
                return isNotMultipleOfAnyPreviousPrimeFactor(candidate);
            }
        
            private static boolean isLeastRelevantMultipleOfNextLargerPrimeFactor(int candidate) {
                int nextLargerPrimeFactor = primes[multiplesOfPrimeFactors.size()];
                int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor;
                return candidate == leastRelevantMultiple;
            }
        
            private static boolean isNotMultipleOfAnyPreviousPrimeFactor(int candidate) {
                for (int n = 1; n < multiplesOfPrimeFactors.size(); n++) {
                    if (isMultipleOfNthPrimeFactor(candidate, n))
                        return false;
                }
                return true;
            }
        
            private static boolean isMultipleOfNthPrimeFactor(int candidate, int n) {
                return candidate == smallestOddNthMultipleNotLessThanCandidate(candidate, n);
            }
        
            private static int smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
                int multiple = multiplesOfPrimeFactors.get(n);
                while (multiple < candidate)
                    multiple += 2 * primes[n];
                multiplesOfPrimeFactors.set(n, multiple);
                return multiple;
            }
        }
        
        ```
        
- 프로그램이 길어진 이유.
    1. 좀 더 길고 서술적인 변수 이름 사용함
    2. 코드에 주석을 추가하는 수단으로 함수 선언과 클래스 선언을 활용함
    3. 가독성을 높이고자 공백을 추가하고 형식을 맞춤

> 두 프로그램의 알고리즘과 동작 원리는 동일하다. 하지만 하나의 거대한 함수보다는 책임을 하나만 가진 여러개의 클래스가 더 좋다.
> 

## 변경하기 쉬운 클래스

> 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.
> 
- **메타 자료로 적절한 SQL 문자열을 만들어주는 sql 클래스**
    
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
        private String valuesList(Object[] fields, final Column[] columns)
      private String selectWithCriteria(String criteria)
        private String placeholderList(Column[] columns)
    }
    
    ```
    
    위 클래스가 SRP를 위반하는 이유
    
    1. 새로운 SQL문을 지원하려면 Sql클래스 수정해야함.
    2. 기존 SQL문 수정할 때도 Sql클래스 수정해야함.
- **리팩터링**
    
    ```java
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
        @Override public String generate()
        private String placeholderList(Column[] columns)
      }
    
      public class Where {
        public Where(String criteria)
        public String generate()
      }
    
      public class ColumnList {
        public ColumnList(Column[] columns)
        public String generate()
      }
    
    ```
    
    Where, ColumnList는 어떤식으로 쓰는건지 모르겠네;
    
    추상 클래스 상속으로 분리하여 코드가 이해되기 쉬워졌고, SRP와 OCP를 지키게 됨.
    
    > OCP란? 클래스는 확장에 개방적이고 수정에 폐쇄적이어야한다는 원칙.
    => 새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 없어야 한다.
    > 

### 변경으로부터 격리

- 요구사항 변함 -> 코드 변함
- 객체지향 프로그래밍에서는 구체적인 클래스와 추상 클래스가 있다.
- 상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험에 빠짐. 
=> 인터페이스와 추상 클래스를 사용해 구현이 미치는 영향을 격리함.

**예시**

```java
public insterface StockExchange {
	Money currentPrice(String symbol);
}
```

```java
public Portfolio {
	private StockExchange exchange;
	public Portfolio(StockExchange exchange) {
		this.exchange = exchange;
	}
	// ...
}
```

```java
public class PortfolioTest {
	private FixedStockExchangeStub exchange;
	private Portfolio portfolio;

	@Before
	protected void setUp() throws Exception {
		exchange = new FixedStockExchangeStub();
		exchange.fix("MSFT", 100);
		portfolio = new Portfolio(exchange);
	}

	@Test
	public void GivenFiveMSFTTotalShouldBe500() throws Exception {
		portfolio.add(5, "MSFT");
		Assert.assertEquals(500, portfolio.value());
	}
}
```

- 예로 든거처럼 API를 사용해 포트폴리오 값 계산하는데 이 API가 5분마다 값이 변하므로 테스트 코드를 작성하기 어려움.
    
    ⇒ API를 직접 호출하지않고 인터페이스 생성함.
    

**장점**

- 결합도가 낮아지며 시스템 요소가 잘 격리되어 있어서 이해하기 쉬워짐.
- 구현 클래스가 아닌 인터페이스에 의존 (**DIP**가 적용됨)
    
    DIP란? 클래스가 상세한 구현이 아니라 추상화에 의존해야 한다는 원칙
    

## 참고할만한거

- SOLID 
[https://www.notion.so/jjoong/9bd1160bc0a5449a893c81d2d6d0cbf5#8b6934d28f444b108b06093b22b23474](https://www.notion.so/9bd1160bc0a5449a893c81d2d6d0cbf5)
- 인터페이스 매개변수 
[https://im-recording-of-sw-studies.tistory.com/56](https://im-recording-of-sw-studies.tistory.com/56)
