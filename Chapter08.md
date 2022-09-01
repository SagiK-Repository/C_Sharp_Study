# 8. 인터페이스와 추상 클래스


## 8.1 인터페이스의 선언

  ```cs
  interface ILogger {
    void WriteLog( string message );
  }
  ```
- 인터페이스(interface)는 다음과 같이 interface 키워드를 이용해 선언한다.
- 클래스와 비슷하다. 단 접근 제한자를 사용할 수 없고, 모든 것이 public으로 선언된다.
- 인터페이스를 상속받는 클래스의 인스턴스를 만들 수 있다.
  ```cs
  class ConsoleLogger : ILogger {
    public void WriteLog( string message ){
      Console.WriteLine("{0} {1}", DataTime.Now.ToLocalTime(), message);
    }
  }

  //...//
  ILogger logger = new ConsoleLogger();
  logger.WriteLog( "Hello, World!" );
  ```
- Consolelogger 객체는 ILogger 객체로 취급할 수 있다.
- 인터페이스의 이름 앞에 'I'를 붙이는 것이 관례이다.


<br><br>

## 8.2 인터페이스는 약속이다.

- 인터페이스도 소프트웨어 내에서 USB와 같은 역할을 한다.
- 예를 들어 ILogger 인터페이스는 자신으로부터 파생될 클래스가 반드시 WriteLog() 메소드를 구현하도록 강제한다.
- 이처럼 ILogger 인터페이스를 상속받는 새로운 클래스를 선언해서 파일에 로그를 출력하도록 할 수 있고, 네트워크 너머에 있는 서버에 저장하도록 패킷을 보낼 수도 있다.

```cs
using System;
using System.IO;

namespace Interface {

    interface ILogger {
        void WriteLog(string message);
    }

    class FileLogger : ILogger {
        private StreamWriter writer;

        public FileLogger( string path ) {
            writer = File.CreateText( path );
            writer.AutoFlush = true;
        }

        public void WriteLog( string message ) {
            writer.WriteLine("{0} {1}", DateTime.Now.ToShortTimeString(), message);
        }
    }

    class ClimateMonitor {
        private ILogger logger;
        public ClimateMonitor(ILogger logger) {
            this.logger = logger;
        }

        public void start() {
            while( true ) {
                Console.Write("온도를 입력해주세요. : ");
                string temperature = Console.ReadLine();
                if (temperature == "")
                    break;

                logger.WriteLog("현재 온도 : " + temperature);
            }
        }
    }

    class MainApp {
        static void Main(String[] args) {
            ClimateMonitor monitor = new ClimateMonitor( new FileLogger("MyLog.txt") );
            monitor.start();
        }
    }

}
```

<br><br>

## 8.3 인터페이스를 상속하는 인터페이스

- 인터페이스도 인터페이스를 상속할 수 있다.
- 기존의 인터페이스에 새로운 기능을 추가한 인터페이스를 만들고 싶을 때, 인터페이스를 상속하는 인터페이스를 만들면 된다.
  ```cs
  interface ILogger {
    void WriteLog( string message );
  }

  interface IFormattableLogger : ILogger {
    void WriteLog(string format, params Object[] args);
  }

  class ConsoleLogger2 : IFormattableLogger {
    public void WriteLog( string message ) {/*...*/}
    public void WriteLog( string format, params Object[] args ) {/*...*/}
  }

  class MainApp{
    static void Main(string[] args){
      IFormattableLogger logger = new ConsoleLogger2();
      logger.WriteLog( "The World is not flat." );
      logger.WriteLog( "{0} + {1} = {2}", 1, 1, 2 );
    }
  }
  ```

<br><br>

## 8.4 여러 개의 인터페이스, 한꺼번에 상속하기

- 클래스는 여러 클래스를 한꺼번에 상속할 수 없다. C#은 클래스의 다중상속을 허용하지 않는다.
- 인터페이스는 내형이 아닌 외형을 물려준다.
```cs
interface IRunnable {
  void Run();
}

interface IFlyable{
  void Fly();
}

class FlyingCar : IRunnable, IFlyable {
  public void Run() {/*...*/}
  public void Fly() {/*...*/}
}

//..//
FlyingCar car = new FlyingCar();
car.Run(); car.Fly();

IRunnable runnable = car as IRunnable;
runnable.Run();

IFlyable flyable = car as IFlyable;
flyable.Fly();
```


<br><br>

## 8.5 인터페이스의 기본 구현 메소드

- 인터페이스가 선언하는 메소드는 파생될 클래스가 무엇을 구현해야 할지를 정의하는 역할들을 다루었다.
- 인터페이스의 다른 메소드와는 역할이 약간 다르다.
  ```cs
  interface ILogger {
    void WriteLog( string message );
    void WriteError( string error ){
      WriteLog($"Error: {error}"); // WriteError()에 기본 구현을 제공한다.
    }
  }
  class ConsoleLogger : ILogger {
    public void WriteLog( string message ) {/*...*/}
  }

  ILogger logger = new ConsoleLogger();
  logger.WriteLog( "System UP" );
  logger.WriteError( "System Fail" );

  ConsoleLogger clogger = new ConsoleLogger();
  colgger.WriteLog( "System UP" );
  //colgger.WriteError( "System Fail" ); // 컴파일 에러
  ```

<br><br>

## 8.6 추상 클래스: 인터페이스와 클래스 사이

- 추상 클래스는 "구현"을 가질 수 있다. 인스턴스는 가질 수 없다.
- 추상 클래스는 다음과 같이 abstract 한정자와 class 키워드를 이용해 선언한다.
- 인터페이스는 모든 메소드가 public, 클래스는 private 이다.
  ```cs
  abstract class AbstractBase {
    public abstract void SomeMethod();
  }

  class Derived : Base {
    public override void SomeMethod() {/*...*/}
  }
  ```
