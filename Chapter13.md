# 13. 대리자와 이벤트

## 13.1 대리자란?

- 사장님이 돌아오면 전화달라는 것을 영어로 콜 백(Callback)이라고 부른다.
- 콜백을 작성하고 다른 코드에 이 콜백을 맡겨 대신 실행하게 된다. 콜백을 맡아줄 코드는 컴파일 시점이 아닌 프로그램 실행 중에 결정된다.
- C#에서는 콜백을 맡아 실행하는 일을 '대리자'가 담당한다.
- 대리자는 메소드에 대한 참조이다.
- 대리자는 다음과 같이 delegate 키워드를 이용해서 선언한다.
  ```cs
  delegate int MyDelegate( int a, int b );
  ```
- 대리자는 인스턴스가 아닌 형식(Type)이다. MyDelegate는 int, string과 같은 형식이며 "메소드를 참조하는 그 무엇"을 만들려면 MyDelegate의 인스턴스를 따로 만들어야 한다.
  ```cs
  int Plus( int a, int b ){ return a + b; } //class Calculator
  int Minus( int a, int b ){ return a - b; } //class Calculator

  Calculator Calc = new Calculator();
  MyDelegate Callback;

  Callback = new MyDelegate( Calc.Plus ); // 대리자의 인스턴스를 만들 때도 new 연산자가 필요하다.
  Console.WriteLine( Callback(3, 4) );

  Callback = new MyDelegate( Calc.Minus ); 
  Console.WriteLine( Callback(7, 5) );
  ```
- 대리자를 이용하여 콜백을 구현하는 과정을 요약한다.
  - 대리자를 선언한다.
  - 대리자의 인스턴스를 생성한다. 인스턴스를 생성할 때는 대리자가 참조할 메소드를 인수로 넘긴다.
  - 대리자를 호출한다.


<br><br>

## 13.2 대리자는 왜, 그리고 언제 사용하나요?

- 프로그래밍을 하다 보면, "값"이 아닌 "코드" 자체를 매개변수에 넘기고 싶을 때가 많다.
- 대리자는 메소드에 대한 참조이므로, 메소드를 참조할 대리자를 매개변수에 받을 수 있도록 정렬 메소드를 작성해 놓으면 해결된다.
  ```cs
  // 1. Compare 대리자를 선언
  delegate int Compare( int a, int b );
  // 2. Compare 대리자가 참조할 비교 메소드를 작성
  delegate int AscendCompare( int a, int b ) {
    if ( a > b ) return 1;
    else if ( a == b ) return 0;
    else return -1;
  }
  // 3. 정렬 메소드를 작성한다. 이때 매개변수로는 정렬할 배열과 비교할 메소드를 참조하는 대리자를 입력받는다.
  static void BubbleSort(int[] DataSet, Compare Comparer){
    
    int i = 0, j = 0, temp = 0;
    
    for( i = 0; i < DataSet.length - 1; i++){
      for( j = 0; j < DataSet.length - ( i + 1 ); j++){
        
        if( Comparer( Dataset[j], DataSet[j+1] ) > 0){
          temp = DataSet[j+1];
          DataSet[j+1] = DataSet[j];
          DataSet[j] = temp;
        }

      }
    }

  }
  // 4. 메소드를 호출하면 우리가 원하던 대로 정렬 방식이 분리된 정렬 코드를 얻을 수 있다.
  int [] array = { 3, 7, 4, 2, 10 };
  BubbleSort( array, new Compare( AscendCompare ) ); // array는 { 2 3 4 7 10 }이 된다.
  ```

<br><br>

## 13.3 일반화 대리자

- 대리자는 보통의 메소드 뿐만 아니라 일반화 메소드도 참조할 수 있다.
  ```cs
  delegate int Compare<T>( T a, T b );
  //...//
  static void BubbleSort<T>( T[] DataSet, Compare<T> Compare ){/*...*/}
  ```

<br><br>

## 13.4 대리자 체인

- 대리자 하나가 여러 개의 메소드를 동시에 참조할 수 있다.
- 대리자의 인스턴스가 동시에 참조할 수 있도록 += 연산자를 이용하여 결합할 수 있다.
  ```cs
  delegate void TherIsAFire( string location );

  void Call119( string location ){ Console.WriteLine("소방서죠? 불났어요! 주소는 {0}", location ); }
  
  void ShotOut( string location ){ Console.WriteLine("피하세요! {0}에 불이 났어요!",location ); }

  void Escape( string location ){ Console.WriteLine("{0}에서 나갑시다!", location ); }
  //...//
  ThereIsAFire Fire = new ThereIsAFire( Call119 );
  Fire += new ThereIsAFire( ShotOut );
  Fire += new ThereIsAFire( Escape );
  Fire( "우리집 " );
  // 소방서죠? 불났어요! 주소는 우리집
  // 피하세요! 우리집에 불이 났어요!
  // 우리집에서 나갑시다!
  ```
- 다음과 같이 대리자 체인을 만들고 뺄수 있다.
  ```cs
  ThereIsAFire Fire = new ThereIsAFire( Call119 );
  Fire += new ThereIsAFire( ShotOut );
  Fire += new ThereIsAFire( Escape );

  ThereIsAFire Fire = new ThereIsAFire( Call119 )
                    + new ThereIsAFire( ShotOut )
                    + new ThereIsAFire( Escape );
  
  ThereIsAFire Fire = (ThereIsAFire) Delegate.Combine(
                                new ThereIsAFire( Call119 ),
                                new ThereIsAFire( ShotOut ),
                                new ThereIsAFire( Escape ) );

  Fire -= new ThereIsAFire( Call119 );

  Fire = (ThereIsAFire) Delegate.Remove( new ThereIsAFire( Call119 ) );
  ```

<br><br>

## 13.5 익명 메소드

- 메소드는 한정자가 없어도, 반환할 값이 없어도(void), 매게변수가 없어도 괜찮지만 이름은 있다.
- 익명 메소드(Anonymous Method)란 이름이 없는 메소드를 말한다.
  ```cs
  delegate int Calculate( int a, int b );

  public static void Main(){
    Cacluate Calc;

    Calc = delegate (int a, int b){ // 이름을 제외한 메소드의 구현 (익명 메소드)
             return a + b; //실행하고자 하는 코드
           }
    
    Console.WriteLine( "3 + 4 : {0}", Calc( 3, 4 ) ); //  3 + 4 : 7
  }
  ```

<br><br>

## 13.6 이벤트 : 객체에 일어난 사건 알리기

- 어떤 일이 생겼을 때 이를 알려주는 객체가 필요한 경우가 있다.
- 특정시간이 되었을 때 알려주거나, 버튼을 클릭했을 때 알려주는 객체를 말한다.
- 이런 객체를 만드는 것이 바로 이벤트(Event)이다.
- 이벤트의 동작 원리는 대리자와 거의 비슷하다.
- 이벤트는 대리자를 event 한정자로 수식해서 만든다.
- 다음 절차를 통해 이벤트를 선언하고 사용한다.
    1. 대리자를 선언한다. 이 대리자는 클래스 밖에 선언해도 되고 안에 선언해도 된다.
    2. 클래스 내에 1에서 성넝한 대리자의 인스턴스를 event 한정자로 수식해서 선언한다.
    3. 이벤트 핸들러를 작성한다. 이벤트 핸들러는 1에서 선언한 대리자와 일치하는 메소드로 한다.
    4. 클래스의 인스턴스를 생성하고 이 객체의 이벤트에 3에서 작성한 이벤트 핸들러를 등록한다.
    5. 이벤트가 발생하면 이벤트 핸들러가 호출된다.
  ```cs
    // Step 1 대리자를 선언한다. 이 대리자는 클래스 밖에 선언해도 되고 안에 선언해도 된다.
    delegate void EventHandler( string message );

    class MyNotifier
    {
        // Step 2 클래스 내에 step 1 에서 선언한 대리자의 인스턴스를 event 한정자로 수식해서 선언한다.
        public event EventHandler SomethingHappened;
        public void DoSomething( int number )
        {
            int temp = number % 10;

            // number가 3, 6, 9로 끝나는 값이 될 때마다 이벤트 발생
            if ( temp != 0 && temp % 3 == 0 )
            {
                SomethingHappened( String.Format( "{0} : 짝", number ) );
            }
        }
    }

    class MainApp
    {
        // Step 3 이벤트 핸들러를 작성한다. 이벤트 핸들러는 step 1 에서 선언한 대리자와 일치하는 메소드로 한다.
        static public void MyHandler( string message )
        {
            Console.WriteLine( message );
        }

        static void Main( string[] args )
        {
            // Step 4 클래스의 인스턴스를 생성하고 이 객체의 이벤트에 step 3 에서 작성한 이벤트 핸들러를 등록한다.
            MyNotifier notifier = new MyNotifier();
            notifier.SomethingHappened += new EventHandler(MyHandler);

            for (int i = 1; i < 30; i++)
            {
                notifier.DoSomething(i);  // Step 5 : 이벤트가 발생하면 이벤트 핸들러가 호출된다.
            }
        }
    }
  ```

<br><br>

## 13.7 대리자와 이벤트

- 이벤트는 대리자에 event 키워드로 수식해서 선언한 것에 불과하다.
- 이벤트가 대라자와 가장 크게 다른 점은, 이벤트를 외부에서 직접 사용할 수 없다는 데 있다.
- 이벤트는 public 한정자로 선언되어 있어도 자신이 선언된 클래스 외부에서는 호출이 불가하다.
- 대리자는 public, internal로 수식되어 있으면 클래스 외부에서 호출이 가능하다.
  ```cs
  delegate void EventHandler( string message );
  
  class MyNotifier {
      public event EventHandler SomethingHappened;
      //...//
  }
  
  class MainApp {
      static void Main(string[] args) {
          MyNotifier notifier = new MyNotifier();
          notifier.SomethingHappened("테스트"); // Error!! 이벤트는 객체 외부에서 직접 호출할 수 없다.
      }
  }
  ```
