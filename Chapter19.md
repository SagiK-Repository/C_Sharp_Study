# 19. 스레드와 태스크

## 19.1 프로세스와 스레드

- 오늘날 운영체제는 여러 프로세스(Process)를 동시에 실행할 수 있는 능력이 있다.
- 멀티 스레드를 이용했을 때 얻는 장점과 단점
  - 장점
    - 사용자 대화형 프로그램에서 `응답성을 높일 수 있다.`
    - 멀티 스레드 방식이 `자원 공유가 쉽다.`
    - 스레드를 띄울 때 이미 프로세스에 할당된 메모리와 자원을 그대로 사용하므로 메모리와 자원을 절약할 수 있어 `경제성이 좋다.`
  - 단점
    - 멀티 스레드 구조의 소프트웨어는 `구현하기가 매우 까다롭다.`
    - 자식 스레드 중 하나에 문제가 생기면, 전체 프로세스에 영향을 준다. 즉, `안정성을 악화시킬 수 있다.`
    - `스레드를 너무 많이 사용하면 오히려 성능이 저하된다.`


<br>

### 19.1.1 스레드 시작하기

- C#에서 스레드를 배우는 방법에 대해 안다.
- .NET은 스레드를 제어하는 클래스로, System.Threading.Thread를 제공한다.
  1. Thread의 인스턴스를 생성한다. 이때 생성자의 인수로 스레드가 실행할 메소드를 넘긴다.
  2. Thread.Start() 메소드를 호출하여 스레드를 시작한다.
  3. Thread.Join() 메소드를 호출하여 스레드가 끝날 때 까지 기다린다.
  ```cs
  static void DoSomething() {
    for(int i = 0; i < 5; i++) {
      Console.WriteLine("DoSomething : {0}", i);
      Thread.Sleep(10);
    }
  }

  static void Main(string[] args){
    Thread t1 = new Thread(new ThreadStart(DoSomething)); // 1) 스레드 인스턴스 생성

    t1.Start(); // 2) 스레드 시작
    t1.Join(); // 3) 스레드 종료 대기
  }

  ```
- 실습
  ```cs
  using System;
  using System.Threading;
  
  namespace BasicThread
  {
      class MainApp
      {
          static void DoSomething()
          {
              for (int i = 0; i < 5; i++)
              {
                  Console.WriteLine("DoSomething : {0}", i);
                  Thread.Sleep(10); // 인수(10)ms 만큼 CPU 사용을 멈춘다.
              }
          }
  
          static void Main(string[] args)
          {
              Thread t1 = new Thread(new ThreadStart(DoSomething)); // 1) 스레드 인스턴스 생성
  
              Console.WriteLine("Starting Thread...");
              t1.Start();
  
              for (int i = 0; i < 5; i++)
              {
                  Console.WriteLine($"Main : {i}");
                  Thread.Sleep(10);
              }
  
              Console.WriteLine("Waiting until thread stops...");
              t1.Join();
  
              Console.WriteLine("Finished");
          }
      }
  }
  ```

>>>
### 실행 결과
Starting Thread...  
Main : 0  
DoSomething : 0  
DoSomething : 1  
Main : 1  
DoSomething : 2  
Main : 2  
DoSomething : 3  
Main : 3  
DoSomething : 4  
Main : 4  
Waiting until thread stops...  
Finished  
>>>

<br>

### 19.1.2 스레드 임의로 종료시키기

- 사용자가 작업관리자 등을 이용해서 프로세스를 임의로 죽일 수 있다.
- 스레드를 죽이려면, Thread 객체의 Abort() 메소드를 이용해야 한다.
  ```cs
  static boid DoSomething() {
    try {
      for (int i = 0; i < 10000; i++){
        Console.WriteLine("DoSomething : {0}", i);
        Thread.Sleep(10);
      }
    } catch(ThreadAbortedException e) {/*...*/} finally {/*...*/}
  }

  static void Main(string[] args) {
    Thread t1 = new Thread(new ThreadStart(DoSomething));

    t1.Start();
    t1.Abort(); // 스레드 취소(종료)
    t1.Join();
  }
  ```
<br>

### 19.1.3 스레드의 일생과 상태 변화

- 스레드는 살아가면서 여러가지 상태변화를 격는다.
- .NET은 스레드의 상태를 ThreadState 열거형에 정의
- 다음과 같은 몇가지 스레드의 상태가 정의되어 있다.
  - Unstarted : 스레드 객체를 생성한 후 Thread.Start() 메소드가 호출되기 전 상태
  - Running : 스레드가 시작하여 동작중인 상태.
  - Suspended : 스레드의 일시 중단 상태이다. Thread.Suspended()로 가능.
  - WaitSleepJoin : 스레드가 블록(Block)된 상태. Monitor.Enter(), Thread.Sleep(), Thread.Join() 메소드를 호출하면 이 상태가 된다.
  - Aborted : 스레드가 취소된 상태. Aborted 상태가 된 스레드는 다시 Stopped 상태로 전환되어 완전히 중지된다.
  - Stopped : 중지된 스레드의 상태.
  - Background : 스레드가 백그라운드로 동작함을 나타낸다. Thread.IsBackground 속성에 true 값을 입력하면 이상테가 된다.

<br>

### ThreadState in Flags

- ThreadState가 Flags 애트리뷰트를 갖고 있다.
- Flags는 자신이 수식하는 열거형을 비트 필드(Bit Field), 즉 플래그 집합으로 처리할 수 있음을 나타낸다.
  ```cs
  enum MuEnum {
    Apple = 1 << 0, // 1(0001)
    Orange = 1 << 1,
    Kiwi = 1 << 2,
    Mango = 1 << 3
  };

  Console.WriteLine( (MyEnum)1 ); // Apple
  Console.WriteLine( (MyEnum)2 ); // Orange
  Console.WriteLine( (MyEnum)4 ); // Kiwi
  Console.WriteLine( (MyEnum)8 ); // Mango
  Console.WriteLine( (MyEnum)(1 | 4) ); // 5 // 열거 요소에 대응하지 못하는 값은 원래 값으로 표현된다.
  Console.WriteLine( (MyEnum)(1 | 8) ); // 9 

  [Flags]
  enum MuEnum {
    Apple = 1 << 0,
    //...//
  };

  Console.WriteLine( (MyEnum)(1 | 4) ); // Apple, Kiwi // Flag 애트리뷰트는 열거형의 요소들을 집합으로 구성되는 값들로 표현할 수 있다.
  Console.WriteLine( (MyEnum)(1 | 8) ); // Apple, Mango

  if( t1.ThreadState & ThreadState.Abortes == ThreadState.Abortes )
    Console.WriteLine("스레드가 정지했습니다.");
  else if (t1.ThreadState & ThreadState.Stopped == ThreadStated.Stopped)
    Console.WriteLine("스레드가 취소되었습니다.");
  ```
- 실습
  ```cs
  private static void PrintThreadState(ThreadState state) {
      Console.WriteLine("{0,-16} : {1}",state, (int)state );
  }
  static void Main(string[] args) {
      PrintThreadState(ThreadState.Running);
      PrintThreadState(ThreadState.StopRequested);
      PrintThreadState(ThreadState.SuspendRequested);
      PrintThreadState(ThreadState.Background);
      PrintThreadState(ThreadState.Unstarted);
      PrintThreadState(ThreadState.Stopped);
      PrintThreadState(ThreadState.WaitSleepJoin);
      PrintThreadState(ThreadState.Suspended);
      PrintThreadState(ThreadState.AbortRequested);
      PrintThreadState(ThreadState.Aborted);
      PrintThreadState(ThreadState.Aborted | ThreadState.Stopped);
  }
  ```
>>>
### 실행 결과
Running          : 0  
StopRequested    : 1  
SuspendRequested : 2  
Background       : 4  
Unstarted        : 8  
Stopped          : 16  
WaitSleepJoin    : 32  
Suspended        : 64  
AbortRequested   : 128  
Aborted          : 256  
Stopped, Aborted : 272  
>>>

<br>

### 19.1.4 인터럽트: 스레드를 임의로 종료하는 다른 방법

- 불가피하게 스레드를 강제로 종료시켜야 하는 경우가 있다.
- Thread.Interrupt() 메소든는 스레드가 이미 WaitSleepJoin 상태에 있으면 즉시 중단하고, 그렇지 않으면 WaitSleepJoin가 되어서야 중단된다.
- 이 덕분에 최소한의 코드가 "절대 중단되면 안되는 작업"을 하고 있을 때는 중단되지 않는다는 보장을 받을 수 있다.
  ```cs
  static void DoSomething() {
    try {
      for (int i = 0; i < 10000; i++){
        Console.WriteLine("DoSomething : {0}", i);
        Thread.Sleep(10);
      }
    } catch (InterruptedException e) {/*...*/} finally {/*...*/}
  }

  static void Main(string[] args){
    Thread t1 = new Thread(new ThreadStart(DoSomething));

    t1.Start();
    t1.Interrupt(); // 스레드 취소
    t1.join();
  }
  ```

<br>

### 19.1.5 스레드 간의 동기화

- 스레드들이 순서를 갖춰 자원을 사용하게 하는 것을 "동기화(Synchronization)" 이라고 한다.
- 이를 통해 멀티 스레드 프로그래밍을 완변하게 할 수 있다.
- 스레드 동기화에서 가장 중요한 것은 "자원을 한 번에 하나의 스레드가 사용하도록 보장"하는 것이다.
- .NET이 제공하는 대표적인 도구로 Lock 키워드와 Monitor 클래스가 있다.
- lock 키워드와 Monitor 클래스 둘다 하는 일은 거의 유사하지만, lock가 쉽게 사용할 수 있고 Monitor 클래스가 세심한 동기화 제어 기능을 제공한다.

<br>

### lock 키워드로 동기화 하기

- 크리티컬 섹션(Critical Section)은 한 번에 한 스레드만 사용할 수 있는 코드영역이다.
- C#에서는 lock 키워드로 감싸주기만 해도 평범한 코드를 크리티컬 섹션으로 바꿔준다.
  ```cs
  class Counter {
    public int count = 0;
    public void Increase(){
      count = count + 1;
    }
  }
  //...//
  CounterClass obj = new CounterClass();
  Thread t1 = new Thread(new ThreadStart(obj.Increase));
  Thread t2 = new Thread(new ThreadStart(obj.Increase));
  Thread t3 = new Thread(new ThreadStart(obj.Increase));

  t1.Start(); t2.Start(); t3.Start();
  t1.Join(); t2.Join(); t3.Join();

  Console.WriteLine( obj.count ); // obj.count = ??
  
  // lock 키워드로 동기화
  class Counter {
    public int count = 0;
    public void Increase(){
      lock ( thisLock ) { // 한 스레드가 이 코드를 실행하다가 lock 블록이 끝나는 괄호를 만나기 까지 다른 스레드는 이 코드를 실행할 수 없다.
        count = count + 1;
      }
    }
  }
  ```
- lock 키워드의 매개변수로 사용하는 객체는 참조형이면 어느 것이든 슬 수 있지만, public 키워드 등을 통해 외부 코드에서도 접근할 수 있는 다음 세 가지는 절대 사용하지 않기를 권한다.
  - this : lock(this)는 나쁜 버릇이다.
  - Type 형식 : lock(typeod(SomeClass))나 lock(obj.GetType())는 피해라.
  - string 형식 : 절대로 string 객체로 lock 하지 말것. lock("abc")와 같은 코드는 안된다.

<br>

### 모니터로 동기화 하기

- Monitor 클래스는 스레드 동기화에 사용하는 몇 가지 정적 메소드를 제공한다.
- Monitor.Enter() 메소드는 크리티컬 섹션을 만들며, Monitor.Exit() 메소드는 크리티컬 섹션을 제거한다.
  ```cs
  class Counter {
    public int count = 0;
    public void Increase(){
      Monitor.Enter( thisLock );
      try{
        count = count + 1;
      } finally {
        Monitor.Exit( thisLock );
      }
    }
  }
  ```

<br>

### Monitor.Wait()와 Monitor.Pulse()로 하는 저수준 동기화

- lock 키워드 대신 Monitor 클래스를 사용해야 한다면, Enter() 와 Exit() 메소드 때문이 아니라 Wait()와 Pulse() 메소드 때문이다. 덕분에 세심한 동기화 제어가 가능하다.
- 다음 순서를 거쳐 진행한다.
  1. 클래스 안에 다음과 같이 동기화 객체 필드를 선언한다.
  ```cs
  readonly object thisLock = new object();  
  ```
  2. 아울러 스레드를 Wait SleepJoin 상태로 바꿔 블록시킬 조건을 결정할 필드를 선언한다.
  ```cs
  bool lockedCount = false;
  ```
  3. 이제 본격적인 동기화 코드를 작성한다. 스레드를 블록 시키고 싶은 곳에 다음과 같이 lock 블록 안에서 2)에서 선언한 필드를 검사하여 Monitor.Wait()를 호출한다.
  ```cs
  lock(thisLock) {
    while ( count > 0 || lockedCount == true )
      Monitor.Wait ( thisLock );
    //...//
  }
  ```
  4. 3)에서 선언한 코드는 coun가 0 보다 크거나 lockedCount가 true면 해당 스레드는 블록된다.  
  이렇게 블록되어 있던 스레드가 깨어나려면, 2)에서 선언한 lockedCount의 값을 true로 바꾼다.  
  이렇게 해두면 다른 스레드가 이 코드에 접근할 때 3)에서 선언해둔 블로킹 코드에 걸려 같은 코드를 실행할 수 없게 된다.  
  작업을 마치면 lockedCount의 값을 false로 바꾼 뒤 Monitor.Pulse()를 호출한다. 그럼 WaitingQueue에 대기하고 있던 다른 스레드가 깨어나 flase로 바뛴 lockedCount를 보고 작업을 수행할 것이다.  
  ```cs
  lock ( thisLock ) {
    while ( count > 0 || lockedCount == true )
      Monitor.Wait ( thisLock );
    
    lockedCount = true;
    count++;
    lockedCount = false;

    Monitor.Pulse( thisLock );
  }
  ```

<br><br>

## 19.2 Task와 Task<TResult> 그리고 Parallel

- .NET 프레임워크 4.0부터 도입된 System.Threading.Tasks 네임스페이스에는 병행성 코드나 비동기 코드를 쉽게 작성할 수 있도록 돕는 클래스다 있다.
- 그중 Task와 Task<TResult>, Parallel 클래스를 알아본다.

<br>

### 19.2.1 System.Threading.Tasks.Task 클래스

- Task 클래스는 System.Threading.Tasks 네임스페이스의 주인공이다.
- 비동기 코드를 손쉽게 작성할 수 있도록 도와준다.
- 비동기(Asynchronous) 코드를 알기 위해서 동기(Synchronous) 코드에 대해서 알아야 한다.
  - 동기 코드는 메소드를 호출한 뒤에 이 메소드의 실행이 완전히 종료되어야함 다음 메소드를 실행할 수 있다.
  - 비동기 코드는 메소드를 호출한 뒤 메소드의 종료를 기다리지 않고 다음 코드를 실행한다.
  ```cs
  Action SomeAction = () => { //Action 대리자
    Thread.Sleep(1000);
    Console.WriteLine( "Printed asynchronously." );
  };

  Task myTask = new Task( SomeAction ); // 생성자에서 넘겨받은 무명 함수를 비동기로 호출한다.
  myTask.Start();

  Console.WriteLine( "Printed synchronously." );

  myTask.Wait(); // myTask 비동기 호출이 완료될 때까지 기다린다.

  // 결과는
  // Printed synchronously.
  // Printed asynchronously.


  // Task.Run() 활용
  var myTask = Task.Run( ()=>{
      Thread.Sleep(1000);
      Console.WriteLine( "Printed asynchronously." );
    }
  );

  Console.WriteLine( "Printed synchronously." );

  myTask.Wait();
  // 결과는
  // Printed synchronously.
  // Printed asynchronously.

  t3.RunSynchronously(); // 동기 실행
  ```

<br>

### 19.2.2 코드의 비동기 실해 결과를 주는 Task<TResult> 클래스

- Thread 클래스 만으로 결과를 취합해야 한다면 일이 복잡해진다.
- Task<TResult>는 코드의 비동기 실행 결과를 손쉽게 취합할 수 있다.
  ```cs
  var myTask = new Task< List<int> >.Run( () => {
    Thread.Sleep(1000);

    List<int> list = new List<int>();
    list.Add(3);
    list.Add(4);
    list.Add(5);

    return list;
  });

  var myList = new List<int>();
  myList.Add(0);
  myList.Add(1);
  myList.Add(2);

  myTask.Wait();
  myList.AddRange(myTask.Result.ToArray()); // 0 1 2 3 4 5
  ```

<br>


### 19.2.3 손쉬운 병렬 처리를 가능케 하는 Parallel 클래스

- Taks<TResult> 덕분에 병렬 수행하도록 프로그램을 구현하는 것은 크게 어렵지 않다.
- System.Threading.Task.Parallel 클래스는 for(), Foreach() 등의 메소드를 제공함으로써 더 쉽게 구현할 수 있다.
  ```cs
  void SomeMethod( int i ) {
    Console.WriteLine( i );
  }
  //...//

  Parallel.For( 0, 100, SomeMethod );
  ```

<br><br>

## 19.3 async 한정자와 await 연산자로 만드는 비동기 코드


- async 한정자와 await 연산자는 C# 5.0에서 새롭게 도입된 장치지만 일부 얼리아답터 프로그래머들은 C# 5.0이 공개되기 전부터 Cω(C 오메가)를 통해 이들의 존재와 사용법을 알고 있었다.
- C#의 사촌격인 Cω는 마이크로소프트위 연구용 언어이다.
  - 이전에는 C# 1.0버전부터 BeginInoke()/EndInvoke() 메소드를 이용하는 비동기 코드 패턴을 지원했다.
- async 한정자는 메소드, 이벤트 처리기, 태스크, 람다식 등을 수식함으로써 C# 컴파일러가 이들을 호출하는 코드를 만날 때 호출 결과를 기다리지 않고 바로 다음 코드로 이동하도록 실행코드를 생성하게 한다.
  ```cs
  public static async Task MyMethodAsync() {/*...*/}
  ```
- "async로 한정한 Task 또는 Task<Tresult>를 반환하는 메소드/태스크/람다식은 await 연산자를 만나는 곳에서 호출자에게 제어를 돌려주며, await 연산자가 없는 경우 동기로 실핸된다."
  ```cs
  void Caller(){ // Main
    //...// (1)
    MyMethodAsync();
    //...// (a)
  }

  asyn MyMethodAsync(){
    //...// (2)
    await Task.Run( async () => {
      //...// 이후 전부 (b)
    });
    //...// (b)
  }

  // (1) -> (2, 동기) -> (a), (b, 비동기) 동시 실행
  ```

<br>

### 19.3.1 .Net이 제공하는 비동기 API 맛보기

- MS는 C# 언어가 비동기 프로그래밍 패러다임을 지원하도록 만드는 한편, .NET도 기존 API에 더불어 비동기 버전 API를 새롭게 제공하도록 업그레이드를 했다.
- .NET 의 ~Async()라는 메소드가 바로 그것이다.
- 다음 표는 System.IO.Stream에서 제공하는 읽기/쓰기 메소드의 동기와 비동기 버전을 보여준다.
  | 동기 버전 메소드  |  비동기 버전 메소드  |
  |------------|--------------|
  |  Read      | ReadAsync    |
  |  Write     | WriteAsync   |
- 다음은 동기 버전과 비동기 버전을 비교한 코드이다.
  ```cs
  // 동기 버전
  static long CopySync( string FromPath, string ToPath) {
    using ( var fromStream = new FileStream(FromPath, FileMode.Open) ) {
      long totalCopied = 0;
      
      using ( var toStream = new FileStream(ToPath, FileMode.Create) ) {
        byte[] buffer = new byte[1024];
        int nRead = 0;
        while ( (nRead = fromStream.Read(buffer, 0, buffer.length)) != 0 ) {
          toStream.Write(buffer, 0, nRead);
          total.Copied += nRead;
        }
      }
      return totalCopied;
    }

  }

  // 비동기 버전
  async Task<long> CopyAsync( string FromPath, string ToPath) {
    using ( var fromStream = new FileStream(FromPath, FileMode.Open) ) {
      long totalCopied = 0;
      
      using ( var toStream = new FileStream(ToPath, FileMode.Create) ) {
        byte[] buffer = new byte[1024];
        int nRead = 0;
        while ( (nRead = await fromStream.ReadAsync(buffer, 0, buffer.Length)) != 0 ) {
          await toStream.WriteAsync(buffer, 0, nRead);
          totalCopied += nRead;
        }
      }
      return totalCopied;
    }

  }
  ```
