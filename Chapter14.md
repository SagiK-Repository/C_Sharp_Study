# 14. 람다식
## 14.1 람다식, 너는 어디서 왔니?

- 람다식(Lambda Expression), λ-Expression 이라고 한다.
- 유래
  - 알론조 처치(Alonzo Church)라는 수학자가 1936년에 발표한 람다 계산법(Lambda Calculus)에서 사용하는 식이다.
  - 수학 기초론을 연구하던 중에 분명하고 간결한 방법으로 함수를 묘사하기 위해 람다 계산법을 고안했다.
  - 람다 계산법은 함수의 정의와 변수, 함수의 적용으로 이루어져 있다. 0, 1, 2...와 같은 숫자고 함수로 표현한다.
  - 람다 계산법에서 어떤 값을 변수에 대입하고 싶으면 함수를 변수에 대입하며, 이를 함수의 적용이라 부른다.
  - 람다 계산법은 단순한 수학 이론이었지만, 알론조 처치의 제자인 존 매카시가 이것을 프로그래밍 언어에 도입할 수 있겠다는 아이디어를 냈고, 1950년대 말에 LISP라는 언어로 만들어졌다.
- C#, C++, 자바, 파이썬 같은 주류 프로그래밍 언어는 대부분 람다식을 지원한다.

<br><br>

## 14.2 처음 만나는 람다식

- 람다식은 익명 메소드를 만들기 위해 사용된다.
- 람다식으로 만드는 익명 메소드는 무명 함수(Anonymous Function)라는 이름으로 부릅니다.
- 람다식도 입력(매개변수)과 출력(반환값)을 갖고 있다.
- 기본적인 람다식을 선언하는 형식은 다음과 같다.
  ```cs
  매개변수_목록 => 식

  delegate int Calculrate(int a, int b);

  static void Main( string[] args ){
    Calculrate calc = ( int a, int b ) => a + b; // a, b를 받아 더해 반환한다.
  }
  ```
- C# 컴파일러는 이 코드를 한 층 더 간결하게 만들 수 있도록 "형식 유추(Type Inference)"라는 기능을 제공한다.
  ```cs
  Calculrate calc = ( a, b ) => a + b;

  //이를 익명 메소드로 표현하면
  Calculrate calc = delegate( int a, int b ) {
                    return a + b;
                    };
  
  ```
- 마이크로소프트는 대리자를 이용한 익명 메소드를 C# 2.0에 도입했다.
- 람다식은 C# 3.0에 도입했다. 이때, 익명 메소드 기능을 빼거나 대체할 수 없었다.

<br><br>

## 14.3 문 형식의 람다식

- 람다식이었다면 이제는 람다 문 이다.
- if( a == b ) return 0; else return 1;와 같은 문장을 사용하기 위함이다.
- 문 형식의 람다 식(Statement Lambda)은 다음과 같이 구현한다.
  ```cs
  (매개변수_목록) => {
                      문장1;
                      문장2;
                    }

  delegate void DoSomething();

  static void Main( string[] args ) {
    DoSomething Doit = ( ) => {
                                Console.WriteLine( "DoSometing" );
                                Console.WriteLine( "Anythings" );
                              };
  }
  ```
 
<br><br>

## 14.4 Func와 Action으로 더 간편하게 무명 함수 만들기

- 익명 메소드와 무명 함수는 코드를 더 간결하게 만들어주는 요소이다.
- Func 대리자는 결과를 반환하는 메소드, Action 대리자는 결과를 반환하지 않는 메소드를 참조한다.

<br><br><br>

### 14.4.1 Func 대리자

- Func 대리자는 결과를 반환하는 메소드를 참조하기 위해 만들어졌다.
- .NET에는 모두 17가지 버전의 Func 대리자가 준비되어 있다.
  ```cs
  public delegate TResult Func<out TResult> ()
  public delegate TResult Func<in T, out TResult> (T arg)
  public delegate TResult Func<in T1, in T2, out TResult> (T1 arg1, T2 arg2)
  ...
  public delegate TResult Func<in T1, ..., in T16 out TResult> (T1 arg1, ..., T16 arg)
  ```
- Func 대리자는 입력 매개변수가 하나도 없는 것 부터 16개에 이르기까지 버전이 다양하기 때문에 별도의 대리자를 만들어 쓸 필요가 없다.
- Func 대리자는 다음과 같이 사용한다.
  ```cs
  Func<int> func1 = () => 10; // 입력 매개변수는 없으며, 무조건 10을 반환
  Console.WriteLine( func1() ); // 10 출력

  Func<int, int> func2 = (x) => x*2; // 입력 매개변수는 int 형식 하나, 반환 형식도 int 하나
  Console.WriteLine( func2(3) ); //6 출력
  ```
- 예제
  ```cs
  using System;
  namespace FuncTest {
      class MainApp {
          static void Main( string[] args) {
              Func<int> func1 = () => 10;
              Console.WriteLine($"func1() : {func1()}");
  
              Func<int, int> func2 = (x) => x * 2;
              Console.WriteLine($"func2(4) : {func2(4)}");
  
              Func<double, double, double> func3 = (x, y) => x / y;
              Console.WriteLine($"func3(22 , 7) : {func3( 22, 7 )}");
          }
      }
  }
  ```
  

<br><br><br>

### 14.4.2 Action 대리자

- Action 대리자는 Func와 거의 똑같다.
- Action 대리자는 반환 형식이 없다.
  ```cs
  public delegate void Action<> ()
  public delegate void Action<in T> (T arg)
  public delegate void Action<in T1, in T2> (T1 arg, T2 arg)
  ...
  public delegate void Action<in T1, ..., in T16> (T1 arg, ..., T16 arg)
  ```
- Func와 달리 Action은 어떤 결과를 반환하는 것을 목적으로 하지 않고, 일련의 작업을 수행하는 것이 목적이다.
  ```cs
  Action acr1 = () => Console.WriteLine("Action()");
  act1();

  int result = 0;
  Action<int> act2 = (x) => result = x * x; //람다식 밖에 선언한 result에 x*x 결과를 저장
  act2(3);
  Console.WriteLine("Result : {0}", result); // 9를 출력

  Action<double, doubel> act3 = (x, y) =>
  {
    double pu = x / y;
    Console.WriteLine( "Action<T1,T2>({0}, {1}) : {2}", x, y, pi);
  };
  act3( 22.0, 7.0 );
  ```

<br><br>

## 14.5 식 트리

- 식 트리(Expression Tree)의 개념을 설명하기 전에 트리 자료구조에 대해 알아본다.
- 트리
  - 트리는 노드(Node : 마디)로 구성되며, 각 노드는 서로 부모-자식 관계로 연결된다.
  - 트리의 뿌리가 되는 노드를 루트(Root) 노드라고 한다.
  - 가장 끝에 있는 노드를 잎(Leaf) 노드 또는 단말(Terminal) 노드라고 한다.
- 식 트리는 한 부모 노드가 단 두 개만의 자식 노드를 가질 수 있는 이진 트리(Binary Tree)이다.
- 1*2+(7-8)이라는 식을 트리로 표현하면 다음과 같다.
  ```mermaid
  flowchart TD
  A(("+"))
  A --- B1(("*"))
  B1 --- C1(("1"))
  B1 --- C2(("2"))
  A --- B2(("-"))
  B2 --- C3(("7"))
  B2 --- C4(("8"))
  ```
- 식 트리 자료구조는 컴파일이나 인터프리터를 제작하는데 유용하다.
  - 컴파일러는 프로그래밍 언어늬 문법에 따라 작성된 소스 코드를 분석해서 식 트리로 만든 후 이를 바탕으로 실행파일을 만든다.
  - C#은 프로그래머가 C# 코드 안에서 직접 식 트리를 조립하고 컴파일해서 사용할 수 있는 기능을 제공한다.
  - 즉, 프로그램 실행 중에 동적으로 무명 함수를 만들어 사용할 수 있게 해준다.
- 식 트리를 다루는데 필요한 클래스들은 System.Linq.Expressions 네임스페이스 안에 준비되어 있다.
- Expression 클래스
  - Expression 클래스는 식 트리를 구성하는 노드를 표현한다.
  - 또한 객체를 생성하는 역할도 담당한다.
  - Expression 클래스 자싱는 abstract로 선언되어 자신의 인스턴스는 만들 수 없지만, 파생 클래스의 인스턴스를 생성하는 정적 팩토리 메소드를 제공한다.
  - 팩토리 메소드 (Factory Method)
    - 팩토리 메소드는 클래스의 인스턴스를 생성하는 일을 담당하는 메소드를 가리키는 용어이다.
    - 객체 생성에 복잡한 논리가 필요한 경우, 객체 생성 과정을 별도의 메소드에 구현해 놓으면 코드의 복잡성을 상당히 줄일 수 있다.
- 예시
  ```cs
  Expression const1 = Expression.Constant(1); // 상수 1
  Expression param1 = Expression.Parameter(typeof(int), "x"); // 매개변수 x

  Expression exp = Expression.Add( const1, param1 ); // 1 + x

  Expression<Func<int, int>> lambda = 
    Expression<Func<int, int>>.Lambda<Func<int, int>>(
      exp, new ParameterExpression[]{ (ParameterExpression)param1 } );
  
  Func<int, int> compiledExp = lambda1.Compile(); //수행 가능한 코드로 컴파일

  Console.WriteLine( compiledExp(3) ); // x = 3 이면 1+x=4 이므로 4를 출력
  ```
- Expession 형식은 불변(Immutable)이기 때문에 인스턴스가 한번 만들어진 후에는 변경할 수 없다.
- 예제
  ```cs
  using System;
  using System.Linq.Expressions;
  namespace ExpressionTreeLambda {
      class MainApp {
          static void Main( string[] args ) {
              Expression<Func<int, int, int>> expression = (a, b) => 1 * 2 + (a - b);
              Func<int, int, int> func = expression.Compile();
  
              Console.WriteLine($"1*2+({7}-{8}) = {func(7, 8)}"); // x = 7, y = 8
          }
      }
  }
  ```
>>>
### 실행 결과
1*2+(7-8) = 1  
>>>
<br><br>

## 14.6 식으로 이루어지는 맴버


- 메소드를 비롯하여 속성(인덱서), 생성자, 종료자는 공통된 특징이 있다. 클래스의 맴버로써 본문이 {중괄호}로 만들어져 있다.
- 이러한 맴버 본문을 식(Expression)만으로 구현할 수 있는데, 이렇게 식으로 구현된 맴버를 영어로 "Expression-Boldied Member"라고 하고, 우리말로는 "식 본문 맴버"라고 한다.
  ```cs
  맴버 => 식;

  class FirendList {
    private List<string> list = new List<string>();
    //...//

    public void Add( string name ) => list.Add( name ); // 메소드
    public void Remove( string name ) => list.Remove( name );

    public FriendList() => Console.WriteLine( "FriendList()" ); // 생성자
    ~FriendList() => Console.WriteLine( "~FriendList();" ); // 종료자

    public int Capacity => list.Capacity;  // 읽기 전용 속성
    public string this[ int index ] => list[ index ]; // 읽기 전용 인덱서

    public int Capacity { // 속성
      get => list.Capacity;
      set => list.Capacity = value;
    }

    public string this[ int index ] { // 인덱서
      get => list[ index ];
      set => list[ index ] = value;
    } 

  }
  ```
  
