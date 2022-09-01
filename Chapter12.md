# 12. 예외 처리하기

## 12.1 예외에 대하여

- 프로그래머가 생각하는 시나리오에서 벗어나는 사건, 이것을 예외(Exception)이라고 한다.
- 예외 프로그램의 오류나 다운으로 이어지지 않도록 적절하게 처리하는 것을 예외 처리(Exception Handling)라고 한다.

<br><br>

## 12.2 try~catch로 예외 받기

- try~catch 문을 이용해 예외를 받는다.
  ```cs
  using System;
  namespace TryCatch {
      class MainApp {
          static void Main(string[] args) {
              int[] arr = { 1, 2, 3 };
  
              try {

                  for(int i = 0; i < 5; i++)
                      Console.WriteLine(arr[i]);
  
              } catch( IndexOutOfRangeException e) {
                  Console.WriteLine($"예외가 발생했습니다 : {e.Message}");
              }
  
              Console.WriteLine("종료");
          }
      }
  }
  ```
>>>
### 실행 결과
1  
2  
3  
예외가 발생했습니다 : Index was outside the bounds of the array.  
종료  
>>>

<br><br>

## 12.3 System.Exception 클래스

- C#에서 모든 예외 클래스는 반드시 System.Exception 클래스로부터 상속받아야 한다.
  ```cs
  try{/*...*/}
  catch( IndexOutOfRangeException e ){
    //...//
  }
  catch( DivideByZeroException e ){
    //...//
  }

  //...//

  try{/*...*/}
  catch( Exception e ){
    //...//
  }
  ```
- System.Exception 형식은 프로그래머가 발생할 것을 계산한 예외 말고도 다른 예외까지 받아낼 수 있다.
- 만약 그 예외가 현재 코드가 아닌 상위 코드에서 처리해야 할 예외라면, 이 코드는 예외를 처리하는 대신 버그를 만들고 있는 셈이다.
- System.Exception 예외를 사용할 때는 코드를 면밀히 검토해서 처리하지 않아야 할 예외까지 처리하는 일이 없도록 해야 한다.

<br><br>

## 12.4 예외 던지기

- throw 문을 이용해 예외를 던진다.
  ```cs
  try{
    throw new Exception("예외를 던집니다.");
  }
  catch(Exception e){
    Console.WriteLine( e.Message );
  }

  //...//

  static void DoSomething( int arg ){
    if( arg < 10 )
      Console.WriteLine("arg : {0}", arg);
    else
      throw new Exception("arg가 10보다 큽니다.");
  }

  static void Main() {
    try {
      DoSomething( 13 );
      DoSomething( 7 ); // DoSomething( 13 ); 에서 예외가 발생하여 이 코드는 실행되지 않는다.
    }
    catch (Exception e ) {
      Console.WriteLine( e.Message );
    }
  }
  ```
- C# 7.0 부터는 throw 식(Expression)으로도 사용할 수 있도록 개선되었다.
  ```cs
  int? a = null;
  int b = a ?? throw new ArgumentNullException(); //a는 null 이므로, b에 a가 할당하지 않고 throw 식이 실행된다.

  //...//

  int[] array = new int[] { 1, 2, 3 };
  int index = 4;
  int value = array[
      index >= 0 && index < 3 ? index : throw new IndexOutOfRangeException()
      ];
  ```

<br><br>

## 12.5 try~catch와 finally

- C#에서는 예외 처리를 할 때 자원 해제 같은 뒷 마무리를 실행할 수 있도록 finally 절을 try~catch와 함께 제공한다.
- return이나 t
  ```cs
  try {
    dbconn.Open();
    /*...*/
    return dividend; // 예외가 일어나더라도 finally 절은 실행된다.
  }
  catch ( Exception e ){
    throw e; //예외가 일어나도 finally 절은 실행된다.
    /*...*/
  }
  finally { dbconn.Close(); /*...*/}
  ```

<br><br>

## 12.6 사용자 정의 예외 클래스 만들기

- C#에서 사용되는 모든 예외 객체는 System.Exception 클래스로부터 파생되어야 한다.
- Exception 클래스를 상속하기만 하면, 새로운 예외 클래스를 만들 수 있다.
- .NET이 100가지 넘는 예외 형식을 제공하지만, 특별한 데이터를 담아서 예외 처리 루틴에 추가 정보를 제공하고 싶거나 예외 상황을 더 잘 설명하고 싶을 때는 사용자 정의 예외 클래스가 필요하다.
  ```cs
  class MyException : Exception {
    //...//
  }
  ```
- 예제
  ```cs
  using System;
  namespace MyException {
      class InvalidArgumentException : Exception { // Exception 상속
          public InvalidArgumentException() {  }
  
          public InvalidArgumentException(string message) : base(message){ }
  
          public object Argument { get; set; }
  
          public string Range { get; set; }
      }
  
      class MainApp {
          static uint MergeARGB(uint alpha, uint red, uint green, uint blue) {
              uint[] args = new uint[] { alpha, red, green, blue };
  
              foreach (uint arg in args) {
                  if (arg > 255)
                      throw new InvalidArgumentException()
                      {
                          Argument = arg,
                          Range = "0~255"
                      };
              }
  
              return (alpha << 24 & 0xFF000000) |
                     (red << 16 & 0x00FF0000) |
                     (green << 8 & 0x0000FF00) |
                     (blue & 0x000000FF);
          }
  
          static void Main(string[] args)
          {
              try {
                  Console.WriteLine("0x{0:X}", MergeARGB(255, 111, 111, 111));
                  Console.WriteLine("0x{0:X}", MergeARGB(1, 65, 192, 128));
                  Console.WriteLine("0x{0:X}", MergeARGB(0, 255, 255, 300));
              }
              catch (InvalidArgumentException e) {
                  Console.WriteLine(e.Message);
                  Console.WriteLine($"Argument:{e.Argument}, Range:{e.Range}");
              }
          }

      }
  }
  ```
>>>
### 실행 결과
0xFF6F6F6F  
0x141C080  
Exception of type 'MyException.InvalidArgumentException' was thrown.  
Argument:300, Range:0~255  
>>>


<br><br>

## 12.7 예외 필터하기

- C# 6.0부터는 catch 절이 받아들일 예외 객체에 제약 사항을 명시해서 해당 조건을 만족하는 예외 객체에 대해서만 예외 처리 코드를 실행할 수 있도록 하는 예외 필터(Exception Filter)가 도입되었다.
- catch() 절 뒤에 when 키워드를 이용해서 제약 조건을 기술하면 된다.
- (when을 if라고 생각하면서 읽으면 필터 코드를 이해하기가 쉽다)
  ```cs
  class FilterableException : Exception {
    public int ErrorNo { get; set; }
  }

  try{
    int num = GetNumber();

    if( num < 0 || num > 10)
        throw new FilterableException() { ErrorNo = num; }
    else
      Console.WriteLine($"Output : {num}");
  }
  catch( FilterableException e ) when ( e.ErrorNo < 0 ) {
    Console.WriteLine("Negative input is not allowed.");
  }
  catch( FilterableException e ) when ( e.ErrorNo > 10 ) {
    Console.WriteLine("Too big number is not allowed.");
  }
  ```

<br><br>

## 12.8 예외 처리 다시 생각해보기

- try~catch 문을 이용한 예외 처리는 실제 일을 하는 코드와 문제를 처리하는 코드를 깔끔하게 분리시킴으로써 코드를 간결하게 만들어준다.
- 예외 객체의 StackTrace 프로퍼티를 통해 문제가 발생한 부분의 소스코드 위치를 알려주기 때문에 디버깅이 아주 용이하다.
  ```cs
  using System;
  
  namespace StackTrace
  {
      class MainApp
      {
          static void Main(string[] args)
          {
              try
              {
                  int a = 1;
                  Console.WriteLine(3 / --a);
              }
              catch( DivideByZeroException e )
              {
                  Console.WriteLine(e.StackTrace);
              }
          }
      }
  }
  ```
>>>
### 실행 결과
   at StackTrace.MainApp.Main(String[] args) in D:\Mirero_My_Wish\Src\C# Projects\C_Shap_Learn_Solution\12.8 StackTrace\MainApp.cs:line 12
>>>
