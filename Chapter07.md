# 07. 클래스

## 7.1 객체지향 프로그래밍과 클래스

- 객체지향 프로그래밍(Object Oriented Programming)은 OOP라고도 하는데, 코드 내의 모든 것을 객체(Object)로 표현하고자 하는 프로그래밍 패러다임을 뜻한다.
- 객체는 세상의 모든 것을 지칭하는 단어이다.
- 객체는 속성(데이터)와 기능(메소드)로 표현한다.
- 클래스는 객체를 만들기 위한 청사진이다.
- `int a = 30;`
  - int : 클래스, 청사진
  - a : 객체, int의 실체(instance)

<br><br>

## 7.2 클래스의 선언과 객체의 생성

- 클래스 안에 선언된 변수들을 필드(Field)라고 한다.
- 필드와 메소드를 비롯하여 프로퍼티, 이벤트등 클래스 내에 선언된 요소들을 맴버(Member)라고 한다.
- 클래스는 다음과 같이 class 키워드를 이용해 선언한다.
  ```cs
  class 클래스_이름{
    // 데이터와 메소드
  }
  //-------------------------------///

  class Cat{
    public string Name;
    public string Color;

    public void Meow(){
        Console.WriteLine("{0}: 야옹", Name);
    }
  }
  //-------------------------------///

  Cat Kitty = new Cat();
  Cat Nero = new Cat();
  Kitty.Color = "하얀색";
  Kitty.Name = "키티";
  Kitty.Meow();
  Nero.Color = "검은색";
  Nero.Name = "네로";
  Nero.Meow();
  ```
- Cat()은 생성자(Constructor)라고 하는 특별한 메소드이다.
- 생성자는 클래스의 이름과 동일한 이름을 가지며, 객체를 생성하는 역할을 한다.
- `Cat Kitty;`에서 Kitty는 null을 가리킨다. 복합 데이터 형식은 참조 형식이기 때문이다.
- Kitty 자체에 메모리가 할당되는 것이 아니고, 참조로써 객체가 있는 곳을 가리킬 뿐이기 때문이다.

```cs
using System;

namespace BasicClass
{
    class Cat
    {
        public string Name;
        public string Color;

        public void Meow()
        {
            Console.WriteLine($"{Name} : 야옹");
        }
    }

    class MainApp
    {
        static void Main(string[] args)
        {
            Cat kitty = new Cat();
            kitty.Color = "하얀색";
            kitty.Name = "키티";
            kitty.Meow();
            Console.WriteLine($"{kitty.Name} : {kitty.Color}");

            Cat nero = new Cat();
            nero.Color = "검은색";
            nero.Name = "네로";
            nero.Meow();
            Console.WriteLine($"{nero.Name} : {nero.Color}");
        }
    }
}
```

>>>
### 실행 결과
키티 : 야옹  
키티 : 하얀색  
네로 : 야옹  
네로 : 검은색  
>>>

<br><br>

### new

- new 연산자와 생성자는 모든 데이터 형식에 사용할 수 있다.
- `int a = new int();`, `string b = new string( new char []{'한', '글'});`

<br><br>

## 7.3 객체의 삶과 죽음에 대하여 : 생성자와 종료자

- 객체가 생성될 때 생성자(Constructor)가 호출되고 소멸할 때는 종료자(Finalizer)가 호출된다.

<br>

### 7.3.1 생성자

- 생성자는 클래스와 이름이 같고 반환 형식이 없다.
- 해당 형식(클래스)의 객체를 생성하는 것이 목적이다.
- 클래스를 생성할 때 명시적으로 생성자를 구현하지 않아도 컴파일러에서 생성자를 만들어준다.

```cs
class Cat{
  public Cat(){ //생성자
    Name = "";
    Color = ""
  }

  public Cat(string _Name, string _Color){
    Name = _Name;
    Color = _Color;
  }

  public string Name;
  public string Color;
}
//-------------------------------//
Cat kitty = new Cat();
kitty.Color = "하얀색"
kitty.Name = "키티";
Cat nabi = new Cat("나비", "갈색");
```

<br>

### 7.3.2 종료자

- 종료자의 이름은 클래스 이름 앞에 ~를 붙인 꼴이다.
- 종료자는 생성자와 달리 매개변수도 없고, 한정자도 사용하지 않는다.
- CLR의 가비지 컬렉터가 객체가 소멸되는 시점을 판단해서 종료자를 호출한다.


```cs
class Cat{
  //...//

  ~Cat(){
    Console.WriteLine($"{Name} : 잘가");
  }

  //...//
}
//-------------------------------//
{
  Cat nabi = new Cat("나비", "갈색");
} // 나비 : 잘가

```

<br><br>


## 7.4 정적(static) 필드와 메소드

- static은 사전적으로는 "정적"으로 움직이지 않는 다는 뜻이다.
- C#에서 static은 메소드나 필드가 클래스의 인스턴스가 아닌 클래스 자체에 소속되도록 하는 한정자이다.
- 똑같은 클래스가 2개 이상 존재할 수 없다.
- 어떤 필드가 클래스에 소속된다는 것은 곧 그 필드가 프로그램 전체에서 유일하게 존재하는 것을 의미한다.
- static으로 한정하지 않은 필드는 자동으로 인스턴스에 소속되며, static으로 한정한 필드는 클래스에 소속된다.

```cs
//----- 인스턴스에 소속된 필드
class MyClass{
  public int a;
}
//...//
public static void Main(){
  MyClass obj1 = new MyClass();
  obj.a = 1;
}

//----- 정적 필드
class MyClass{
  public static int a; // 프로그램 전체에 걸쳐 하나밖에 존재하지 않는다.
}
//...//
public static void Main(){
  MyClass.a = 1; //인스턴스를 만들지 않고 직접 접근한다.
}
```


```cs
using System;
namespace StaticField
{
    class Global {
        public static int Count = 0;
    }
    class ClassA {
        public ClassA() {
            Global.Count++;
        }
    }
    class MainApp {
        static void Main() {
            Console.WriteLine($"Global.Count : {Global.Count}");

            new ClassA();
            new ClassA();

            Console.WriteLine($"Global.Count : {Global.Count}");
        }
    }
}
```

>>>
### 실행 결과
Global.Count : 0  
Global.Count : 2
>>>

<br>

### 정적(static) 메소드 

- 정적 메소드가 클래스의 인스턴스를 생성하지 않아도 호출이 가능한 메소드이다.
- 
```cs
//----- 인스턴스 메소드
class MyClass{
  public void InstanceMethod(){ /*...*/ }
}
//...//
MyClass obj = new MyClass();
obj.InstanceMethod();

//----- 정적 메소드
class MyClass{
  public static void StaticMethod(){ /*...*/ } // 프로그램 전체에 걸쳐 하나밖에 존재하지 않는다.
}
//...//
MyClass.StaticMethod();
```

<br><br>

## 7.4 객체 복사하기 : 얕은 복사와 깊은 복사

- 클래스는 태생이 참조형식이다.
- 객체를 복제할 때 참조만 실짝 복사하는 것을 얕은 복사라 한다.
  ```cs
  MyClass source = new MyClass();
  MyClass target = source; // 얕은 복사
  target.MyField1 = 10; // 참조이기 때문에 source도 함께 반영된다.
  ```
- C#에서는 깊은 복사에 대해서 스스로 코드를 만들어야 한다.
  ```cs
  class MyClass{
    public int MyField1;
    public int MyField2;
    public MyClass DeepCopy(){ // 객체를 힙에 새로 할당하여 그곳에 자신의 맴버를 일일이 복사해 넣는다.
      MyClass newCopy = new MyClass();
      newCopy.MyField1 = this.MyField1;
      newCopy.MyField2 = this.MyField2;
      return newCopy;
    }
  }
  //-------------------------------//
  MyClass source = new MyClass();
  MyClass target = source.DeepCopy(); // 깊은 복사
  target.MyField1 = 10; // 개별 반영된다.
  ```


<br><br>

## 7.5 this 키워드

### 7.6.1 나

- 우리 자신을 지칭할 때 "나"라고 한다.
- this라는 객체가 자신을 지칭할 때 사용하는 키워드이다.
- 외부에서는 객체의 필드나 메소드에 접근시 객체의 이름을 사용하지만, 객체 내부에서는 this로 자신의 필드나 메소드에 접근한다.

```cs
class Employee{
  private string Name;

  public void SetName( string Name ){
    this.Name = Name; // 첫 번째 Name은 처음에 선언한 Name이고, 두번째 Name은 SetName의 매게변수 Name이다.
  }
}
```

<br>

### 7.6.2 this() 생성자

- this()는 자기 자신의 생성자를 가리킨다.
- this()는 생성자에서만 이용될 수 있다.
- this()는 코드 앞쪽에서만 사용 가능하다.

```cs
class MyClass{
  int a, b, c;

  public MyClass(){
    this.a = 5425;
  }
  public MyClass(int b){
    this.a = 5425;
    this.b = b;
  }
  public MyClass(int b, int c){
    this.a = 5425;
    this.b = b;
    this.c = c;
  }
}
//-----------다음과 같이 축약 가능---------------//
class MyClass{
  int a, b, c;

  public MyClass(){
    this.a = 5425;
  }
  public MyClass(int b) : this() { // this()는 MyClass()를 호출
    this.b = b;
  }
  public MyClass(int b, int c) : this( b ) { // this(int)는 MyClass(int)를 호출
    this.c = c;
  }
}
```


<br><br>

## 7.7 접근 한정자로 공개 수준 결정하기

- 객체지향 프로그래밍에도 필요한 최소의 기능만 노출하고 내부를 감출 것을 요구한다.
- 이러한 특성을 은닉성(Encapsulation)이라고 한다.
- 객체지향 프로그래밍의 3대 특성
  - 은닉성(Encapsulation)
  - 상속성(Inheritance)
  - 다형성(Polymorphism)
- 수식하지 않은 클래스 맴버는 무조건 private으로 접근 수준이 자동 설정된다.

| 접근 한정자             | 설명                                                                                   |
|--------------------|--------------------------------------------------------------------------------------|
| public             | 클래스 내/외부 모든 곳에서 접근 가능                                                                |
| protected          | 클래스 외부에서 접근 불가, 파생클래스에서 접근 가능                                                        |
| private            | 클래스 내부에서만 접근 가능                                                                      |
| internal           | 같은 어셈블리에 있는 코드에서만 public으로 접근 가능. <br> 다른 어셈블리에 있는 코드에서는 private과 같은 수준의 접근성을 가진다.   |
| protected internal | 같은 어셈블리에 있는 코드에서만 protected로 접근 가능. <br> 다른 어셈블리에 있는 코드에서는 private과 같은 수준의 접근성을 가진다. |
| private internal   | 같은 어셈블리에 있는 클래스에서 상속받은 클래스 내부에서만 접근 가능                                               |

```cs
class MyClass{
  private int MyFiled1;
          int MyFiled2; //private
  protected int MyFiled3;
  public int MyMethod_1(){/*...*/}
  internal int MyMethod_2(){/*...*/}
}
```

<br><br>

## 7.8 상속으로 코드 재활용 하기

- 클래스는 다른 클래스로부터 유산을 물려 받을 수 있다.
- 객체지향 프로그래밍에서는 물려받는 클래스(파생 클래스(Derived Calss) 또는 자식 클래스)가 유산을 물려줄 클래스(기반 클래스(Base Class) 또는 부모 클래스)를 지정한다.
- 파생 클래스의 이름 뒤에 콜론(:)을 붙여주고 그 뒤에 상속받을 기반 클래스의 이름을 붙여주면 된다.
- 파생 클래스는 객체를 생성할 때 내부적으로 기반 클래스의 생성자를 호출한 후에 자신의 생성자를 호출하고, 객체가 소멸될 때는 반대의 순서로 (파생>기반)종료자를 호출한다.
- 예) `class Derived : Base {}` 일때 : Base() > Derived() > ~Derived() > ~Base()
- 상속을 원하지 않으면 class 앞에 sealed 라는 한정자를 이용한다. (상속 봉인)
  ```cs
  class Base{
    public Void BaseMethod(){
      Console.WriteLine("BaseMethod");
    }
  }

  class Derived : Base // Derived 클래스는 Base 클래스를 상속 했으므로 BaseMethod()를 갖는다.
  {
    // 아무 맴버를 선언하지 않아도 기반 클래스의 모든 것을 물려받아 갖게 된다.
    // 단, private으로 선언된 맴버는 예외이다.
  }
  ```

<br>

### base, base()

- 파생 클래스의 인스턴스를 생성할 때 호출되는 기반 클래스의 생성자에는 base 키워드를 통해 전달 된다.
- this, this()와 같은 맥락으로 활용된다.
  ```cs
  class Base{
    public Base(string Name){
      this.Name = Name;
    }

    public Void BaseMethod(){
      Console.WriteLine("BaseMethod");
    }
  }

  class Derived : Base {
    public void DerivedMethod() : base(Name){ //Base(string Name) 호출
      base.BaseMethod(); //Base에 속한 메소드 접근
    }
  }
  ```

```cs
using System;
namespace Inheritance {
    class Base {
        protected string Name;

        public Base(string name) {
            this.Name = name;
            Console.WriteLine($"{this.Name}.Base()");
        }

        ~Base()
            Console.WriteLine($"{this.Name}.~Base()");

        public void BaseMethod()
            Console.WriteLine($"{Name}.BaseMethod()");
    }

    class Derived : Base {
        public Derived(string Name) : base(Name)
            Console.WriteLine($"{this.Name}.Derived()");

        ~Derived()
            Console.WriteLine($"{this.Name}.~Derived()");

        public void DerivedMethod()
            Console.WriteLine($"{Name}.DerivedMethod()");
    }

    class MainApp {
        static void Main(string[] args) {
            Base b = new Base("b");  //Base(Name) 생성
            b.BaseMethod(); // Base 내의 BaseMethod();

            Derived d = new Derived("d");  //Derived(Name) : Base(Name) 생성
            d.BaseMethod(); // Derived : Base 내의 BaseMethod();
            d.DerivedMethod(); // Derived 내의 DerivedMethod();
        }
    }
}
```

>>>
### 실행 결과
b.Base()  
b.BaseMethod()  
d.Base()  
d.Derived()  
d.BaseMethod()  
d.DerivedMethod()  
d.~Derived()  
d.~Base()
a.~Base()
>>>


<br><br>

## 7.9 기반 클래스와 파생 클래스 사이의 형식 반환, is as

- 부모 클래스는 자식 클래스로 형식변환 할 수 있다.
  ```cs
  class Mammal{ /*...*/}
  class Dog : Mammal {/*...*/}
  class Cat : Mammal {/*...*/}
  class Lion : Mammal {/*...*/}
  //...//
  Mammal mammal = new Mammal();
  mammal = new Dog(); //부모 > 자식 형식변환 가능
  mammal = new Cat();
  mammal.Meow();
  ```
- 다음과 같이 기반 클래스와 그 속성을 갖추는 다양한 클래스레 파생되도록 할 수 있다.
  ```cs
  class Dog{ /*...*/}
  class Cat{ /*...*/}
  class Lion{ /*...*/}

  class Zookeeper{
    public void Wash( Dog dog ) { /*...*/}
    public void Wash( Cat cat ) { /*...*/}
    public void Wash( Lion Lion ) { /*...*/}
    //...//
  }
  //-------------------------------//
  class Mammal{ /*...*/}
  class Dog : Mammal {/*...*/}
  class Cat : Mammal {/*...*/}
  class Lion : Mammal {/*...*/}

  class Zookeeper{
    public void Wash( Mammal mammal ){ /*...*/}
  }
  ```

<br>

### 형식변환

- C#에서는 형식 변환을 위한 연산자 2개를 제공한다.
- 코딩을 하다보면 어떤 클래스가 상속을 받았는지 헷갈릴 때가 있다. 이런 경우에 is, as를 통해 검사를 진행하여 형식변환을 쉽게 할 수 있도록 해준다.
- 코딩이 보다 안전하게 형식 변환하기 위해 사용된다.
- is보다는 as 연산자를 사용하는 쪽을 권장한다.(빌 와그너 Bill Wagner가 지은 "Effective C#" 참조)
- 이는 형식 변환이 실패하더라도 예외가 일어나 갑자기 코드의 실행이 점프하는 일이 없어 코드 관리가 더 수원하기 때문이다.

| 연산자 | 설명                                                                                               |
|-----|--------------------------------------------------------------------------------------------------|
| is  | 객체가 해당 형식에 해당하는지 검사하여 그 결과를 bool 값으로 반환한다.                                                       |
| as  | 형식 변환 연산자와 같은 역할을 한다.<br>다만 형식 변환 연산자가 변환에 실패하는 경우 예외를 던지는 반면에 as 연산자는 객체 참조를 null로 만든다는 것이 다르다. |

```cs
Mammal mammal = new Dog();
Dog dog;
if( mammal is Dog){ // mammal 객체가 Dog 형식임이 확인했으므로, 안전하게 형식변환한다.
  dog = (Dog)mammal;
  dog.Bark();
}

mammal = new Cat();
Cat cat = mammal as Cat; // mammal이 Cat 형식 변환에 실패했다면 cat == null이 된다.
if(cat != null){ // mammal은 Cat 형식에 해당하므로 안전하게 형식 변환이 이루어진다.
  cat.Meow();
}
```

<br><br>

## 7.10 오버라이딩과 다형성

- 객체지향 프로그래밍에서 다형성(Polymorphism)은 객체가 여러 형태를 가질 수 있음을 의미한다.
- 하위 형식 다형성(Subtype Polymorphism)의 준말이다.
- 자신으로부터 상속받아 만들어진 파생 클래스를 통해 다형성을 실현한다.
- 이를 활용하면 이미 만들어진 부모 클래스를 다른 자식 클래스에서 재정의(업그레이드)할 수 있다.
- 이를 위해서 부모에서 언제든지 재정의 할 수 있도록 메소드에 virtual 키워드를 사용하고, 자식에서 업그레이드 할 메소드에 override 키워드를 사용한다.
- 단 private로 선언한 메소드는 오버라이딩이 불가능하다.
  ```cs
  class ArmorSuite{
    public virtual void Initialize(){
      Console.WriteLine("Armored");
    }
  }
  //...//
  class IronMan : ArmorSuite{
    public override void Initialize(){
      base.Initialize();
      Console.WriteLine("Repulsor rays Armed");
    }
  }
  class WarMachine : ArmorSuite{
    public override void Initialize(){
      base.Initialize();
      Console.WriteLine("Double-Barrel Cannon Armed");
      Console.WriteLine("Micro-Rocket Launcher Armed");
    }
  }

  //...//
  ArmorSuite arm0 = new ArmorSuite();
  arm0.Initialize();  // Armored
  IronMan arm1 = new IronMan();
  arm1.Initialize(); // Armored, Repulsor rays Armed
  WarMachine arm2 = new WarMachine();
  arm2.Initialize(); // Armored, Double-Barrel Cannon Armed, Micro-Rocket Launcher Armed
  ```
  

<br><br>

## 7.11 메소드 숨기기

- 메소드 숨기기란, CLR에게 기반 클래스에서 구현된 버전의 메소드를 감추고 파생 클래스에서 구현된 버전만 보여주는 것을 말한다.
  ```cs
  class Base {
    public void MyMethod() {
      Console.WriteLine("Base.MyMethod()");
    }
  }

  class Derived : Base {
    public new void MyMethod() {
      Console.WriteLine("Derived.MyMethod()");
    }
  }

  //...//
  Base based = new Base();
  based.MyMethod(); // "Base.MyMethod()" 출력

  Derived derived = new Derived();
  derived.MyMethod(); // "Base.MyMethod()"가 아닌, "Derived.MyMethod()"출력

  Base based = new Derived();
  based.MyMethod(); // "Base.MyMethod()" 출력
  ```

<br><br>

## 7.12 오버라이딩 봉인하기

- 클래스를 상속이 안되도록 봉인하는 것 처럼, 메소드도 오버라이딩 되지 않도록 sealed 키워드를 이용해서 봉인할 수 있다.
- 이때, [virtual로 선언된 가상 메소드]를 오버라이딩한 버전의 메소드,만 가능하다.
  ```cs
    class Base {
    public virtual void SealMe() {/*...*/}
  }

  class Derived : Base {
    public sealed override void SealMe() {/*...*/} // 이 메소드만 봉인할 수 있다.
  }
  ```

<br><br>

## 7.13 읽기 전용 필드

- 상수는 변치 않는 데이터를 담는다.
  ```cs
  const double pi = 3.14159265359;
  ```
- 읽기 전용 필드는 읽기만 가능한 필드를 말한다.
- 클래스나 구조체의 맴버로써 존재할 수 있으며 생성자 안에서 한 번 값을 지정하면, 그 후로는 값을 변경할 수 없는 것이 특징이다.
- 읽기 전용 필드는 readonly 키워드를 통해서 선언할 수 있다.
  ```cs
  class Configuration{
    private readonly int min;
    private readonly int max;

    public Configuration(int v1, int v2){
      min = v1;
      max = v2;
    }
  }
  ```
- 읽기 전용 필드는 생성자 안에서만 초기화가 가능하다.

<br><br>

## 7.14 중첩 클래스

- 중첩 클래스(Nested Class)는 클래스 안에 선언되어 있는 클래스를 말한다.
- 자신이 소속된 클래스의 맴버에 자유롭게 접근할 수 있다.
  ```cs
  class OuterClass {
    private int OutNumber;

    class Nested Class{
      public void DoSomething(){
        OuterClass outer = new OuterClass();
        outer.OutNumber = 10; // OuterClass의 private 맴버에 접근하여 값을 할당하거나 읽을 수 있다.
      }
    }
  }
  ```

<br><br>

## 7.15 분할 클래스

- 분할 클래스(Partial Class)란, 여러 번 나눠서 구현하는 클래스를 말한다.
- 클래스의 구현이 길어질 경우 여러 파일에 나누어서 구현할 수 있게 함으로써 소스 코드 관리의 편의를 제공하는 데 그 의미가 있다.
  ```cs
  partial class MyClass {
    public void Method1() {/*...*/}
  }
  partial class MyClass {
    public void Method2() {/*...*/}
  }
  //...//
  MyClass obj = new MyClass();
  obj.Method1();
  obj.Method2();
  ```


<br><br>

## 7.16 확장 메소드

- 확장 메소드(Extension Method)는 기존 클래스의 기능을 확장하는 기법이다.
- 메소드를 선언하되, static 한정자로 수식해야 한다.
- 매개변수 목록이 실제로 확장 메소드를 호출할 때 입력되는 매게변수이다.
- 클래스도 역시 static 한정자로 수식해야 한다.
  ```cs
  namespace MyExtension {
    public static class IntegerExtension{

      public static int Power( this int myInt, int exponent){
        int result = myInt;
        for(int i = 1; i < exponent; i++)
          result = result * myInt;
        return result;
      }

    }
  }
  //...//

  using MyExtension;
  
  int a = 2;
  Console.WriteLine( a.Power( 3 ) );
  Console.WriteLine( 10.Power( 3 ) );
  ```

<br><br>

## 7.17 구조체

- C#의 복합 데이터 형식엔 클래스 말고도 구조체(Structure)라는 것이 있다.
  ```cs
  struct MyStruct {
    public int MyField1;
    public int MyField2;
    public void MyMethod() {/*...*/}
  }
  ```
  | 특징      | 클래스                 | 구조체                |
  |---------|---------------------|--------------------|
  | 키워드     | class               | struct             |
  | 형식      | 참조 형식(힙에 할당)        | 값 형식(스택에 할당)       |
  | 복사      | 얕은 복사(Shallow Copy) | 깊은 복사(Deep Copy)   |
  | 인스턴스 생성 | new 연산자와 생성자 필요     | 선언만으로도 생성          |
  | 생성자     | 매개변수 없는 생성자 선언 가능   | 매개변수 없는 생성자 선언 불가능 |
  | 상속      | 가능                  | 값 형식이므로 상속 불가능     |
- readonly 키워드를 통해 변경 불가능 구조체로 선언할 수 있다.
- 이때 해당 struct 안에 존재하는 모든 필드가 readonly로 선언되어야 한다.
  ```cs
  readonlt struct ImmutableStruct {
    public readonly int ImmutableField; //OK
    public int MutableField; // 컴파일 에러
  }
  ```
  - 구조체와 같은 값 형식은 생성된 지역을 벗어나면 스택에서 소멸되기 때문에, 프로그램에 장기적인 부담을 주지 않는다는 장점이 있다.

<br><br>

## 7.18 튜플

- 튜플(Tuple)도 여러 필드에 담을 수 있는 구조체이다.
- 튜플은 형식 이름이 없다. 튜플은 구조체이므로 값 형식이다.
  - 구조체와 같은 값 형식은 생성된 지역을 벗어나면 스택에서 소멸되기 때문에, 프로그램에 장기적인 부담을 주지 않는다는 장점이 있다.
  ```cs
  var tuple = (123, 789); // 튜플은 괄호 사이에 두 개 이상의 필드를 지정함으로써 만들어진다.
  var tuple1 = (Name: "박주형", Age: 25);
  Console.WriteLine($"{tuple1.Name}, {tuple1.Age}");
  // 튜플 분해
  var (name, age) = tuple1;
  Console.WriteLine($"{name}, {age}");
  // 특정 필드 무시
  var (name, _) = tuple1;
  // 튜플을 통한 여러변수 생성 및 초기화
  var (name2, age2) = ("박주형", 17);

  //...//
  var unnamed = ("슈퍼맨", 9999);
  var named = (Name: "박주형", Age: 17);
  
  named = unnamed;
  Console.WriteLine($"{named.Name}, {named.Age}"); // 출력 결과 : 슈퍼맨, 9999

  named = ("원더우먼", 10000);
  unnamed = named;
  Console.WriteLine($"{unnamed.Name}, {unnamed.Age}"); // 출력 결과 : 원더우먼, 10000
  ```
- 튜플이 분해 가능한 이유는 분해자(Deconstructor)를 구현하고 있다.
- 분해자를 구현하고 있는 객체를 분해한 결과를 switch 문이나 switch 식의 분기 조건에 활용할 수 있다.
- 이를 위치 패턴 매칭(Positional Pattern Matching)이라고 한다.
  ```cs
  var alice = (job: "학생", age: 17);
  var discount = alice switch{
    ("학생", int n) when n < 18 => 0.2, // 학생 & 18세 미만
    ("학생", _) => 0.1, // 학생 & 18세 이상
    ("일반", int n) when n < 18 => 0.1, // 일반 & 18세 미만
    ("일반", _) => 0.1, // 일반 & 18세 이상
  }
  ```
