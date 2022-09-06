# 15. LINQ

- LINQ는 원래 DBMS에서 사용하던 SQL을 본떠 프로그래밍 언어 안에 통합한 것이다.

## 15.1 데이터! 데이터! 데이터!

- LINQ는 지루한 데이터 작업을 해방시켜준다.
- LINQ는 Language INtegrated Query의 약자로, C# 언어에 통합된 데이터 질의 기능을 말한다.
- 데이터 질의(Query) 기능. 질의란, 뭔가에 대해 물어본다는 뜻이다.
- "데이터 질의"는 데이터에 대해 물어보는 것이다. 기본적으로 다음 내용을 포함한다.
  - From : 어떤 데이터의 집합에서 찾을 것인가?
  - Where : 어떤 값의 데이터를 찾을 것인가?
  - Select : 어떤 항목을 추출할 것인가?
  ```cs
  class Profile {
    public string Name { get; set; }
    public int Height { get; set; }
  }

  Profile[] arrProfile = {
    new Profile(){ Name = "정우성", Height = 186 },
    new Profile(){ Name = "김태희", Height = 158 },
    new Profile(){ Name = "고현정", Height = 172 },
    new Profile(){ Name = "이문세", Height = 178 },
    new Profile(){ Name = "하동훈", Height = 171 }
  };

  // Height 프로퍼티가 175 미만인 데이터만 골라 새 컬렉션으로 추출해야 한다면 (기존방법)
  List<Profile> profiles = new List<Profile>();

  foreach ( Profile profile in arrProfile )
    if ( profile.Height < 175 )
      profiles.Add( profile );
  
  profiles.Sort(
    (profile1, profile2) => {
      return profile1.Height - profile2.Height;
    }
  );

  foreach ( var profile in profiles )
    Console.WriteLine("{0}, {1}", profile.Name, profile.Height );

  // 이를 LINQ를 통해 간단히 나타낸다.
  var profiles =
    from profile in arrProfile // arrProfile 안에 있는 각 데이터로부터
    where profile.Height < 175 // Height가 175 미만인 객체만 골라
    orderby profile.Height // 키순으로 정렬하여
    select profile; // profile 객체를 추출한다.
  
  foreach ( var profile in profiles )
    Console.WriteLine( "{0}, {1}", profile.Name, profile.Height );
  ```


<br><br>

## 15.2 LINQ의 기본: from, where, orderby, select

### 15.2.1 from

- 모든 LINQ 쿼리식(Query Expression)은 반드시 from 절로부터 시작된다.
- 쿼리식 대상이 될 데이터 원본(Data Source)과 데이터 원본 안에 들어 있는 각 요소 데이터를 나타내는 범위변수(Range Variable)를 from 절에서 지정해줘야 한다.
- 이때, from의 데이터 원본은 아무 형식이나 사용할 수 없고, IEnumerable<T> 인터페이스를 상속하는 형식이어야만 한다.
- 배열이나 컬렉션 객체들은 IEnumerable<T>를 상속하기 때문에 이들은 모두 from 절의 데이터 원본으로 사용할 수 있다.
- 범위 변수는 쿼리 변수(Query Value)라고도 하는데, foreach문의 반복 변수를 생각하면 이해가 쉽다.
- from절은 다음과 같이 `from <범위변수> in <데이터 원본>`의 형식으로 사용한다.
  ```cs
  int[] numbers = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

  var result =
    from n in numbers
    where n % 2 == 0
    orderby n
    select n;
  ```

<br>

### 15.2.2 where

- where 연산자를 인수로 입력하면 LINQ는 해당 조건에 부합하는 데이터만을 걸러낸다.

<br>

### 15.2.3 orderby

- orderby는 데이터의 정렬을 수행하는 연산자이다.
  ```cs
  //오름차순
  orderby profile.Height // 키순으로 정렬
  orderby profile.Height ascending  // 또는 지정
  //내림차순
  orderby profile.Height descending // 키 역 순으로 정렬
  ```
<br>

### 15.2.4 select

- select 절은 최종 결과를 추출하는 쿼리식의 마침표이다.
- select 문을 이용하여 최종 결과를 추출해 낸다.
  ```cs
  select profile;
  select profile.Name; // IEnumerable<string> 형식으로 컴파일 된다.
  select new { Name = profile.Name, InchHeigh = profile.Height * 0.393 }; // 새로운 형식을 즉석으로 만들 수 있다.
  ```

<br><br>

## 15.3 여러 개의 데이터 원본에 질의하기

- LINQ 쿼리식은 데이터 원본에 접근하기 위해 from 절을 사용한다.
- 여러 개의 데이터 원본에 접근하려면 from 문을 중첨해서 사용하면 된다.
  ```cs
  class Class {
    public string Name { get; set; }
    public int[] Score { get; set; }
  }

  Class[] arrClass = {
    new Class(){Name = "A Class", Score = new int[] { 99, 80, 70, 24 } },
    new Class(){Name = "B Class", Score = new int[] { 60, 45, 87, 72 } },
    new Class(){Name = "C Class", Score = new int[] { 92, 30, 85, 94 } },
    new Class(){Name = "D Class", Score = new int[] { 90, 88, 00, 17 } }
  };

  var classes = from c in arrClass
                  from s in c.Score
                  where s < 60
                select new { c.Name, Lowest = s };
  ```

<br><br>

## 15.4 group by로 데이터 분류하기

- 어떤 것을 특정 기준에 따라 나누어 정리하는 것을 group by 절을 통해 해낸다.
  ```cs
  group A by B into C // from 절에서 뽑아낸 변수 A, 분류기준 B, C에 그룹 변수를 위치

  // 분류기준 : 175 미만? 또는 175 이상?
  var listProfile = from profile in arrProfile
                    group profile by profile.Height < 175 into g 
                    select new { GroupKey = g.Key, Profile = g }; // g.Key : true, false

  foreach ( var Group in listProfile ) {
    Console.WriteLine("-175cm 미만? : {0}", Group.GroupKey );

    foreach ( var profile in Group.Profiles ) {
      Console.WriteLine(">>> {0}, {1}", prifile.Name, profile.Height );
    }
  }
  ```
>>>
### 실행 결과
\- 175cm 미만? : True  
\>>> 김태희, 158  
\>>> 하하, 171  
\>>> 고현정, 172  
\- 175cm 미만? : False  
\>>> 이문세, 178  
\>>> 정우성, 186  
>>>

<br><br>

## 15.5 두 데이터 원본을 연결하는 join

- join은 두 데이터 원본을 연결하는 연산이다.
- 각 데이터 원본에서 특정 필드값을 비교하여 일치하는 데이터끼리 연결을 수행한다.

<br>

### 15.5.1 내부 조인

- 내부 조인 (Inner Join)은 교집합과 비슷하다.
- 두 데이터 사이에서 일치하는 데이터들만 연결한 후 반환한다.
- from 절에서 나온 기준 데이터 a, 연결 대상 데이터 b 는 join 절에서 뽑아낸 변수이다.
- on 절의 조인 조건은 "동등 (Equality)" 만 허용한다. (비교 불가)
  ```cs
  from a in A
  join b in B on a.XXX equals b.YYY

  var listProfile = // 배우 프로필 정보와 작품 정보를 내부 조인하여 새로운 컬렉션을 만든다.
    from profile in arrProfile // string Name, int Height
    join product in arrProduct on profile.Name equals product.Star // class Product : string Title, string Star
    select new
    {
      Name = profile.Name,
      Work = product.Title,
      Height = profile.Height
    };
  ```

<br>

### 15.5.2 외부 조인

- 외부 조인 (Outer Join)은 기본적으로 내부 조인과 비슷하지만, 조인 결과에 기준이 되는 데이터 원본이 모두 포함된다는 점은 다르다.
- 해당이 되지 않는 데이터는 비어있는 상태(빈 값)로 나타난다.
- 임시 컬렉션에 대해 DefaultIfEmpty 연산을 수행해서 비어있는 조인 결과에 빈 값을 채워 넣는다.
  ```cs
  var listProfile = 
    from profile in arrProfile
    join product in arrProduct on profile.Name equals product.Star into ps
    from product in ps.DefualtIfEmpty(new Product(){ Title = "그런 거 없음" } )
    select new
    {
      Name = profile.Name,
      Work = product.Title,
      Height = profile.Height
    };
  ```

<br><br>

## 15.6 LINQ의 비밀과 LINQ 표준 연산자

- LINQ를 .NET언어에서는 C#과 VB에서만 사용이 가능하다.
- 원래 LINQ는 다음과 같이 사용될 수 있다.
  ```cs
  var profiles = from profile in arrProfile
                 where profile.Height < 175
                 orderby profile.Height
                 select new { Name = profile.Name, InchHeight = profile.Height * 0.393 };
  
  var profile = arrProfile
                      .Where( profile => profile.Height < 175 )
                      .OrderBy( profile => profile.Height )
                      .Select( profile => new 
                        {
                           Name = profile.Name,
                           InchHeight = profile.Height
                        }
                      );
  ```
- 이들 메소드는 IEnumerator<T>의 확장메소드 이기 때문에, 이들을 사용하려면 System.Linq 네임스페이스를 사용하도록 선언해야 한다.
- 또한 [LINQ에는 다양한 표준 연산자](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=ljc8808&logNo=220662771778)들이 존재한다.
- 예제
  ```cs
  using System;
  using System.Linq;
  
  namespace MinMaxAvg {
      class Profile {
          public string Name { get; set; }
          public int Height { get; set; }
      }
  
      class MainApp {
          static void Main( string[] args ) {
              Profile[] arrProfile =
              {
                  new Profile(){ Name = "정우성", Height = 186 },
                  new Profile(){ Name = "김태희", Height = 158 },
                  new Profile(){ Name = "고현정", Height = 172 },
                  new Profile(){ Name = "이문세", Height = 178 },
                  new Profile(){ Name = "하하", Height = 171 }
              };
  
              var heightStat = from profile in arrProfile
                               group profile by profile.Height < 175 into g
                               select new
                               {
                                   Group = g.Key == true ? "175미만" : "175이상",
                                   Count = g.Count(),
                                   Max = g.Max( profile => profile.Height ),
                                   Min = g.Min( profile => profile.Height ),
                                   Average = g.Average( profile => profile.Height )
                               };
  
              foreach ( var stat in heightStat ) {
                  Console.Write( "{0} - Count:{1}, Max:{2}, ",
                      stat.Group, stat.Count, stat.Max );
                  Console.WriteLine( "Min:{0}, Average:{1}",
                      stat.Min, stat.Average );
              }

          }
      }
      
  }
  ```
>>>
### 실행 결과
175이상 - Count:2, Max:186, Min:178, Average:182  
175미만 - Count:3, Max:172, Min:158, Average:167  
>>>
