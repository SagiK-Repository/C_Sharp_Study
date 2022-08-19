# 05. 코드의 흐름 제어하기

## 5.1 분기문

- 분기문(Branching Statement)은 프로그램의 흐름을 조건에 따라 여러 갈래로 나누는 흐름 제어 구문이다.
- 단 한개의 조건을 평가하는 if와, 여러개의 조건을 평가하는 switch로 이루어져 있다.

<br>
<br>

### if else, else if

- if문은 단 한 번에 단 하나의 조건을 평가한다.
- if문에서 사용하는 조건식은 true 또는 false의 값을 가지는 bool 형식이어야 한다.
- else if는 if문 처럼 조건식을 가지며, if문에 종속되어 사용된다.

```cs
int a = 10;
if ( a < 0 )
    Console.WriteLine("음수");
else if ( a  > 0 )
    Console.WriteLine("양수");
else
    Console.WriteLine("0");
```

<br>
<br>

### if 문 중첩

- 분기문, 반복문 같은 흐름 제어문은 또 다른 흐름 제어문을 중첩(Nesting)해서 사용할 수 있다.
- 코드의 흐름은 사람이 쉽게 이해할 수 있도록 가급적 단순하고 명료하게 유지해야 한다.

```cs
if ( number > 0 ){
  if( number % 2 == 0 )
    Console.WriteLine("0보다 큰 짝수");
  else
    Console.WriteLine("0보다 큰 홀수");
} else {
  Console.WriteLine("0보다 작거나 같은 수");
}
```

<br>
<br>

### switch 문

- switch 문은 조건식의 결과가 가질 수 있는 다양한 경우를 한번에 평가하고 프로그램의 흐름을 가를 때 사용한다.
  ```cs
  switch ( 조건식 ){
    case 상수1:
      // 실행할 코드
      break;
    case 상수n:
      // 실행할 코드
      break;
    default: // 어떤 조건과도 맞지 않을 경우 (생략가능)
      // 실행할 코드
      break;
  }
  ```
- switch 문에 사용되는 조건식은 정수 형식과 문자열 형식 두 가지만 지원한다.
- C와 C++에서는 오로지 정수 형식 하나만 지원한다.
- `break` 문은 프로그램의 흐름을 멈추고 현재 실행중인 코드의 바깥으로 실행 위치를 옮기는 역할을 한다.
  ```cs
  int number = 1;
  switch (number) {
    case 1:
      Console.WriteLine("1");
      break;
    case 2:
      Console.WriteLine("2");
      break;
    case 3:
      Console.WriteLine("3");
      break;
    default:
      Console.WriteLine("제가 아는 숫자는 1, 2, 3 뿐 입니다.");
      break;
  }
  ```

<br><br>

### switch 문 데이터 형식 조건 활용

- C# 7.0 부터는 switch 문에 데이터 형식을 조건으로 사용할 수 있다.
  ```cs
  using System;
  namespace Switch_Object
  {
      class MainApp
      {
          static void Main(string[] args)
          {
              object obj = null;
  
              string s = Console.ReadLine();
              if (int.TryParse(s, out int out_i))
                  obj = out_i;
              else if (float.TryParse(s, out float out_f))
                  obj = out_f;
              else
                  obj = s;
  
              switch (obj)
              {
                  case int i:
                      Console.WriteLine($"{i}는 int 형식입니다.");
                      break;
                  case float f:
                      Console.WriteLine($"{f}는 flaot 형식입니다.");
                      break;
                  default: Console.WriteLine($"{obj}(은)는 모르는 형식입니다.");
                      break;
              }
          }
      }
  }
  ```
- `형식.TryParse()`와 `형식.Parse()`는 문자열을 숫자로 변환하는 특징이 있다.
- 이 둘의 차이는, 실패했을 때 예외를 발생시키는 Parse()와, 성공여부를 반환한다.
- switch문의 when절을 이용해 추가적인 조건 검사를 수행할 수 있다.
  ```cs
  switch (obj)
  {
      case int i:
          Console.WriteLine($"{i}는 int 형식입니다.");
          break;
      case float f when f >= 0 :
          Console.WriteLine($"{f}는 양수인 flaot 형식입니다.");
          break;
      case float f:
          Console.WriteLine($"{f}는 음수인 flaot 형식입니다.");
          break;
      default: Console.WriteLine($"{obj}(은)는 모르는 형식입니다.");
          break;
  }
  ```

<br><br>

### switch 식

- switch 문(Statement)와 switch 식(Expression)은 다른데, switch 식은 어떤 계산을 해서 결과를 내놓는다.
  ```cs
  switch(score){ //switch 문
    case 90:
      grade = "A";
      break;
    case 80:
      grade = "B";
      break;
    case 70:
      grade = "C";
      break;
    case 60:
      grade = "D";
      break;
    default :
      grade = "F";
  }
  string grade = score switch{ //switch 식
    90 when repeated == true => "B+", //when 사용 가능
    90 => "A",
    80 => "B",
    70 => "C",
    60 => "D",
    _ => "F"
  };
  ```

<br><br>

## 5.2 반복문
- 반복문(Loop Statement)는 특정 조건을 만족하는 동안 코드 또는 코드블록을 반복해서 실행하도록 하는 문장이다.

<br>

### while

```cs
while(조건식)
    반복 실행할 코드
```

- while은 "~하는 동안"이라는 뜻이 있다.
- C#에서는 조건문이 참인경우에만 코드를 반복 실행한다.
  ```cs
  while ( a > 0)
      Console.WriteLine(a--); // 5 4 3 2 1
  while ( b > 0){             // 5 3 1
      Console.WriteLine(b);
      b-=2;
  }
  ```

<br>
<br>

### do while

```cs
do{
  반복 실행할 코드 블록
}while( 조건식 );
```

- do while 문은 while 문과 유사한 반복문이지만, do while 문은 조건식을 평가하기 전에 처음 한번은 실행한다는 점이 다르다.
  ```cs
  do{
    Console.WriteLine(a);
    a -= 2;
  }while( a > 0 ); // 5 3 1
  ``` 

<br>
<br>


### for

- for는 영어로 "~를 위하여"라는 뜻으로 시작해 굉장히 많은 뜻을 갖고 있다.
- 여기서는 "~하는 동안"을 의미한다.
- while 문 보다 더 정교하게 제어할 수 있다는 차이가 있다.
  ```cs
  for( 초기화식; 조건식; 반복식;)
      반복실행할_코드_또는_코드블록;
  ```
- 초기화식 : 반복 실행하기 전에 가장 먼저 실행하는 코드이다.
- 조건식 : 반복을 계속 수행할 지 결정하는 식이다.
- 반복식 : 반복이 끝날 때 마다 실행된다.
  ```cs
  for(int i = 0; i < 10; i++)
      Console.WriteLine(i);
  ```

<br>
<br>

### 중첩 for

- for문은 if와 같이 중첩해서 활용할 수 있다.
- 반복문을 겹쳐서 사용할 때, 가장 적합하다.
  ```cs
    for(int i = 0; i < 10; i++)
      for(int j = 0; j < 10; j++)
        Console.WriteLine("안녕");
  ```

```cs
using System;
namespace Switch_Object{
    class MainApp {
        static void Main(string[] args) {

            for (int i = 0; i < 5; i++)
            {
                for (int j = 0; j <= i; j++)
                    Console.Write("*");
                Console.WriteLine();
            }

        }
    }
}
```
>>>
### 실행 결과
\*  
\**  
\***  
\****  
\*****  
>>>

<br>
<br>

### foreach

- foreach문을 이해하기 위해서는 배열이나 컬렉션 개념을 이해하고 있어야 한다.
- 배열 : 여러 개의 데이터를 담을 수 있는 코드 요소이다.
- 컬렉션 : 여러 개의 데이터를 담는 코드 요소로 배열과 비슷하지만, 사용하는 방식과 데이터를 저장하고 접근하는 알고리즘이 다르다.
- foreach문은 배열, 컬렉션을 순회하며 각 데이터 요소를 차례대로 접근하도록 해준다.
- 끝에 도달하면 자동으로 반복을 종료한다.
  ```cs
  foreach(데이터 형식 변수명 in 배열_또는_컬렉션)
      코드_또는_코드블록
  
  int[] arr = new int[]{0, 1, 2, 3, 4};
  for(int a in arr)
      Console.WriteLine(a); // 0 1 2 3 4
  ```

<br>
<br>

### for 또는 while을 이용한 무한 반복 코드

- for 문은 프로그래머에게 몇 번이나 코드를 반복 실행할 지 반드시 입력하도록 요구한다.
- for 문이 무한하게 코드를 실행하도록 만들 수 있다. 매게변수에 아무것도 넣지 않으면 된다.
- while 문, do While 문을 통해 무한 반복 코드를 만들 수 있다.

```cs
for ( ; ; )
  // 반복 실행할 코드 또는 코드 블록

while ( true )
  // 반복 실행할 코드 또는 코드 블록
```

<br><br>

## 5.3 점프문

- 흐름 제어문들은 흐름을 분기하거나 반복하더라도 흐름을 끊는 기능은 없다.
- 이러한 기능을 점프문(Jump Statement)이 해준다.
- 흐름을 끊고 프로그래밍의 실행 위치를 원하는 곳으로 단숨에 도약시킬 수 있다.
- C#에서 제공하는 점프문은 5가지 있다.
  - break
  - continue
  - goto
  - return
  - throw

<br>
<br>

### 5.3.1 break

- break 문은 영어로 '탈출하다', '중단하다' 라는 뜻으로, 현재 실행중인 반복문, switch 문의 실행을 중단하고자 할 때 사용한다.

```cs
while (a > 0) {
  a--;
  if( a == 3 )
    break;
}
```

<br>
<br>

### 5.3.2 continue

- 반복문을 멈처게 하는 break와 달리, continue 문은 한 회 건너 뛰어 반복을 계속 수행하게 하는 기능을 한다.
- 가독성이 좋다.

```cs
for (int i = 0; i < 5; i++) {
  if( i == 3 )
    continue;
  Console.WriteLine( i );
}
```

<br>
<br>

### 5.3.3 goto

- goto 문은 점프문 중에서 가장 터프한 점프문이다.
- goto 문의 사용 형식에서 레이블(Lable)은 코드 안의 위치를 나타내는 표지판 같은 존재이다.
- goto 문은 레이블이 가리키는 곳으로 바로 뛰어 넘어간다.
- goto 문은 해악으로 여기며, 흐름을 자주 끊어 코드를 읽기 어렵게 만들기 때문이다.
- 중첩된 반복문을 뚫고 나올 때 유용하게 사용한다.

```cs
goto 레이블;

레이블 : 
 // 이어지는 코드
```

```cs
Console.WriteLine("1");

goto JUMP:

Console.WriteLine("2");
Console.WriteLine("3");
Console.WriteLine("4");

JUMP:
Console.WriteLine("5"); // 1, 5만 출력
```
