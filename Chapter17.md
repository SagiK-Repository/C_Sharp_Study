# 17. dynamic 형식

## 17.1 dynamic 형식 소개

- dynamic 형식도 데이터 형식이다.
- 형식 검사를 하는 시점이 프로그램 실행 중이라는 점이 다르다.
- 컴파일어가 dynamic 키워드를 만나면 프로그램을 실행할 때 형식 검사를 하도록 미룬다.
  ```cs
  class MyClass {
    public void FuncAAA() {/*...*/}
  }

  // 일반
  class MainApp {
    static void Main(string[] args){
      MyClass obj = new MyClass();
      obj.FuncAAA();
      obj.FuncBBB(); // 컴파일 에러가 발생한다.
    }
  }

  // dynamic 형식
  class MainApp {
    static void Main(string[] args){
      dynamic obj = new MyClass();
      obj.FuncAAA();
      obj.FuncBBB(); // 컴파일러 형식 검사를 피해간다.
    }
  }
  ```

<br>

### 17.1.1 덕 타이핑

- "오리처럼 걷고 오리처럼 헤엄치며 오리처럼 꽉꽉 거리는 새를 봤을 때, 나는 그 새를 오리라고 부른다."
  - 미국의 시인인 제임스 휘트컴 하일리(1849~1916)의 시에서 인용된 것으로, 덕 타이핑(Duck Typing)을 가장 잘 설명하는 문장이다.
- 덕 타이핑은 객체지향 프로그래밍과는 상당히 다른 각도에서 형식을 바라본다.
- 객체지향 프로그래밍에서는 C#에서 어떤 형식의 오리(Duck)라고 인정받으려면 그 형식의 조상중에 오리가 이어야 한다.
- 반면 덕 타이핑은 어떤 형식이 오리로 인정받으려면 오리처럼 걷고, 오리처럼 헤엄치고, 오리처럼 꽉꽉거리면 된다.
- 이 형식이 어느 형식으로부터 상속받았는지는 전혀 중요하지 않다.
  ```cs
  class Duck {
    public void Walk() { Console.WriteLine( "Duck.Walk" );}
    public void Swim() { Console.WriteLine( "Duck.Swim" );}
    public void Quack() { Console.WriteLine( "Duck.Quack" );}
  }

  class Mallad : Duck { // Mallad는 Duck 으로부터 상속을 받으므로 Duck라고 인정할수 있다.
    //...//
  }

  class Robot { // 덕 타이핑 관점에서는 Robot도 오리이다.
    public void Walk() { Console.WriteLine( "Robot.Walk" );}
    public void Swim() { Console.WriteLine( "Robot.Swim" );}
    public void Quack() { Console.WriteLine( "Robot.Quack" );}
  }
  ```
- C#에서는 dynamic 형식을 통해 형식 검사를 실행할 때 미룬다는 점을 이용한다.
  ```cs
  Duck[] arr = new Duck[]{ new Duck(), new Mallad(), new Robot() }; // 오류
  
  dynamic[] arr = new Duck[]{ new Duck(), new Mallad(), new Robot() };

  foreach (dynamic duck in arr) { // 문제없이 동작한다.
    Console.WriteLine( duck.GetType() );
    duck.Walk();
    duck.Swim();
    duck.Quack();

    Console.WriteLine();
  }
  ```

<br><br>

## 17.2 COM.과 .NET 사시의 상호 운용성을 위한 dynamic 형식

- 과거에 COM은 매우 중요했다. 현재도 많이 쓰이고 있다.
- COM 컴포넌트는 그래픽 프로그래밍에 서툰 프로그래머가 화려한 차트 기능을 애플리케이션에 넣게 해주고, 엑셀 문서의 파일 구조를 몰라도 엑셀 문서를 읽거나 쓸 수 있게 해준다.
- C#을 비롯한 .NET 언어들은 RCW(Runtime Callable Wrapper)를 통해 COM 컴포넌트를 사용할 수 있다.
- RCW는 .NET이 제공하는 Type Library Importer(tlbimp.exe)를 이용해서 만들 수 있는데, 비주얼 스튜디오를 사용해서 COM 객체를 프로젝트 참조에 추가하면 IDE가 자동으로 tlbimp.exe를 호출해 RCW를 만든다.
- RCW는 COM에 대한 프록시 역할을 함으로써, C# 코드에서 .NET 클래스 라이브러리를 사용하듯 COM API를 사용할 수 있게 해준다.
- COM
  - COM은 Component Object Model의 약자로, MS 소프트웨어 컴포넌트 규격을 말한다.
  - OLE, ActiveX, COM+와 같은 파생 규격들이 모두 COM을 바탕으로 만들어졌다.
- C#이랑 COM은 서로 친화적이지 않는데 다음 이유가 있다.
  1. COM은 메소드가 결과를 반환할 때 실제 형식이 아닌 object 형식으로 반환한다. 이 때문에 C#코드에서는 이 결과를 실제 형식으로 변환해줘야 하는 하는 번거로움이 있다.
  2. COM은 오버로딩을 지원하지 않는다. 그 대신 메소드의 선택적 인수와 기본값을 지원한다. C#은 4.0버전으로 업그레이드되기 전 까지는 선택적 인수와 기본값을 지원하지 못했다. 덕분에 C# 코드에서 COM API하나를 호출하려면 사용하지도 않을 인수를 수 없이 입력해야 하는 번거로움이 있었다.
- C# 4.0버전에 이르러서야 dynamic 형식의 도입을 통해 (1)을 해결했고, 메소드의 선택적 인수 기본값 도입을 통해 (2)를 해소했다.

### 엑셀 제어 실습

- Step0. Excel이 설치 되어 있어야 한다.
- Step1. Visual Studio 새 프로젝트 > 콘솔 앱(.NET Core) > 프로젝트 이름 입력 > 프로젝트 생성
- Step2. "Program.cs"에서 "MainApp.cs"로 이름을 바꾼다.
- Step3. 솔루션 탐색기 > 종속성 항목 오른쪽 마우스 클릭 > `COM 참조 추가`
- Step4. `COM` > `형식 라이브러리` > "Micorsoft Excel xx.x Object Library" > 확인
- Step5. 솔루션 탐색기 > COM > "Interop.Micorsoft.Office.Interop.Excel" 선택 > 아래 속성창 > [Interop 형식 포함] 항목 값을 "예"로 변경
- Step6. 코드 작성
  ```cs
  using System;
  using Excel = Microsoft.Office.Interop.Excel;
  
  namespace COMInterop
  {
      class MainApp
      {
          public static void OldWay( string[,] data, string savePath )
          {
              Excel.Application excelApp = new Excel.Application();
  
              excelApp.Workbooks.Add( Type.Missing );
  
              Excel.Worksheet workSheet = ( Excel.Worksheet )excelApp.ActiveSheet;
  
              for ( int i = 0; i < data.GetLength( 0 ); i++ )
              {
                  ( ( Excel.Range )workSheet.Cells[i + 1, 1] ).Value2 = data[i, 0];
                  ( ( Excel.Range )workSheet.Cells[i + 1, 2] ).Value2 = data[i, 1];
              }
  
              workSheet.SaveAs( savePath + "\\shpark-book-old.xlsx",
                  Type.Missing,
                  Type.Missing,
                  Type.Missing,
                  Type.Missing,
                  Type.Missing,
                  Type.Missing,
                  Type.Missing,
                  Type.Missing );
  
              excelApp.Quit();
          }
  
          public static void NewWay( string[,] data, string savePath )
          {
              Excel.Application excelApp = new Excel.Application();
  
              excelApp.Workbooks.Add();
  
              Excel._Worksheet workSheet = ( Excel._Worksheet )excelApp.ActiveSheet;
  
              for ( int i = 0; i < data.GetLength( 0 ); i++ )
              {
                  workSheet.Cells[i + 1, 1] = data[i, 0];
                  workSheet.Cells[i + 1, 2] = data[i, 1];
              }
  
              workSheet.SaveAs( savePath + "\\shpark-book-dynamic.xlsx" );
              excelApp.Quit();
          }
  
          static void Main( string[] args )
          {
              string savePath = System.IO.Directory.GetCurrentDirectory();
              string[,] array = new string[,]
              {
                  { "뇌를 자극하는 알고리즘", "2009" },
                  { "뇌를 자극하는 C# 4.0",   "2011" },
                  { "뇌를 자극하는 C# 5.0",   "2013" },
                  { "뇌를 자극하는 파이썬 3", "2016" },
                  { "그로킹 딥러닝",          "2019" },
                  { "이것이 C#이다",          "2018" },
                  { "이것이 C#이다 2E",       "2020" }
              };
  
              Console.WriteLine( "Creating Excel document in old way..." );
              OldWay( array, savePath );
  
              Console.WriteLine( "Creating Excel document in new way..." );
              NewWay( array, savePath );
          }
      }
  }
  ```
- Step7. 프로그램을 실행하면 다음 메시시가 출력한 후 종료된다.  
  ```
  Creating Excel document in old way...
  Creating Excel document in new way...
  ```
- Step8. 엑셀 문서가 잘 만들어졌는지 확인한다.  
  <img src="/uploads/88718641f31e705f6cb875020d255ebb/image.png" width="70%">

<br><br>

## 17.3 동적 언어와의 상호 운용성을 위한 dynamic 형식

- 파이썬(Python)과 루비(Ruby)는 프로그래머들 사이에서 인기가 많은 동작 언어(Dynamic Languege또는 Dynamic Typed Language)이다.
  - 파이썬 : 귀도 반 로썸(Guido van Rossum)
  - 루비 : 마츠모토 유키히로
- CLR(Common Languege Runtime)은 IL(Intermediate Language)로 컴파일할 수 있는 언어들은 지원하지만, 파이썬이나 루비처럼 실행할 때 코드를 해석해서 실행하는 방식의 동적 언어는 지원할 수 없다.
- MS는 동적 언어를 수행할 수 잇도록 하는 플랫폼인 DLR(Dynamic Language Runtime)을 선보였다.
- DLR은 CLR위에 동작하며, 파이썬이나 루비와 같은 동작 언어를 실행할 수 있다.
- DLR은 파이썬이나 루비같은 동적 언어의 코드에서 만ㄴ들어진 객체에 C#이나 VB같은 정적 언어의 코드에서 접근할 수 있게 해준다.
- C#에서 파이썬, 루비 코드를 실행하고 그 결과를 받아볼 수 있다.
- 이는 파이썬에 없는 라이브러리가 루비에 있었을 때, 루비언어를 통해 라이브러리를 사요할 수 있다.
  - DLR은 .NET의 일부가 되었다. MS는 DLR을 오픈소스로 개발하여 공개하고 있다.([링크](https://github.com/IronLanguages/dlr)) C#으로 되어 있기 때문에 직접 빌드해볼 수 있다.
  - 파이썬 코드를 실행하기 위해서는 IronPython을 별도로 설치해야 한다.

<br>

### DLR이 제공하는 클래스
- ScriptRuntime 클래스 : 동적 언어를 호스팅하는 시작점이다. 
- ScriptScope 클래스 : 기본적으로 네임스페이스를 나타낸다.
- ScriptEngine 클래스 : 스크립트 엔진은 언어의 구문을 나타내는 일꾼이다.
- ScriptSource 클래스 : 이 클래스는 소스 코드를 읽어들여 여러 메소드와 읽어들인 소스 코드를 다양한 방법으로 실행하는 메소드들을 제공한다.
- CompiledCode 클래스 : 이 클래스는 컴파일된 코드를 나타낸다. 한번 컴파일 해놓고 여러 번 반복해서 실행하는 코드를 나타내는데 사용된다.
  ```cs
  //ScriptRuntime 객체가 읽어 실행하는 예제
  ScriptRuntime runtime = Python.CreateRuntime();
  dynamic result = runtime.ExecuteFile("namecard.py");

  // 문자열에 담긴 파이썬 코드를 실행하는 예제
  ScriptEngine engine = Python.CreateEngine();
  ScriptScope scope = engine.CreateScope();
  scope.SetVariable("n", "박주형");
  scope.SetVariable("p", "010-1111-12345");

  ScriptSource source = engine.CreateScriptSourceFromString(
    //파이선 코드를 활용
    @" 
    class NameCard : 
      name = ''
      phone = ''

      def __init__(self, name, phone) : 
        self.name = name
        self.phone = phone
      
      def printNameCard(self) :
        print self.name + ',' + self.phone
    
    NameCard(n, p)
    "
  );

  dynamic result = source.Execute(scope);
  result.printNameCard();

  Console.WriteLine("{0}, {1}", result.name, result.phone);
  ```

<br>

### C# with Python 실습

- Step1. Visual Studio 새 프로젝트 > 콘솔 앱(.NET Core) > 프로젝트 이름 입력 > 프로젝트 생성
- Step2. "Program.cs"에서 "MainApp.cs"로 이름을 바꾼다.
- Step3. `도구` > `NuGet 패키지 관리자` > `패키지 관리자 콘솔` > 아래 패키지 관리자 콘솔이 나타난다.
- Step4. "Install-Package IronPython"을 입력한다. > 필요한 클래스 라이브러리 참조를 프로젝트에 추가해준다.
  ```
  PM > Install-Package IronPython
  ```
- Step5. 코드를 작성한다.
  ```cs
  using System;
  using System.Collections.Generic;
  using System.Linq;
  using System.Text;
  
  using Microsoft.Scripting;
  using Microsoft.Scripting.Hosting;
  using IronPython.Hosting;
  
  namespace WithPython
  {
      class MainApp
      {
          static void Main(string[] args)
          {
              // 동적 언어 코드 실행
              ScriptEngine engine = Python.CreateEngine();
              ScriptScope scope = engine.CreateScope();
              scope.SetVariable("n", "박상현");
              scope.SetVariable("p", "010-123-4566");
  
              // 파이썬 소스 코드 읽어들이기
              ScriptSource source = engine.CreateScriptSourceFromString(
                  @"
  class NameCard :
      name = ''
      phone = ''
  
      def __init__(self, name, phone) : 
          self.name = name 
          self.phone = phone
  
      def printNameCard(self) : 
          print self.name + ', ' + self.phone
  
  NameCard(n, p)
  ");
              // ScriptSource method : 파이썬 코드 실행하여 결과 반환
              dynamic result = source.Execute(scope);
              result.printNameCard();  // 객체 메소드 호출 가능
  
              Console.WriteLine("{0}, {1}", result.name, result.phone);  // 필드 접근 가능
          }
      }
  }
  ```
- Step6. 실행결과를 확인한다.
