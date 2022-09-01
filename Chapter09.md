# 9. 프로퍼티

## 9.1 public 필드의 유혹

- public 필드의 유혹
  ```cs
  class MyClass {
    private int myFiled;
    public int GetMyFiled(){ return myFiled; }
    public void SetMyFiled( int NewValue ){ myFiled = NewValue; }
  }
  //...//
  MyClass obj = new MyClass();
  obj.SetMyFiled( 3 );
  Console.WriteLine( obj.GetMyFiled() ); // 3 출력
  ```

<br><br>

## 9.2 메소드보다 프로퍼티

- 프로퍼티는 다음과 같이 선언한다.
  ```cs
  class MyClass{
    
    private int myFiled;
    
    public int MyFiled{
      get {
        return myFiled;
      }
      set {
        myFiled = value;
      }
    }

  }
  //...//
  MyClass obj = new MyClass();
  obj.MyFiled = 3;
  Console.WriteLine( obj.MyFiled() ); // 3 출력
  ```

<br><br>

## 9.3 자동 구현 프로퍼티

- 프로퍼티는 데이터의 오염에 대해선 메소드처럼 안전하고, 데이터를 다룰 대는 필드처럼 간결하다.
  ```cs
  public class NameCard{
    private string name;
    private string phoneNumber;

    public string Name {
      get { return name; }
      set { name = value; }
    }
    public string PhoneNumber {
      get { return name; }
      set { name = value; }
    }
  }
  ```
- 코드를 더 단순하게 만드는 자동 구현 프로퍼티(Auto-Implemented Property)를 C# 3.0부터 도입했다.
  ```cs
  public class NameCard{
    public string Name{
      get; set;
    }
    public string PhoneNumber{
      get; set;
    }
  }
  ```
- C# 7.0부터는 다음과 같이 자동 구현 프로퍼티를 선언함과 동시에 초기화를 수행할 수 있다.
  ```cs
  public class NameCard{
    public string Name{ get;set; } = "Unknown";
    public string PhoneNumber{ get;set; } = "000-0000";
  }
  ```

<br><br>

## 9.4 프로퍼티와 생성자

- 객체를 생성할 때, 각 필드를 초기화 하는 또다른 방법인, 프로퍼티를 이용한 초기화 방법이 있다.
- 초기화 하고픈 프로퍼티만 넣어서 초기화하면 된다.
  ```cs
  BirthdayInfo birth = new BirthdayInfo()
    {
      Name = "서현",
      Birthday = new DateTime(1991, 6, 28)
    };
  ```

<br><br>

## 9.5 초기화 전용(Init-Only) 자동 구현 프로퍼티

- C#에는 데이터 오염을 방지할 수 있는 readonly 필드, readonly 구조체, 튜플 등이 있다.
- 프로퍼티를 읽기 전용으로 선언하는 방법이 불편하다.
  ```cs
  class Transaction {
    public Transaction( string _form, string _to, int _amount ){
      from = _from; to = _to; amount = _amount;
    }

    string from;
    string to;
    int amount;

    public string From { get{return from;} }
    public string To { get{return to;} }
    public int Amount { get{return amount;} }
  }
  ```
- C# 9.0에 이르러서는 읽기 전용 프로퍼티를 아주 간편하게 선언할 수 있도록 개선되었다.
- init 접근자는 set 접근자와 같이 외부에서 프로퍼티를 변경할 수 있지만, 객체 초기화를 할 때만 프로퍼티 변경이 가능하다는 점에서 다르다.
  ```cs
  public class Transaction {
    public string From { get; init; }
    public string To { get; init; }
    public int Amount { get; init; }
  }
  ```

<br><br>

## 9.6 레코드 형식으로 만드는 불변 객체

- 불변(Immutable) 객체는 내부 상태(데이터)를 변경할 수 없는 객체를 말한다.
- 상태를 변경할 수 없기 때문에, 불변 객체에서는 데이터 복사와 비교가 많이 이루어진다.
- 레코드(Record)는 불변 객체에서 빈번하게 이뤄지는 이 두 가지 연산을 편리하게 수행할 수 있도록 C# 9.0에서 도입된 형식이다.
- 레코드 형식은 값 형식처럼 다룰 수 있는 불변 참조 형식으로, 참조 형식의 비용 효율과 값 형식이 주는 편리함을 모두 제공한다.

<br>

### 9.6.1 레코드 선언하기

  ```cs
  record RTransaction {
    public string From { get; init; }
    public string To { get; init; }
    public int Amount { get; init; }
  }

  //...//
  RTransaction tr1 = new RTransaction{From="Alice", To="Bob", Amount=100};
  ```

<br>

### 9.6.2 with를 이용한 레코드 복사

- C# 컴파일러는 레코드 형식을 위한 복사 생성자를 자동으로 작성한다.
  ```cs
  RTransaction tr1 = new RTransaction{From="Alice", To="Bob", Amount=100};
  RTransaction tr2 = with tr1 {To="Charlie"}; //To 프로퍼티 값만 "Charlie"로 수정
  ```

<br>

### 9.6.3 레코드 객체 비교하기

- 컴파일러는 레코드의 상태를 비교하는 Equals() 메소드를 자동으로 구현한다.
  ```cs
  Ctransaction tr1 = new Ctransaction{From="Alice", To="Bob", Amount=100};
  Ctransaction tr2 = new Ctransaction{From="Alice", To="Bob", Amount=100};
  Console.WriteLine( tr1.Equals(tr2) ); // True 출력
  ```
- 실습
  ```cs
  using System;
  
  namespace RecordComp
  {
      class CTransaction
      {
          public string From { get; init; }
          public string To { get; init; }
          public int Amount { get; init; }
  
          public override string ToString()
          {
              return $"{From,-10} -> {To, -10} : ${Amount}";
          }
      }
  
      record RTransaction
      {
          public string From { get; init; }
          public string To { get; init; }
          public int Amount { get; init; }
  
          public override string ToString()
          {
              return $"{From,-10} -> {To,-10} : ${Amount}";
          }
      }
  
  
      class MainApp
      {
          static void Main(string[] args)
          {
              CTransaction trA = new CTransaction { From = "Alice", To = "Bob", Amount = 100 };
              CTransaction trB = new CTransaction { From = "Alice", To = "Bob", Amount = 100 };
  
              Console.WriteLine(trA); // Alice -> Bob : $100
              Console.WriteLine(trB);
              Console.WriteLine($"trA equals to trB : { trA.Equals(trB) }"); // False : class
              
              RTransaction tr1 = new RTransaction { From = "Alice", To = "Bob", Amount = 100 };
              RTransaction tr2 = new RTransaction { From = "Alice", To = "Bob", Amount = 100 };
  
              Console.WriteLine(tr1); // Alice -> Bob : $100
              Console.WriteLine(tr2);
              Console.WriteLine($"trA equals to trB : { tr1.Equals(tr2) }"); // True : record
          }
      }
  }
  ```
  >>>
  ### 실행결과
  Alice      -> Bob        : $100  
  Alice      -> Bob        : $100  
  trA equals to trB : False  
    
  Alice      -> Bob        : $100  
  Alice      -> Bob        : $100  
  trA equals to trB : True
  >>>




<br><br>

## 9.7 무명 형식

- 이름이 없는 형식, 즉 무명 형식(Anonymous Type).
- 무명 형식은 형식의 선언과 동시에 인스턴스를 할당한다.
- 무명 형식에 할당된 값은 변경이 불가능하다.
  ```cs
  var myInstance = new { Name = "박주형", Age = "17", Scores = new int[] {90, 80, 70, 60} };
  //...//
  Console.WriteLine( myInstance.Name, myInstance.Age );
  foreach ( var score in myInstance.Score )
    Console.Write($"{score}");
  ```

<br><br>

## 9.8 인터페이스의 프로퍼티

- 인터페이스는 메소드 뿐만 아니라 프로퍼티와 인덱서도 가질 수 있다.
  ```cs
  interface IProduct {
    string ProductName {
      get; set;
    }
  }

  class Product : IProduct {
    private string productName;

    public string ProductName { // 파생클래스는 기반 추상 클래스의 모든 추상 메소드, 프로퍼티를 재정의 해야 한다.
      get { return productName; }
      set { productName = value; }
    }
  }
  ```

<br><br>

## 9.9 추상 클래스의 프로퍼티

- 추상 클래스는 클래스처럼 구현된 프로퍼티를 가질 수 있는 한편, 인터페이스처럼 구현되지 않은 프로퍼티도 가질 수 있다.
- 추상 클래스에서는 이것을 추상 프로퍼티(Abstract Property)라고 한다.
- 추상 프로퍼티 역시 인터페이스의 프로퍼티와 다를 것이 없다. 파생 클래스가 해당 프로퍼티를 구현하도록 강제하는 것일 뿐이다.
- 추상프로퍼티는 abstract 한정자를 이용해 선언한다.
  ```cs
  abstract class Product {
    private static int serial = 0;
    public string SerialID {
      get { return String.Format("{0:d5}", serial++); }
    }

    abstract public DateTime ProductDate {
      get; set;
    }
  }

  class MyProduct : Product {
    public override DateTime ProductDate { get; set; }
  }
  ```
