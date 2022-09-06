# 16. 리플렉션과 애트리뷰트

## 16.1 리플렉션

- Object 형식에 GetType() 메소드를 만들어 모든 형식을 들여다 볼 수 있다.
- 객체의 형식(Type) 정보를 들여다보는 기능이다.

### 16.1.1 Object.GetType() 메소드와 Type 클래스

- Object는 모든 데이터 형식의 조상이다. 모든 데이터 형식은 Object 형식이 갖고 있는 메소드를 물려받아 갖고 있다.
- GetType() 메소드는 Type 형식의 결과를 반환한다.
- Type 형식은 .NET에서 사용되는 데이터 형식의 모든 정보를 담고 있다.
  - 형식 이름, 소속된 어셈블리 이름, 프로퍼티 목록, 메소드 목록, 필드목록, 이벤트 목록, 이 형식이 상속하는 인터페이스 목록 등등을 갖고 있다.
- Object.GetType()메소드와 Type 형식 사용 방법은 다음과 같다.
  ```cs
  int a = 0;

  Type type = a.GetType();  // Type
  FieldInfo[] fields = type.GetFields();  // 필드 목록 조회

  foreach (FieldInfo field in fields)
    Console.WriteLine( "Type : {0}, Name : {1}", field.FieldType.Name, field.Name );
  ```
- System.Type에는 수많은 메소드가 존재한다.
- [MSDN을 통해 제공하는 메소드를 확인해보자.](https://docs.microsoft.com/ko-kr/dotnet/api/system.type?view=net-6.0)
  - GetConstructor() 메소드 : ConstructorInfo[] 형식의 모든 생성자 목록을 반환한다.
  - GetFields() 메소드 : FieldInfo[] 형식의 모든 필드 목록을 반환한다.
  - GetProperty() 메소드 : PropertyInfo[] 형식의 프로퍼티 목록을 반환한다.
- 메소드 검색옵션은 System.Reflection.BindingFlas 열거 형을 이용해서 구성한다.
  ```cs
  Type type = a.GetType();

  //public 인스턴스 필드 조회
  var field1 = type.GetFields( BindingFlas.Public | BindingFlas.Instane )

  // 비 public 인스턴스 필드 조화
  var field2 = type.GetFields( BindingFlas.NonPublic | BindingFlas.Instane );

  // public 정적 필드 조회
  var field3 = type.GetFields( BindingFlas.Public | BindingFlas.Staic )

  // 비 public 정적 필드 조회
  var field4 = type.GetFields( BindingFlas.NonPublic | BindingFlas.Staic )
  ```
- Object.GetType() 메소드 외에도 형식 정보를 얻을 수 있는 typeof 연산자와 Type.GetType() 메소드를 제공한다.
  ```cs
  //Object.GetType()과 달리 인스턴스 생성이 필요 없다.

  // typeof 연산자
  Type a = typeof(int);  // 인수 : 형식의 식별자 자체
  Console.WriteLine(a.FullName);
  // Type.GetType()
  Type b = Type.GetType("System.Int32");  // 인수 : 형식 네임스페이스를 포함하는 전체 이름
  Console.WriteLine(b.FullName);  
  ```

<br>

### 16.1.2 리플렉션을 이용해서 객체 생성하고 이용하기

- 리플렉션을 이용해 특정 형식의 인스턴스를 만들고 데이터를 할당하며 메소드를 호출하는 방법을 안다.
- 리플렉션을 이용해 동적으로 인스턴스를 만들기 위해서는 System.Activator 클래스의 도움이 필요하다.
  ```cs
  // 인스턴스 생성
  object a = Activator.CreateInstance( typeof(int) );
  List<int> list = Activator.CreateInstance< List<int> >(); // List<int> 인스턴스 제작 예시
  
  // 값 할당 및 읽기
  class Profile {
    public string Name{get;set;}
    public string Phone{get;set;}
    public void Print() { Console.WriteLine("{0}, {1}", Name, Phone); }
  }
  
  Type type = typeof(Profile);
  Object profile = Activator.CreateInstance( type );

  PropertyInfo name = type.GetProperty( "Name" );
  PropertyInfo phone = type.GetProperty( "Phone" );

  name.SetValue( profile, "박찬호", null ); // 값 할당
  phone.SetValue( profile, "997-5511", null );

  Console.WriteLine( "{0}, {1}",
    name.GetValue( profile, null ), // 값 읽기
    phone.GetValue( profile, null )
  );

  //메소드 호출
  MethodInfo method = type.GetMethod("Print");
  method.Invoke( profile, null ); // Profile.Print에 인수가 없기 때문에 null.
  ```

<br>

### 16.1.3 형식 내보내기

- 동적으로 새로운 형식을 만드는 작업은 System.Reflection.Emit 네임 스페이스에 있는 클래스들을 통해 이루어진다.
- 이밋(Emit)은 영어로 레이저를 "내뿜다" 도는 지폐를 "발행하다"는 뜻을 갖고 있다.
- 리플렉션에서 Emit은 프로그램이 실행 중에 만들어낸 새 형식을 CLR의 메모리에 "내보낸다"라는 의미이다.
- Emit 네임 스페이스에서 제공하는 다양한 클래스들이 있다.
  - AssemblyBuilder 클래스 : 동적 어셈블리를 정의하고 나타낸다.
  - ILGenerator 클래스 : MSIL 명령어를 생성한다.
  - ParameterBuilder 클래스 : 매개변수 정보를 생성하거나 결합시킨다.
  - ...
- Emit 클래스 사용하는 요령은 다음 순서와 같다.
  1. AssemblyBuilder를 이용해서 어셈블리를 만든다.
  2. ModuleBuilder를 이용해서 1에서 생성한 어셈블리 안에 모듈을 만들어 넣는다.
  3. 2에서 생성한 모듈 안에 TypeBuilder로 클래스(형식)를 만들어 넣는다.
  4. 3에서 생성한 클래스 안에 메소드(MethodBuilder 이용)나 프로퍼티(PropertyBuilder 이용)를 만들어 넣는다.
  5. 4에서 생성한 것이 메소드라면, ILGenerator를 이용해서 메소드 안에 CPU가 실행할 IL명령들을 넣는다.
- 예제
  ```cs
  // 1. AssemblyBuilder 클래스를 스스로 생성하기 위해 DefineDynamicAssembly() 메소드를 호출한다.
  AssemblyBuilder newAssembly = 
    AssemblyBuilder.DefineDynamicAssembly(
      new AssemblyName( "CalculatorAssembly" ),
      AssemblyBuilderAccess.Run
    );
  
  // 2. DefineDynamicModule() 메소드를 갖고 있으므로, 이 메소드를 호출하여 모듈을 만든다.
  ModuleBuilder newModule = newAssembly.DefineDynamicModule( "Calculator" );

  // 3. 클래스를 만든다. ModuleBuilder의 DefineType() 메소드를 이용해서 클래스를 생성한다.
  TypeBuilder newType = newModule.DefineType( "Sun1To100" );

  // 4. 메소드를 만든다. TypeBuilder 클래스의 DefineMethod() 메소드를 호출해서 "Calculate()"라는 이름의 메소드를 만든다.
  MethodBuilder newMethod = newType.DefineMethod(
    "Calculate",
    MethodAttributes.Public,
    typeof( int ), // 반환 형식
    new Type[0] //매개변수
  );

  // 5. 메소드의 껍데기를 만들었으니, 실행할 코드(IL 명령어)채워 넣는다.
  // ILGenerator 객체를 통해서 이루어진다. MethodBuilder 클래스의 GetILGenerator() 메소드를 통해 얻는다.

  ILGenerator generator = newMethod.GetILGenerator();

  generator.Emit( OpCodes.Ldc_I4, 1 ); // 32비트 정수(1)를 계산 스택에 넣는다.

  for( int i = 2; i <= 100; i++ ) {
    generator.Emit( OpCodes.Ldc_I4, i); // 32 비트 정수(i)를 계산 스택에 넣는다.
    generator.Emit( OpCodes.Add ); // 계산 후 계산 스택에 담겨있는 두 개의 값을 꺼내 더한 후, 결과를 다시 계산 스택에 넣는다.
  }

  generator.Emit( OpCodes.Ret ); // 계산 스택에 담겨있는 값을 반환한다.


  // 6. Calculate() 메소드 안에 IL 명령어를 모두 채웠으니, Sum1To100 클래스를 CLR에 제출한다.
  newType.CreateType();

  // 7. 이 형식의 인스턴스를 동적으로 생성해 이용할 수 있다.
  object sum1To100 = Activator.CreateInstance( newType );
  MethodInfo Calculate = sum1To100.GetType().GetMethod( "Calculate" );
  Console.WriteLine( Calculate.Invoke( sum1To100, null ) );

  ```
- 동적으로 새로운 형식을 만드는 과정을 통해, 컴파일러나 인터프리터를 만들 수 있다.

<br><br>

## 16.2 애트리뷰트

- 애트리뷰트(Attribute)는 코드에 대한 부가 정보를 기록하고 읽을 수 있는 기능이다.
- 메타 데이터(코드에 대한 정보)를 담는 코드 요소이다.
  - 메타 데이터(Metadata)란 데이터의 데이터이다.
  - 애트리뷰트나 리플랙션을 통해 얻는 정보들도 C# 코드의 메타데이터이다.
- 컴파일을 거치면 실행파일(어셈블리) 안에 저장되며, 컴퓨터가 런타임에 읽을 수 있다.

<br>

### 16.2.1 애트리뷰트 사용하기

- 애트리뷰트를 사용할 때는 설명하고자 하는 코드 요소 앞에 \[대괄호\] 쌍을 붙이고 그 안에 애트리뷰트의 이름을 넣으면 된다.
  ```cs
   [ 애트리뷰트_이름( 애트리뷰트_매개변수) ]
   public void MyMethod()
   {
      //...
   }

   class MyClass {
    [Obsolete( "OldMethod는 폐기 되었습니다. NewMethod()를 이용하세요.")]
    public void OldMethod() {
      Console.WriteLine( "I'm old" );
    }

    public void NewMethod() {
      Console.WriteLine( "I'm new" );
    }
   }
  ```
- OldMethod()를 사용하는 코드를 그대로 둔 채 컴파일하면, "OldMethod는 폐기되었습니다. NewMethod()를 이용하세요."라는 경고 메시지를 보낸다.  
  <img src="/uploads/a96b675044890766a3506d3029c6f0af/image.png" width="70%">

<br>

### 16.2.2 호출자 정보 애트리뷰트

- C# 5.0 버전부터 호출자 정보 (Caller Information) 애트리뷰트가 도입되었다.
- 호출자 정보는 메소드의 매개변수에 사용되며, 호출자 이름, 호출자 메소드가 정의된 소스 파일 경로, 소스파일 행(Line) 번호까지 알 수 있다. (C/C++의 __FILENAME__, __LINE__, __FUNCTION__ 와 같은 기능)
- 메소드의 매개 변수에 사용
  - 메소드의 호출자 이름 : CallerMemberNameAttribute
  - 호출자 메소드가 정의되어 있는 소스 파일 경로 : CallerFilePathAttribute
  - 소스 파일 내의 행 번호 파악 : CallerLineNumberAttribute
  ```cs
  public static class Trace {
    public static void WriteLine( string message,
      [CallerFilePath] string file = "",
      [CallerLineNumber] int line = 0,
      [CallerMemberName] string member = ""
      ) {
        Console.WriteLine( "{0}(Line : {1}) {2}: {3}", file, line, member, message );
      }
  }

  void SomeMethod() {
    Trace.WriteLine("즐거운 프로그래밍!");
  }
  ```
- SomeMethod()를 호출하면, `C:\ThisisCshap\Main.App.cs(Line:22) Main: 즐거운 프로그래밍!` 이라고 나온다.

<br>

### 16.2.3 내가 만드는 애트리뷰트

- .NET이 제공하는 애트리뷰트는 Obsolete 말고도 종류가 상당히 많다.
- C나 C++로 작성된 네이티브 DLL (Dynamic Link Library)에 있는 함수를 호출할 때 사용하는 [DLLImport], 조건부 메소드 실행을 지정할 때 사용하는 [Conditional]등이 그 예이다.
- 애트리뷰트도 하나의 클래스이기 때문에, Ststem.Attribute 클래스로부터 상속을 받아 만들어진다.
  ```cs
  class History : System.Attribute  // 상속
  {
     private string programmer;
     
     public double Version
     {
        get;
        set;
     }
     
     public string Changes
     {
        get;
        set;
     }
     
     // 생성자
     public History(string programmer)
     {
        this.programmer = programmer;
        Version = 1.0;
        Changes = "First release";
     }
     
     public string Programmer
     {
        get { return programmer; }
     }
  }
  // History 클래스 사용
  [History("Sean", Version=0.1, Changes="2017-11-01 Created class stub")]
  class MyClass
  {
    public void Func()
    {
       Console.WriteLine("Func()");
    }
  }
  ```
- 두 개 이상의 애트리뷰트를 도시에 사용하려면 System.AttributeUsage라는 애트리뷰트의 도움을 받는다.
  ```cs
  [System.AttributeUsage(
      System.AttributeTargets.Class | System.AttributeTargets.Method,  // Attribute Target
      AllowMultiple=True)]  // AllowMultiple
  class History : System.Attribute
  {
     //...
  }
  ```
