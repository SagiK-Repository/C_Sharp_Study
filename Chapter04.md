# 04. 데이터를 가공하는 연산자

## 4.1 C#에서 제공하는 연산자(Operation)

- C# 프로그래밍에서는 데이터를 자유자재로 다둘 수 있도록 사칙연산을 수행하는 산술 연산자부터, 데이터 비트를 다룰 수 있는 비트 연산자 등등 다양한 연산자를 제공한다.
- 산술 연산자
  - +, -, *, / ,%로 각각 덧셈, 뺄셈, 곱셈, 나눈 몫, 나머지를 구한다.  
  - 예) `int a = 3 + 4`  
  - 우선순위로는 *, /, % 다음으로 +, - 가 진행된다.  
  - 괄호를 통해 연산 순서를 지정할 수 있다.
- 증가 연산자와 감소 연산자
  - ('++' / '--')  
  - 피 연산자 앞 또는 뒤에 붙느냐에 따라 "전위, 후위 증가/감소 연산자"라 한다.
  - 문자열 결합 연산자
  - `string result = "123" + "456";` > "123456"
- 관계 연산자
  - <, <=, >, >=, ==(같으면), !=(다르면) 등이 있다.
  - 각각 비교를 하는 연산자이다.
  - true, false를 반환한다.
- 논리 연산자
  - 논리곱 연산자(&& : AND), 논리합 연산자(|| : OR), 부정 연산자(! : NOT) 
- 조건 연산자
  - `조건식 ? 참일때 값 : 거짓일때 값`
  - `int a = 30; string result = a == 30 ? "삼십" : "삼십 아님";` > "삼십"
- null 조건부 연산자
  - 널 조건 부 연산자 ?.는 C# 6.0에서 도입 되었다. 이 연산자는 엘비스(Elvis)라는 별명을 갖고 있다.
  - ?.가 하는 일은 객체의 맴버에 접근하기 전 객체가 null인지 검사하여 참이면 . 뒤에 지정된 맴버를 반환하고, 그렇지 않으면 반환하지 않는다.
    ```cs
    class Foo{
      public int member;
    }
    Foo foo = null;
    int? bar;
    
    if(foo == null) bar == null; else bar = foo.member; //일반 방식
    bar = foo?.member; //null 조건부 연산자
    ```
- 비트 연산자
  - <<(왼쪽 시프트), >>(오른쪽 시프트), &(논리곱), |(논리합), ^(XOR), ~(보수 NOT) 등이 있다.
- 할당 연산자(Assignment Operators)
  - =(할당 연산자), +=(덧셈), -=, *=, /=, %=, &=, |=, ^=, <<=, >>= 등이 있다.
- null 병합 연산자
  - null 병합 연산자 ??는 null 조건부 연산과 같이, 변수/객체의 null 검사를 간결하게 만들어주는 역할을 한다.
  - `a??0`, a가 null이면 0, a가 값이 존재하면 a를 반환한다.
- 연산자 운선순위

| 우선순위 | 연산자                               |
|:----:|-----------------------------------|
| 1    | 후위++/-- ?. ?[ ]                  |
| 2    | 전위++/--                           |
| 3    | * / %                             |
| 4    | + -                               |
| 5    | << >>                             |
| 6    | < > <= >= is as                   |
| 7    | == !=                             |
| 8    | &                                 |
| 9    | ^                                 |
| 10   | \|                                 |
| 11   | &&                                |
| 12   | \|\|                                |
| 13   | ??                                |
| 14   | ?:                                |
| 15   | = *= /= %= += -= <<= >>= &= ^= \|= |


```cs
using System;
using static System.Console;
namespace Operators {
    class MainApp {
        static void Main(string[] args) {
            double a = 111 + (200 - 100 * 10) / 6.3; // -15.98412698412698
            WriteLine("a : {0}", a);
            WriteLine("a : {0}", a++); //연산 후 더해짐
            WriteLine("a : {0}", a);
            WriteLine("a : {0}", ++a);
            WriteLine($"22 / 7 = {22/7}({22%7})"); // 22 / 7 = 3(1)
            WriteLine();

            string result = "123" + "와" + "456" + "이다";
            WriteLine(result); // 123와456이다
            WriteLine($"{4 == 5} {3 >= 4} {3 != 4}"); // False False True
            WriteLine($"{4 <= 5 || 3 > 5} {4 <= 5 && 3 > 5} {!true}"); // True False False
            WriteLine($"{ ( (10 % 2) == 0 ? "짝수" : "홀수" ) }"); // 짝수 
            WriteLine();

            ArrayList a = null;
            ar?.Add("야구"); ar?.Add("축구"); // a == null 이기 때문에 실행이 안됨
            WriteLine($"Count : {a?.Count}"); // Count : 
            WriteLine($"{a?[0]}");
            WriteLine($"{a?[1]}");

            a = new ArrayList();
            ar?.Add("야구"); ar?.Add("축구");
            WriteLine($"Count : {a?.Count}"); // Count : 2
            WriteLine($"{a?[0]}"); // 야구
            WriteLine($"{a?[1]}"); // 축구
        }
    }
}
```
>>>
### 실행 결과
a : -15.984126984126988  
a : -15.984126984126988  
a : -14.984126984126988  
a : -13.984126984126988  
22 / 7 = 3(1)  
  
123와456이다  
False False True  
True False False  
짝수  

Count :  


Count : 2  
야구  
축구
>>>
