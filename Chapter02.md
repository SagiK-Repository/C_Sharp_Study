# 2. 처음 만드는 C# 프로그램

## 2.1 Hellow, World!
- 간단한 프로그램 하나 만들어 본다.
  1. Visual Studio 실행
  2. 새프로젝트 만들기
  3. `C# 콘솔앱(.NET Core)` 선택 > 프로젝트 이름 지정 > `만들기`
  4. 프로젝트가 생성되며, 코드 편집기와 솔루션 탐색기가 나타난다.
  5. `프로젝트` > `속성` > `애플리케이션` > `대상프레임워크` > `.NET 5.0`으로 지정
  6. 솔루션 탐색기 > "Program.cs"를 "MainApp.cs"로 이름 변경
  7. 소스 코드 작성
    ```cs
    using System;
    using static Sysyem.Console;
    namespace Hello
    {
        class MainApp{

            //프로그램이 시작되는 곳
            static void Main(string[] args){
                if (args.Length == 0) {
                    Console.WriteLine("사용법 : Hello.exe <이름>");
                    return;
                }
                
                WriteLine("Hello, {0}!", args[0]);
            } 
        }
    }
    ```
  8. <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>B</kbd> 를 입력하거나 `빌드` > `솔루션 빌드` 를 눌러 컴파일을 진행한다. 문제가 없으면 bin\Debug 폴더에 .exe가 생성된다.
  9.  cmd를 통해 .exe파일 위치로 이동한다.
  10.  `Hellow.exe World` 또는 `Hellow World`를 입력한다. (.exe 생략 가능)

<br><br>  

## 2.2 첫 번째 프로그램 뜯어보기
- `using System;`
  - using
    - C#의 키워드 중 하나.
    - C# 언어의 규격에 미리 정의되어 있는 특별한 단어이다.
    - using namespace, 네임스페이스 내의 클래스를 사용하겠다고 컴파일러에게 알리는 뜻이다.
  - System
    - C# 코드가 기본적으로 필요로 하는 클래스를 담고있는 네임스페이스
    - System.Console.WriteLine(); > Console.WriteLine();을 사용하기 위함
  - `;`
    - 컴파일러에게 문장의 끝을 알리는 기호이다.
    - 세미클론을 없엘수도 있었지만, 세미클론으로 인해 얻는 장점이 더 많다고 판단하여 사용한다.
       ```cs
       a = 1; b = 2; c = 3; d = 4; e = 5; //한 문장에 몰아넣을 수 있다.
       Text = "Hello, World !!"
       + "Hello, World !!"
       + "Hello World" ;
       ```
- `using static System.Console;`
  - 이는 System.Console.WriteLine(); > Console.WriteLine(); > WriteLine(); 으로 줄여준다.
  - `using static` : 어떤 데이터 형식(예 : 클래스)의 **정적 맴버**를 데이터 형식의 이름을 명시하지 않고 참조하겠다고 선언하는 기능이다.

- `namespace Hello{}`
  - 네임스페이스는 성격이나 하는 일이 비슷한 클래스, 구조체, 인터페이스, 대리자, 열거 형식 등을 하나의 이름 아래 묶는 일을 한다.
  - 다른 네임스페이스에서 MainApp 클래스를 사용하려면 `using Hello;`문장을 이용해서 네임스페이스를 참조하거나 Hello.MainApp 처럼 클래스가 소속되어 있ㄴ느 네임스페이스와 클래스의 이름을 붙여줘야 한다.

- `class MainApp{}`
  - MainApp라는 클래스를 만든다.
  - 클래스는 C# 프로그램을 구성하는 기본 단위로서 데이터와 데이터를 처리하는 기능(메소드 Method)으로 이루어 진다.
  - 이 클래스 코드블록 안에는 Main() 메소드가 담겨 있다.

- `//` : 주석이다. `/* ~ */`로 여러줄을 주석처리할 수 있다.
- `static void Main(string[] args) {}`
  - 프로그램의 진입점으로 프로그램이 시작하면 실행되고, 이 메소드가 종료되면 프로그램도 종료된다.
  - 모든 프로그램은 Main이라는 이름을 가진 메소드를 하나 가지고 있어야 한다.
  - `static`은 한정자(modifier)로서 메소드나 변수 등을 수식한다.
    - C# 프로그램의 각 요소는 `코드가 실행되는 시점에 메모리가 할당`된다.
    - 그러나 `static 키워드`로 수식되는 코드는 `프로그램이 처음 구동될 때 부터 진작에 메모리에 할당된다는 특성`이 있다.
    - static이 없다면, CLR(Common Languege Runtime)은 프로그램을 메모리에 올린 후 진입점을 찾을 때 못 찾는다.
  - `void`는 메소드의 반환 형식이다. void는 영어로 "비어 있는"이라는 뜻이다.
  - `string[] args` : Hello.exe C# 이라 실행했을 때, args에는 C#이라는 파라미터를 받아온다.

<br><br>  

## 2.3 CLR에 대하여
- C#으로 만든 프로ㅡ램은 CLR(Common Languege Runtime)위에서 실행된다.
  ```mermaid
  flowchart TB
  subgraph T1["하드웨어(x86/x64, ARM, ...)"]
    subgraph T2["운영체제(Windows, Linux. OS X, ...)"]
      subgraph T3["Common Languege Runtime"]
        subgraph T4[".NET 라이브러리"]
          A_1["C# 애플리케이션"]
          A_2["VB.NET 애플리케이션"]
          A_3["C++ 애플리케이션"]
          A_4["..."]
        end
      end
    end
  end
  ```
  - CLR은 자바의 실행환경인 자바 가상머신과 비슷한역할을 한다.
  - C# 컴파일러는 C# 소스코드를 컴파일 해서 IL(Intermediate Language)라는 중간 언어로 작성된 실행파일을 만들어 낸다. 사용자가 이 파일을 실행시키면 CLR이 중간코드를 읽어들여 다시 하드웨어가 이해할 수 있는 네이티브 코드로 컴파일 한 후 실행시킨다. 이것을 JIT(Just In Time)컴파일이라고 불린다. (실시간 컴파일 실행)
  - 이와 같이 IL을 통해 중간과정을 거치는 이유는, 다른 언어도 지원하기 위해서이다. IL의 중간언어에서 자신이 설치된 플랫폼에 최적화시켜서 컴파일 한 후 실행하는 것이다.
  - CLR은 언어들을 동작시키는 것 외에도, 프로그램 오류를 처리하도록 도와주는 기능, 언어간의 상속 지원, COM과의 상호 운영성 지원, 자동 메모리 관리 기능(가비지 컬렉션)을 제공한다.
