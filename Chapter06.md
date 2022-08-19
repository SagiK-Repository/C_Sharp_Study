# 06. 메소드 코드로 간추리기


## 6.1 메소드란? 

- 메소드(Method)는 객체지향 프로그래밍 언어에서 사용하는 용어로, C와 C++ 언어에서는 함수(Function)이라 불렀고 파스칼에서는 프로시저(Procedure)라고 불린다. 서브루틴(Subroutine) 또는 서브 프로그램(Subprogram)이라 부르는 언어도 있다.
- 메소드(Method)는 일련의 코드를 하나의 이름 아래 묶은 것이다.
- 메소드를 불러주는 것 만으로도 실행할 수 있다. (메소드를 호출(Call) 한다.)
  ```cs
  class 클래스_이름
  {
    한정자 반환_형식 메소드_이름( 매게변수_목록 )
    {
      // 실행하고자 하는 코드 또는 코드 블록
      return 메소드_결과; // 반환_형식과 메소드_결과의 형식이 일치해야 한다.
    }
  }
  ```
- 메소드가 클래스 안에 선언이 된다. 메소드가 함수, 프로시저, 서브루틴 등과 다른점이 있다면, 이 클래스 안에 존재한단는 것이다.
- C#은 객체지향 프로그래밍 언어이고, 객체지향 프로그래밍에서는 코드 내의 모든 것을 객체로 표현한다.
- 각 객체는 자신만의 속성(데이터)과 기능(메소드)를 갖고 있다.
- 메소드(Method)는 "방법", "방식"이라는 뜻을 갖고 있지만, 객체의 일을 처리하느 방법 또는 방식이라고 생각하면 이해하기 쉽다.

### 메소드

- 메소드(Method)s는 매개변수(Parameter)와 반환 형식(Return Type)를 가진다.
- 호출을 받은 메소드는 매개변수를 이용하여 계산을 수행 후 결과를 호출자에게 반환한다.
- 메소드를 활용하면 코드 길이도 줄고, 가독성도 좋아진다.
- 반환형식이 void이 경우, 메소드들은 자기 할 일만 하고 종료한다.
- static 한정자는 사전적으로 "정적", 움직이지 않는다는 뜻을 갖고 있다. (7장에서 더 자세히 다룰 예정)

```cs
class Calculator
{
  public static int Plus(int a, int b){
    Console.WriteLine("Input : {0}, {1}", a, b);

    int result = a + b;
    return result;
  }
}

//-------------------------------------------//

int x = Calculator.Plus( 3, 4 ); // x = 7
int y = Calculator.Plus( 5, 6 ); // y = 11
```

<br><br>

## 6.2 Return

- return 문은 점프문의 한 종류이다.
- 프로그램의 흐름을 갑자기 호출자에게 돌려 놓는다.
- return 문은 언제든지 메소드 중간에 호출되어 메소드를 종결시키고 프로그램의 흐름을 호출자에게 돌려줄 수 있다.
- 아무것도 반환하지 않을때(void)는 return; 이라고 하여, 아무것도 반환하지 않는다.
- 자기 자신을 호출하는 것을 재귀호출이라 한다.  
  ```cs
  int Fibonacci( int n ){
    if( n < 2 )
      return n;
    else
      return Fibonacci( n-1 ) + Fibonacci( n-2 );
  }
  ```

<br><br>

## 6.3 매개변수에 대하여

- 메소드를 변수를 담아 호출할때, 메소드 외부로부터 메소드 내부로 데이터를 전달받는 매개체 역할을 할 뿐이지, 매게변수도 근본적으로는 "변수"이다. 메소드에 인수로 넘겨진 변수들이 할당하면 변수가 담고있는 데이터만 복사된다.
- Mainapp.Main() 메소드 안에서 Calculator.Plus()가 실행 되었을때, Plus()에 넘긴 데이터가 참조든 값이든 복사가 이루어진다.
- 이때는 별개의 메모리 공간을 사용한다.
- 이처럼 메소드를 호출할 때 데이터를 복사해서 매게변수에 넘기는 것을 "값에 의한 전달(pass by value)" 라고 한다.
  ```cs
  using System;
  namespace SwapByValue{
    class MainApp{
      public static void Swap(int a, int b){
        int temp = a;
        b = a;
        a = temp;
      }
      static void Main(string[] args){
        int x = 3, y = 4;
        Console.WriteLine($"x:{x}, y:{y}");
        
        Swap(x, y);
        Console.WriteLine($"x:{x}, y:{y}"); // 값에 의한 전달은 x, y에 영향이 없다.
      }
    }
  }
  ```
  >>>
  ### 실행 결과
  x:3, y:4  
  x:3, y:4
  >>>

<br><br>

## 6.4 참조에 의한 매게변수 전달

- 값에 의한 전달은 MainApp에 존재하는 변수에 영향을 끼치지 못했다.
- 매게변수를 참조에 의한 전달로 진행하면, 정상적으로 동작하게 된다.
- C#에서 참조에 의한 매개변수 전달은 ref 키워드로 이루어진다.
- 메소드 선언에 ref 키워드를 매개변수 앞에 붙여준다.
  ```cs
  using System;
  namespace SwapByValue{
    class MainApp{
      public static void Swap(ref int a, ref int b){
        int temp = a;
        b = a;
        a = temp;
      }
      static void Main(string[] args){
        int x = 3, y = 4;
        Console.WriteLine($"x:{x}, y:{y}");
        
        Swap(ref x, ref y);
        Console.WriteLine($"x:{x}, y:{y}"); // 값에 의한 전달은 x, y에 영향이 없다.
      }
    }
  }
  ```

  >>>
  ### 실행 결과
  x:3, y:4  
  x:4, y:3
  >>>


<br><br>

## 6.5 메소드의 결과를 참조로 반환하기

- 메소드의 결과를 참조로 반환하는 "참조 반환값(ref return)"에 대해 알아본다.
- ref 한정자를 이용해서 메소드를 선언하고, return 문이 반환하는 변수 앞에도 ref 키워드를 명시한다.
  ```cs
  using System;

  namespace RefReturn{
    class Product{
      private int price = 100;
      public ref int GetPrice() {
        return price;
      }
      public void PrintPrice(){
        Console.WriteLine($"Price :{price}");
      }
    }

    class MainApp{
      static void Main(string[] args){
        Product carrot = new Product();
        ref int ref_local_price = ref carrot.GetPrice();
        int nor_local_price = carrot.GetPrice();

        carrot.PrintPrice(); // 100
        Console.WriteLine($"Ref Local Price :{ref_local_price}"); // 100
        Console.WriteLine($"Nor Local Price :{nor_local_price}"); // 100

        ref_local_price = 200;

        carrot.PrintPrice();  // 200
        Console.WriteLine($"Ref Local Price :{ref_local_price}"); // 200
        Console.WriteLine($"Nor Local Price :{nor_local_price}"); // 100

      }
    }
  }
  ```

<br><br>

## 6.6 출력 전용 매개변수

- 두 개 이상의 결과를 요구하는 특별한 메소드가 존재한다.
  ```cs
  void Divide( int a, int b, ref int quotient, ref int remainder){
    quotient = a / b;
    remainder = a % b;
  }
  //------------------------------------//
  int a = 20, b = 3;
  int c = 0; int d = 0;

  Divide( a, b, ref c, ref d);

  Console.WriteLine("quotient: {0}, Reminder {1}", c, d); // 정상작동한다.
  ```
- 이러한 경우 안정성을 위해 out 키워드를 이용한다.
- ref 키워드를 이용해서 매개변수를 넘기는 경우, 메소드가 해당 매게변수에 결과를 저장하지 않아도 컴파일러는 아무런 경고를 하지 않는다.
- 반대로 out 키워드는 컴파일러가 에러 메세지를 출력한다.
- 또한 메소드를 호출하는 쪽에서, 초기화하지 않은 지역 변수를 메소드의 out 매개변수로 넘길 수 있다.
- 또한 출력 전용 매게변수는 호출할 때 매개변수 목록 안에서 즉석으로 선언해도 된다.
  ```cs
  void Divide( int a, int b, out int quotient, out int remainder){
    quotient = a / b;
    remainder = a % b;
  }
  //------------------------------------//
  int a = 20, b = 3;
  //int c = 0; int d = 0;

  Divide( a, b, out int c, out int d); //out c, out d

  Console.WriteLine("quotient: {0}, Reminder {1}", c, d); // 정상작동한다.
  ```

<br><br>

## 6.7 메소드 오버로딩

- 하나의 메소드 이름에 여러개의 구현을 올리는 것이다.
- 오버로딩(Overloading)이란 "과적하다"라는 뜻을 지니고 있다.
- 오버로딩 해 놓으면 컴파일러가 메소드 호출 코드에 사용되는 매개변수의 수와 형식을 분석해서 어떤 버전이 호출될지 찾아준다.
- 실행할 메소드의 버전을 찾는 작업이 컴파일 타임에 이루어지므로 성능 저하는 걱정하지 않아도 된다.
- 이를 통해 코드를 일관성 있게 유지해준다.
  ```cs
  int Plus(int a, int b){
    return a + b;
  }
  double Plus(double a, double b){
    return a + b;
  }
  //---------------------------------//
  int result1 = Plus(1,2);
  double result2 = Plus( 3.1, 2.4 );
  ```

<br><br>

## 6.8 가변 개수의 인수

- 형식은 같으나 매개 변수의 개수만 유연하게 달라질 수 있는 경우에 적합
- params 키워드와 배열을 이용한다.
  ```cs
  int Sum( params int[] args ){
    int sum = 0;
    for(int i = 0; i < args.Length; i++)
      sum += args[i];
    return sum;
  }
  //-------------------------------//
  int total = 0;

  total = Sum( 1, 2 );
  total = Sum( 1, 2, 3 );
  total = Sum( 1, 2, 3, 4, 5, 6, 7, 8, 9 );
  ```


<br><br>

## 6.9 명명된 인수

- 메소드를 호출할 때 매개변수 목록 중 어느 매개변수에 데이터를 할당할지 지정하는 것은 "순서"이다.
- C#에서는 또 다른 스타일인 명명된 인수(Named Ardument)를 활용하여, 메소드를 호출할 때 인수의 이름에 근거해서 데이터를 할당할 수 있는 기능이다.
  ```cs
  static void PrintProfile( string name, string phone ){
    Console.WriteLine("Name :{0}, Phone :{1}", name, phone);
  }
  //------------------//
  PrintProfile(name : "박주형", phone : "010-0000-1111");
  ```

<br><br>

## 6.10 선택된 인수

- 메소드의 매개변수는 기본 값을 가질 수 있다.
- 매개변수를 특정한 값으로 초기화하듯 메소드를 선언할 수 있다.
- 선택적 인수는 항상 필수로 인수 뒤에 와야 한다.
  ```cs
  void MyMethod( int a, int b = 0 ){
    Console.WriteLine("{0}, {1}", a, b);
  }
  //-------------------------------//
  MyMethod( 3 );
  MyMethod( 3, 2 );
  ```

<br><br>

## 6.11 로컬 함수

- 로컬 함수(Local Function)는 메소드 안에 선언되고, 선언된 메소드 안에서만 사용되는 특별한 함수이다.
- 클래스의 맴버가 아니기 때문에 메소드가 아니라 함수(Function)이라고 불린다.

```cs
class SomeClass {
  public void SomeMethod() {
    int count = 0;
    SomeLocalFunction(1,2);
    SomeLocalFunction(3,4);

    void SomeLocalFunction( int a, int b){
      Console.WriteLine($"count : {++count}");
    }
  }
}
```
