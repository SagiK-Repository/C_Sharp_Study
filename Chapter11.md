# 11. 일반화 프로그래밍

## 11.1 일반화 프로그래밍이란?

- 특수한 개념으로부터 공통된 개념을 찾아 묶는 것을 "일반화(Generalization)"이라고 한다.
- 일반화 프로그래밍(Generic Programming)은 이러한 일반화를 이용하는 프로그래밍 기법이다.
- 일반화하는 대상은 "데이터 형식"이다.
  ```cs
  //int
  void CopyArray( int[] source, int[] target ){
    for( int i = 0; i < source.Length; i++ )
      target[i] = source[i];
  }

  //string
  void CopyArray( string[] source, string[] target ){
    for( int i = 0; i < source.Length; i++ )
      target[i] = source[i];
  }
  
  //... 31가지 데이터 형식을 지원하는 CopyArray를 만들어야 할때, 일반화 프로그래밍을 활용한다.
  ```

<br><br>

## 11.2 일반화 메소드

- 일반화 메소드(Generic Method)는 이름처럼 테이터형식을 일반화한 메소드이다.
- T는 형식(Type)을 뜻한다.
- 이를 활용해 어떤 형식이든 지원할 수 있도록 한다.
  ```cs
  void CopyArray<T>( T[] source, T[] target){
    for( int i = 0; i < source.Length; i++ )
      target[i] = source[i];
  }

  //...//
  CopyArray<int>(source, target);
  ```

<br><br>

## 11.3 일반화 클래스

- 일반화 클래스는 데이터 형식을 일반화한 클래스이다.
  ```cs
  class Array_Int {
    private int[] array;
    public int GetElement( int index ) { return array[index]; }
  }
  class Array_Double {
    private double[] array;
    public double GetElement( int index ) { return array[index]; }
  }
  
  //... 31가지 데이터 형식을 지원하는 Array를 만들어야 할때, 일반화 프로그래밍을 활용한다.
  ```
- 다음과 같이 활용한다.
  ```cs
  class Array_Generic< T > {
    private T[] array;
    public T GetElement( int index ){ return array[index]; }
  }

  //...//
  Array_Generic<int> intArr = new Array_Generic<int>();
  Array_Generic<double> intArr = new Array_Generic<double>();
  ```

<br><br>

## 11.4 형식 매개변수 제약시키기

- 특정 조건을 갖춘 형식에만 대응하는 형식 매개변수가 필요하다.
  ```cs
  void CopyArray<T>( T[] source, T[] target ) where T : struct {
    for( int i = 0; i < source.Length; i++ )
      target[i] = source[i];
  }
  ```
-
  | 제약                  | 설명                             |
  |---------------------|-----------------------------------|
  | where T : struct    | T는 값 형식이어야 한다.                                                |
  | where T : class     | T는 참조 형식이어야 한다.                                              |
  | where T : new()     | T는 반드시 매개변수가 없는 생성자가 있어야 한다.                         |
  | where T : 기반_클래스_이름 | T는 명시한 기반 클래스의 파생 클래스여야 한다.                     |
  | where T : 인터페이스_이름  | T는 명시한 인터페이스를 반드시 구현해야 한다. 인터페이스_이름에는 여러 개의 인터페이스를 명시할 수도 있다. |
  | where T : U         | T는 또 다른 형식 매개변수 U로부터 상속받은 클래스여야 한다.               |  
  ```cs
  public static CreateInstance<T>() where T : new() {
    return new T();
  }

  class BaseArray<U> where U : Base {
    public U[] Array{ get; set; }
    
    public BaseArray(int size){
      Array = new U[size];
    }

    public void CopyArray<T>(T[] Source) where T : U {
      Source.CopyTo(Array, 0);
    }
  }
  ```
- 예제
  ```cs
  using System;
  namespace ConstraintsOnTypeParameters {
      class StructArray<T> where T : struct {
          public T[] Array { get; set; }
          public StructArray(int size) {
              Array = new T[size];
          }
      }
      class RefArray<T> where T : class {
          public T[] Array { get; set; }
          public RefArray(int size) {
              Array = new T[size];
          }
      }
      class Base { }
      class Derived : Base { }
      class BaseArray<U> where U : Base {
          public U[] Array { get; set; }
          public BaseArray(int size) {
              Array = new U[size];
          }
  
          public void CopyArray<T>(T[] Source) where T : U {
              Source.CopyTo(Array, 0);
          }
      }
  
      class MainApp {
          public static T CreateInstance<T>() where T : new() {
              return new T();
          }
  
          static void Main(string[] args) {
              StructArray<int> a = new StructArray<int>(3);
              a.Array[0] = 0;
              a.Array[1] = 1;
              a.Array[2] = 2;
  
              RefArray<StructArray<double>> b = new RefArray<StructArray<double>>(3);
              b.Array[0] = new StructArray<double>(5);
              b.Array[1] = new StructArray<double>(10);
              b.Array[2] = new StructArray<double>(1005);
  
              BaseArray<Base> c = new BaseArray<Base>(3);
              c.Array[0] = new Base();
              c.Array[1] = new Derived();
              c.Array[2] = CreateInstance<Base>();
  
              BaseArray<Derived> d = new BaseArray<Derived>(3);
              d.Array[0] = new Derived();
              d.Array[1] = CreateInstance<Derived>();
              d.Array[2] = CreateInstance<Derived>();
  
              BaseArray<Derived> e = new BaseArray<Derived>(3);
              e.CopyArray<Derived>(d.Array);
          }
      }

  }
  ```

<br><br>

## 11.5 일반화 컬렉션

- 어떤 형식이든 object 형식을 상속하므로, object 형식으로 형식 변환이 가능하다.
- 컬렉션 객체에 int, double, string 형식 데이터도, MyClass 객체도 담을 수 있었다.
- 다음 4가지의 클래스만 다룬다.
- List\<T>, Queue\<T>, Stack\<T>, Dictionary<TKey, TValue>
- 이들은 각각 ArrayList, Queue, Stack, Hashtable의 일반화 버전이다.

<br>

### 11.5.1 List\<T>

- List\<T> 클래스는 비일반화 클래스인 ArrayList와 같은 기능을 하며 사용법 역시 동일하다.
- 차이점은 List\<T> 클래스는 인스턴스를 만들 때 형식 매개변수를 필요로 한다는 것과 한 컬렉션에 "아무"형식의 객체나 마구 집어 넣을 수 있었던 ArrayList와 달리 List\<T>는 형식 매개변수에 입력한 형식 외에는 입력을 허용하지 않는다.
- 예제
  ```cs
  using System;
  using System.Collections.Generic;
  
  namespace UsingGenericList {
      class MainApp {
          static void Main(string[] args) {
              List<int> list = new List<int>();  // 인스턴트 생성 시 형식 매개변수 작성
              for (int i = 0; i < 5; i++)
                  list.Add(i);
  
              foreach (int element in list)
                  Console.Write($"{element} ");
              Console.WriteLine();
  
              list.RemoveAt(2);
  
              foreach (int element in list)
                  Console.Write($"{element} ");
              Console.WriteLine();
  
              list.Insert(2, 2);
  
              foreach (int element in list)
                  Console.Write($"{element} ");
              Console.WriteLine();
          }
      }
  }
  ```
>>>
### 실행결과
0 1 2 3 4  
0 1 3 4  
0 1 2 3 4  
>>>

<br>

### 11.5.2 Queue<T>

- Queue\<T> 클래스는 형식 매개변수를 요구한다는 점만 다를 뿐, 비일반화 클래스인 Queue와 같은 기능을 하며 사용법도 동일하다.
  ```cs
  using System;
  using System.Collections.Generic;
  
  namespace UsingGenericQueue {
      class MainApp {
          static void Main(string[] args) {
              Queue<int> queue = new Queue<int>(); // 인스턴트 생성 시 형식 매개변수 작성
  
              queue.Enqueue(1);
              queue.Enqueue(2);
              queue.Enqueue(3);
              queue.Enqueue(4);
              queue.Enqueue(5);
  
              while (queue.Count > 0)
                  Console.WriteLine(queue.Dequeue());
          }
      }
  }
  ```
>>>
### 실행결과
1  
2  
3  
4  
5  
>>>

<br>

### 11.5.3 Stack<T>

- Stack\<T>의 기능과 사용 방법은 Stack과 동일하다.
  ```cs
  using System;
  using System.Collections.Generic;
  
  namespace UsingGenericStack {
      class MainApp {
          static void Main(string[] args) {
              Stack<int> stack = new Stack<int>();
  
              stack.Push(1);
              stack.Push(2);
              stack.Push(3);
              stack.Push(4);
              stack.Push(5);
  
              while (stack.Count > 0)
                  Console.WriteLine(stack.Pop());
          }
      }
  }
  ```
>>>
### 실행결과
5  
4  
3  
2  
1  
>>>

<br>

### 11.5.4 Dictionary<TKey, TValue>
- Dictionary<TKey, TValue>는 Hashtable의 일반화 버전이다.
  ```cs
  using System;
  using System.Collections.Generic;
  
  namespace UsingDictionary
  {
      class MainApp
      {
          static void Main(string[] args)
          {
              Dictionary<string, string> dic = new Dictionary<string, string>();
  
              dic["하나"] = "one";
              dic["둘"] = "two";
              dic["셋"] = "three";
              dic["넷"] = "four";
              dic["다섯"] = "five";
  
              Console.WriteLine(dic["하나"]);
              Console.WriteLine(dic["둘"]);
              Console.WriteLine(dic["셋"]);
              Console.WriteLine(dic["넷"]);
              Console.WriteLine(dic["다섯"]);
          }
      }
  }
  ```

>>>
### 실행결과
one  
two  
three  
four  
five  
>>>


<br><br>

## 11.6 foreach를 사용할 수 있는 일반화 클래스

- foreach 사용가능한 일반화 클래스이다.
- 형식변환으로 인한 성능 저하가 없으면서도 foreach 수회가 가능한 클래스를 작성할 수 있다.
- IEnumerable\<T> 인터페이스의 메소드는 다음과 같다.
- | 메소드                             | 설명                                              |
  |---------------------------------|-------------------------------------------------|
  | IEnumerator GerEnumerator()     | IEnumerator 형식의 객체를 반환(IEnumerable로부터 상속받은 메소드) |
  | IEnumerator\<T> GerEnumerator() | IEnumerator\<T> 형식의 객체를 반환                       |
- IEnumerator\<T> 의 메소드와 프로퍼티는 다음과 같다.
- | 메소드 또는 프로퍼티     | 설명      |
  |------|-----------------------------|
  | boolean MoveNext()      | 다음 요소로 이동한다. 컬렉션의 끝을 지난 경우에는 false, 이동이 성공한 경우에는 true를 반환한다.                                                     |
  | void Reset()            | 컬렉션의 첫 번째 위치의 "앞"으로 이동한다. 첫 번째 위치가 0번일 때, Reset()을 호출하면 -1번으로 이동하는 것이다. 첫 번재 위치로의 이동은 MoveNext()를 호출한 다음에 이루어진다. |
  | Object Current { get; } | 컬렉션의 현재 요소를 반환한다. (Ienumberator로부터 상속받은 프로퍼티)  |
  | T Current{ get; }       | 컬렉션의 현재 요소를 반환한다.      |
- 예제
  ```cs
  using System;
  using System.Collections;
  using System.Collections.Generic;
  
  namespace EnumerableGeneric {
      class MyList<T> : IEnumerable<T>, IEnumerator<T> { //  # 상속
          private T[] array;
          int position = -1;
  
          public MyList() {
              array = new T[3];
          }
  
          public T this[int index]{
              get { return array[index]; }
  
              set {
                if (index >= array.Length) {
                    Array.Resize<T>(ref array, index + 1);
                    Console.WriteLine($"Array Resized : {array.Length}");
                }
                array[index] = value;
              }
          }
  
          public int Length {
              get { return array.Length; }
          }
  
          public IEnumerator<T> GetEnumerator() {
              return this;
          }
  
          IEnumerator IEnumerable.GetEnumerator() {
              return this;
          }
  
          public T Current {
              get { return array[position]; }
          }
  
          object IEnumerator.Current {
              get { return array[position]; }
          }
  
          public bool MoveNext() {
              if (position == array.Length - 1) {
                  Reset();
                  return false;
              }
  
              position++;
              return (position < array.Length);
          }
  
          public void Reset() {
              position = -1; ;
          }
  
          public void Dispose() { }
      }
  
      class MainApp {
          static void Main(string[] args) {
              MyList<string> str_list = new MyList<string>();
              str_list[0] = "abc";
              str_list[1] = "def";
              str_list[2] = "ghi";
              str_list[3] = "jkl";
              str_list[4] = "mno";
  
              foreach (string str in str_list)
                  Console.WriteLine(str);
  
              Console.WriteLine();
  
              MyList<int> int_list = new MyList<int>();
              int_list[0] = 0;
              int_list[1] = 1;
              int_list[2] = 2;
              int_list[3] = 3;
              int_list[4] = 4;
  
              foreach (int no in int_list)
                  Console.WriteLine(no);
          }
      }
  }
  ```

>>>
## 실행 결과
Array Resized : 4  
Array Resized : 5  
abc  
def  
ghi  
jkl  
mno  
  
Array Resized : 4  
Array Resized : 5  
0  
1  
2  
3  
4  
>>>
