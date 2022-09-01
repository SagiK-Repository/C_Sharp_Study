# 10. 배열과 컬렉션 그리고 인덱서

## 10.1 All for one, One for all

- 배열은 다음과 같이 선언된다.
  ```cs
  int[] scores = new int[5];
  score[0] = 80;
  score[1] = 74;
  score[2] = 81;
  ```
- 다음과 같이 for문이나 foreach문과 함께 사용하여 코드를 간결화할 수 있다.
  ```cs
  Console.WriteLine( score_1 );
  Console.WriteLine( score_2 );
  Console.WriteLine( score_3 );
  Console.WriteLine( score_4 );
  Console.WriteLine( score_5 );
  // to
  foreach( int score in scores )
    Console.WriteLine( score );

  //...//

  int average = ( score_1 + score_2 + score_3 + score_4 + score_5 );
  // to
  int sum = 0;
  foreach( int score in socres )
    sum += score;
  
  int average = sum / score.Length;

  ```
- C# 8.0부터 마지막 인덱스에 대한 불편을 없엔 System.Index 형식과 ^ 연산자가 생겼다.
- ^ 연산자는 컬렉션의 마지막부터 역순으로 인덱스를 지정하는 기능을 갖고 있다.
- ^1은 컬랙션의 마지막 요소를 나타내는 인덱스, ^2는 마지막에서 두번째 인덱스를 나타낸다.
- ^1은 score.Length-1와 같은 의미를 나타낸다.
  ```cs
  System.Index last = ^1;
  score[last] = 34; // score[score.Length-1] = 34; 와 동일
  score[^1] = 34;
  ```


<br><br>

## 10.2 배열을 초기화하는 방법 세 가지

- 배열을 초기화 하는 세 가지 방법에 대해 알아본다.
- 첫 번째 방법은, 배열의 원소 개수를 명시한 뒤 중괄호 {}로 둘러싸인 블록을 붙여 원소를 입력한다.
- 이를 컬렉션 초기자 (Collection Initalizer)라고 부른다.  
  `string[] array1 = new string[3] { "안녕", "Hello", "World" };`
- 두 번째 방법은 배열의 용량을 생략하는 것이다.  
  `string[] array2 = new string[] { "안녕", "Hello", "World" };`
- 세 번째 방법은 new 연산자, 형식과 대괄호 [], 배열의 용량을 모두 생략한채, 코드 블록 사이에 배열의 각 원소에 할당할 데이터를 넣어주는 방법이다.  
  `string[] array3 = { "안녕", "Hello", "World" };`

<br><br>



## 10.3 알아두면 삶이 윤택해지는 System.Array

- C#은 모든 것이 객체이다. 배열도 객체이며, .NET의 CTS(Common Type System)에서 배열은 System.Array 클래스에 대응된다.
- \<T>는 형식 매개변수(Type Parameter)라고 한다.
  - T 대신 배열의 기반 자료형을 인수로 입력하면 컴파일러가 해당 형식에 맞춰 동작하도록 메소드를 컴파일 한다.

| 분류       | 이름                 | 설명            |
|----------|--------------------|--------------------|
| 정적 메소드   | Sort( )            | 배열을 정렬     |
|          | BinarySearch\<T>( ) | 이진 탐색을 수행  |
|          | IndexOf()          | 배열에서 찾고자 하는 특정 데이터의 인덱스를 반환   |
|          | TrueForAll\<T>( ) | 배열의 모든 요소가 지정한 조건에 부합하는지의 여부를 반환    |
|          | FindIndex\<T>( )  | 배열에서 지정한 조건에 부합하는 첫 번째 요소의 인덱스를 반환. IndexOf()메소드가 특정 값을 찾는데 비해, FindIndex\<T>()메소드는 지정한 조건에 바탕하여 값을 찾음. |
|          | Resize\<T>( )       | 배열의 크기를 재조정    |
|          |  Clear( )          | 배열의 모든 요소를 초기화. 배열이 숫자 형식 기반이면 0으로, 논리형식 기반이면 false로, 참조형식 기반이면 null로 초기화      |
|          | ForEach\<T>( )      | 배열의 모든 요소에 대해 동일한 작업을 수행하게 함  |
| 인스턴스 메소드 | GetLength( )       | 배열에서 지정한 차원의 길이를 반환  |
| 프로퍼티     | Length             | 배열의 길이를 반환   |
|          | Rank               | 배열의 차원을 반환   |

- 예제
  ```cs
  using System;
  namespace MoreOnArray {
      class MainApp {
          private static bool CheckPassed(int score) {
              return score >= 60;
          }
          private static void Print(int value) {
              Console.Write($"{value} ");
          }
          static void Main(string[] args) {
              // 배열 선언
              int[] scores = new int[] { 80, 74, 81, 90, 34 };
  
              // foreach문
              foreach (int score in scores)
                  Console.Write($"{score} ");
              Console.WriteLine();
  
              // 정렬
              Array.Sort(scores);
              // 모든 요소에 동일한 작업 수행
              Array.ForEach<int>(scores, new Action<int>(Print)); // <int> : 배열 기반 자료형
              Console.WriteLine();
  
              // 차원 반환
              Console.WriteLine($"Number of dimensions : {scores.Rank}");
  
              // 이진 탐색
              Console.WriteLine($"Binary Search : 81 is at " +
                  $"{Array.BinarySearch<int>(scores, 81)}");
  
              // 찾고자 하는 특정 데이터의 인덱스 반환
              Console.WriteLine($"Linear Search : 90 is at " +
                  $"{Array.IndexOf(scores, 90)}");
  
              // 모든 요소가 지정한 조건에 부합하는지 여부 반환
              Console.WriteLine($"Everyone passed ? : " +
                  $"{Array.TrueForAll<int>(scores, CheckPassed)}");
              // CheckPassed : TrueForAll 메소드는 배열과 함께 '조건을 검사하는 메소드'를 매개변수로 받음
  
              // 지정 조건에 부합하는 첫 번째 요소의 인덱스 반환
              int index = Array.FindIndex<int>(scores, (score) => score < 60);
              // 람다식 : FindIndex 메소드는 `특정 조건에 부합하는 메소드`를 매개변수로 받음 
  
              // 데이터 저장 및 변경
              scores[index] = 61;
  
              // 모든 요소가 지정한 조건에 부합하는지 여부 반환
              Console.WriteLine($"Everyone passed ? : " +
                  $"{Array.TrueForAll<int>(scores, CheckPassed)}");
  
              // 지정한 차원의 길이 반환 (다차원 배열에서 유용하게 사용)
              Console.WriteLine("Old length of scores : " +
                  $"{scores.GetLength(0)}");
  
              // 크기 재조정 : 배열 용량 10으로 재조정
              Array.Resize<int>(ref scores, 10);
  
              // 길이/용량 반환
              Console.WriteLine($"New length of scores : {scores.Length}");
  
              // 모든 요소에 동일한 작업 수행
              Array.ForEach<int>(scores, new Action<int>(Print));
              Console.WriteLine();
  
              // 모든 요소 초기화
              // 숫자 형식 → 0 | 논리 형식 → false | 참조 형식 → null
              Array.Clear(scores, 3, 7); // 인덱스 3~7까지 초기화
              Array.ForEach<int>(scores, new Action<int>(Print));
              Console.WriteLine();
  
              // 분할 : 배열의 일부를 다른 곳에 복사 
              int[] sliced = new int[3];
              Array.Copy(scores, 0, sliced, 0, 3);
              Array.ForEach<int>(sliced, new Action<int>(Print));
              Console.WriteLine();
          }
      }
  }
  ```

<br><br>

## 10.4 배열 분할하기

- C# 8.0에서 System.Index 형식과 함께 도입된 System.Range를 익혀본다.
- System.Range는 시작 인덱스와 마지막 인덱스를 이용해서 범위를 나타낸다.
  ```cs
  System.Range r1 = 0..3; //시작 인덱스 0, 마지막 인덱스 3
  int[] sliced = scores[r1];
  int[] sliced2 = scores[0..3]; // 첫 번째(0)부터 세 번째(2) 요소까지
  int[] sliced3 = scores[1..]; // 두 번째(1)부터 마지막 요소까지
  int[] sliced4 = scores[..]; // 전체

  System.Index idx = ^1;
  int[] sliced4 = scores[idx]; // 뒤에서 두 번째 요소까지
  int[] sliced4 = scores[^1];
  int[] sliced4 = scores[^4..^1]; // 끝에서 4번째 부터 끝ㄴ에서 2번째 까지
  ```
<br><br>

## 10.5 2차원 배열

- 2개의 차원(가로+세로)로 원소를 배치하는 2차원 배열이다.
  ```cs
  int[,] array = new int [ 2, 3 ];
  array[ 0, 0 ] = 1;
  array[ 0, 1 ] = 2;
  array[ 0, 2 ] = 3;
  array[ 1, 1 ] = 4;

  int[,] arr1 = new int [ 2, 3 ] { {1, 2, 3}, {4, 5, 6} };
  int[,] arr2 = new int [,] { {1, 2, 3}, {4, 5, 6} };
  int[,] arr3 = { {1, 2, 3}, {4, 5, 6} };
  ```

<br><br>

## 10.6 다차원 배열

- 다차원 배열이란, 차원이 둘 이상인 배열을 말한다.
  ```cs
  int[,,] array = new int [ 4, 3, 2 ]
    {
        { {1, 2}, {3, 4}, {5, 6} },
        { {1, 4}, {2, 5}, {3, 6} },
        { {6, 5}, {4, 3}, {2, 1} },
        { {6, 3}, {5, 2}, {4, 1} }
    };
  ```

<br><br>

## 10.7 가변 배열

- 가변 배열의 요소로 입력되는 배열은 그 길이가 모두 같을 필요가 없다.
- Jagged 배열이라고도 한다.
  ```cs
  int[][] jagged = new int[3][];
  jagged[0] = new int[5] { 1, 2, 3, 4, 5 };
  jagged[1] = new int[] { 1, 2, 3 };
  jagged[2] = new int[] { 1, 2 };

  int[][] jagged2 = new int[2][] {
    new int[] { 1000, 2000 },
    new int[4] { 6, 7, 8, 9 }
  };
  ```

<br><br>

## 10.8 컬렉션 맛보기

- 컬렉션(Collection)이란, 같은 성격을 띈 데이터의 모음을 담는 자료구조를 말한다.

<br>

### 10.8.1 ArrayList

- ArrayList는 가장 배열과 닮은 컬렉션이다.
- ArrayList에서 가장 중요한 메소드는 Add(), RemoveAt(), Insert() 이렇게 3개 있다.
  - Add() 메소드는 컬렉션의 가장 마지막에 있는 요소 뒤에 새 요소를 추가한다.
  - RemoveAt() 메소드는 특정 인덱스에 있는 요소를 제거한다.
  - Insert() 메소드는 원하는 위치에 새 요소를 삽입한다.
  ```cs
  ArrayList list = new ArrayList();
  list.Add( 10 );
  list.Add( 20 );
  list.Add( 30 ); // 10, 20, 30

  list.RemoveAt( 1 ); // '20'을 삭제  //  10, 30
  list.Insert( 1, 25 ); // '25'를 1번 인덱스에 삽입한다.  // 10, 25, 30
  ```

<br>

### 10.8.2 Queue

- Queue는 대기열, 즉 기다리는 줄(열)이라는 뜻이다.
- Queue 자료구조는 데이터나 작업을 차례대로 입력해 두었다가 입력된 순서대로 하나씩 꺼내 처리하기 위해 사용된다.
  ```cs
  Queue que = new Queue();
  que.Enqueue(1); // 1
  que.Enqueue(2); // 1 2
  que.Enqueue(3); // 1 2 3

  int a = que.Dequeue(); // 1이 나온다. // 2 3
  ```
- 예제
  ```cs
  using System;
  using System.Collections;
  namespace UsingQueue  {
      class MainApp  {
          static void Main(string[] args)  {
              // Queue 선언
              Queue que = new Queue();
              // 삽입
              que.Enqueue(1);
              que.Enqueue(2);
              que.Enqueue(3);
              que.Enqueue(4);
              que.Enqueue(5);
  
              while (que.Count > 0)
                  Console.WriteLine(que.Dequeue()); // 삭제
          }
      }
  }
  ```

<br>

### 10.8.3 Stack

- Stack은 Queue와 반대로 먼저 들어온 데이터가 나중에 나간다. (First In - Last Out)
  ```cs
  Stack stack = new Stack();
  stack.Push( 1 ); // 1
  stack.Push( 2 ); // 1 2
  stack.Push( 3 ); // 1 2 3

  int a = (int)stack.Pop(); // 3이 나온다. // 1 2
  ```

<br>

### 10.8.4 Hashtable

- Hashtable은 캐(Key)와 값(Value)의 쌍으로 이루어진 데이터를 다룰 때 사용한다.
- 탐색 속도도 빠르고 사용하기 편하다.
  ```cs
  Hashtable ht = new Hashtable();
  ht["book"] = "책";
  ht["cook"] = "요리사";
  ht["tweet"] = "지저귀다";
  
  Console.WriteLine( ht["book"] );
  Console.WriteLine( ht["cook"] );
  Console.WriteLine( ht["tweet"] );
  ```

<br><br>

## 10.9 컬렉션을 초기화 하는 방법

- ArrayList, Queue, Stack은 배열의 도움을 받아 간단하게 초기화를 수행할 수 있다.
  ```cs
  int[] arr = { 123, 456, 789 };

  ArrayList list = new ArrayList(arr); // 123, 456, 789
  ArrayList list2 = new ArrayList() { 11, 22, 33 }; // 11, 22, 33
  Queue queue = new Queue(arr); // 123, 456, 789
  Stack stack = new Stack(arr); // 789, 456, 123
  ```
- Hashtable 초기화 할 때는 딕셔너리 초기자 (Dicrionary Initializer)를 이용한다.
  ```cs
  Hashtable ht1 = new Hashtable()
  {
    ["하나"] = 1,
    ["둘"] = 2,
    ["셋"] = 3
  };
  Hashtable ht2 = new Hashtable()
  {
    { "하나", 1 },
    { "둘", 2 },
    { "셋", 3}
  };
  ```

<br><br>

## 10.10 인덱서

- 인덱서(Indexer)는 인덱스(Index)를 이용해서 객체 내의 데이터에 접근하게 해주는 프로퍼티라고 생각하면 이해하기 쉽다.
- 객체를 마치 배열처럼 사용할 수 있게 해준다.
  ```cs
  class MyList {
    private int[] array;

    public MyList() {
      array = new int[3];
    }

    public int this[int index] {
      get { return array[index]; }
      set {
        if (index >= array.Length){
          Array.Resize<int>(ref array, index + 1);
          Console.WriteLine("Array Resized : {0}", array.Length);
        }
        array[index] = value;
      }
    }
  }
  ```

<br><br>

## 10.11 foreach가 가능한 객체 만들기

- foreach문은 배열이나 리스트 같은 컬렉션에서만 사용할 수 있다.
- foreach문은 IEnumerable을 상속하기만 하면 foreach문을 이용해서 요소를 순회할 수 있게 할 수 있다.
- yield return 문은 현재 매소드의 실행을 일시정지 시켜놓고 호출자에게 결과를 반환한다.
  ```cs
  using System;
  using System.Collections; // IEnumerator 상속 클래스 직접 구현 //;
  
  namespace Yield
  {
      class MyEnumerator
      {
          int[] numbers = { 1, 2, 3, 4 };
          public IEnumerator GetEnumerator()
          {
              yield return numbers[0];
              yield return numbers[1];
              yield return numbers[2];
              yield break; // GetEnumerator() 메소드 종료시킨다.
              yield return numbers[3];  // 따라서 코드는 실행되지 않는다.
          }
      }
      class MainApp
      {
  
          static void Main(string[] args)
          {
              var obj = new MyEnumerator();
              foreach (int i in obj)
                  Console.WriteLine(i);
          }
      }
  }
  ```
- GetEnumerator() 메소드는 IEnumerator 형식의 객체, IEnumerator 인터페이스를 상속하는 클래스의 객체를 반환하면 된다.
- 다음 표는 IEnumerator 인터페이스의 메소드 및 프로퍼티 목록이다.
- 
  | 메소드 또는 프로퍼티 | 설명    |
  |:-------------------:|:-------|
  | boolean MoveNext( )  | 다음 요소로 이동. 컬렉션의 끝을 지난 경우에는 false, 이동이 성공한 경우에는 true를 반환  |
  | void Reset( )        | 컬렉션의 첫 번째 위치의 "앞"으로 이동.<br> 첫 번째 위치가 0번이라면, Reset( ) 을 호출하면-1번으로 이동.<br> 첫 번째 위치로의 이동은 MoveNext( )를 호출한 다음에 이루어짐.  |
  | Object Current{get;} | 컬렉션의 현재 요소를 반환  |  
- MoveNext(), Reset() 메소드와 Current 프로퍼티를 구현하면 IEnumerator의 요구사항을 총족한다.
  ```cs
  using System;
  using System.Collections;
  namespace Enumerable  {
      class MyList : IEnumerable, IEnumerator  {
          private int[] array;
          int position = -1;  // 컬렉션의 현재 위치를 다루는 변수, 첫 번째 반복 때 0이 되어야 하므로 
  
          public MyList() {
              array = new int[3];
          }
  
          public int this[int index]  {
              get { return array[index]; }
  
              set {
                  if (index >= array.Length) {
                      Array.Resize<int>(ref array, index + 1);
                      Console.WriteLine($"Array Resized : {array.Length}");
                  }
                  array[index] = value;
              }
          }
  
          // IEnumerator 멤버
          public object Current {  // 현재 위치의 요소 반환
              get { return array[position]; }
          }
  
          // IEnumerator 멤버
          public bool MoveNext() {  // 다음 위치의 요소로 이동
              if (position == array.Length - 1) {
                  Reset();
                  return false;
              }
              position++;
              return (position < array.Length);
          }
  
          // IEnumerator 멤버
          public void Reset() {  // IEnumerator로부터 상속받은 Reset()메소드, 요소의 위치를 첫 요소의 "앞"으로 옮김
              position = -1;
          }
  
          // IEnumerable 멤버
          public IEnumerator GetEnumerator() {
              return this;
          }
      }
  
      class MainApp {
          static void Main(string[] args)  {
              MyList list = new MyList();
              for (int i = 0; i < 5; i++)
                  list[i] = i;
  
              foreach (int e in list)
                  Console.WriteLine(e);
          }
      }
  }
  ```
