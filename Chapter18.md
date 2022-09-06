# 18. 파일 다루기

## 18.1 파일 정보와 디렉터리 정보 다루기

- 파일(File)은 컴퓨터 저장 매체에 기록되는 데이터의 묶음이다.
- 디렉터리(Directory)는 파일이 위치하는 주소로, 파일(서류)를 담는다는 의미에서 폴더(Folder)라고 불린다.
- .NET은 파일과 디렉터리 정보를 손쉽게 다룰 수 있도록 System.IO 네임스페이스 아래 클래스들을 제공한다.
  | 클래스           | 설명                                                    |
  |---------------|-------------------------------------------------------|
  | File          | 파일의 생성, 복사, 삭제, 이동, 조회를 처리하는 정적 메소드를 제공               |
  | FileInfo      | File 클래스와 하는일은 동일하지만, 정적 메소드 대신 인스턴스 메소드를 제공한다.       |
  | Directory     | 디렉터리의 생성, 삭제, 이동, 조회를 처리하는 정적 메소드를 제공한다.              |
  | DirectoryInfo | Directory 클래스와 하는 일은 동일하지만, 정적 메소드 대신 인스턴스 메소드를 제공한다. |
- 하나의 파일/폴더에 한두 가지 정도의 작업을 할 때는 File/Directory 클래스의 정적 메소드를 이용하고, 하나 파일/폴더에 여러 작업을 수행할 떄는 FileInfo/Directory 클래스의 인스턴스 메소드를 이용한다.
- 다음은 기능 표이다.
  | 기능         | File            | FileInfo   | Directory         | DirectoryInfo    |
  |:------------:|-----------------|------------|-------------------|------------------|
  | 생성         | Create()        | Create()   | CreateDirectory() | Create()         |
  | 복사         | Copy()          | CopyTo()   | -                 | -                |
  | 삭제         | Delete()        | Delete()   | Delete()          | Delete()         |
  | 이동         | Move()          | MoveTo()   | Move()            | MoveTo()         |
  | 존재여부 확인 | Exists()        | Exists     | Exists()          | Exists           |
  | 속성 조회    | GetAttributes() | Attributes | GetAttributes()   | Attributes        |
  | 하위 디렉터리 조회 | -          | -          | GetDirectories()  | GetDirectories() |
  | 하위 파일 조회 | -             | -          | GetFiles()        | GetFiles()        |
- 다음은 클래스별 사용 방법이다.
  | 기능       | File       | FileInfo         | Directory      | DirectoryInfo   |
  |------------|------------|------------------|----------------|-----------------|
  | 생성         | FileStream fs = File.Create("a.dat");                   | FileInfo file = new FileInfo("a.dat"); <br>FileStream fs = file.Create();            | Directory dir = Directory.CreateDirectory("a");          | DirectoryInfo dir = new DirectoryInfo("a"); <br>dir.Create()                                       |
  | 복사         | File.Copy("a.dat", "b.dat");                            | FileInfo src = new FileInfo("a.dat"); <br>FileInfo dst = src.CopyTo("b.dat");        | -                                                        | -                                                                                                  |
  | 삭제         | File.Delete("a.dat");                                   | FileInfo file = new FileInfo("a.dat"); <br>file.Delete()                             | Directory.Delete("a");                                   | DirectoryInfo dir = new DirectoryInfo("a"); <br>dir.Delete()                                       |
  | 이동         | File.Move("a.dat", "b.dat");                            | FileInfo file = new FileInfo("a.dat"); <br>file.MoveTo("b.dat")                      | Directory.Move("a", "b");                                | DirectoryInfo dir = new DirectoryInfo("a"); <br>dir.MoveTo("b")                                    |
  | 존재여부 확인    | if( File.Exists("a.dat") )                              | FileInfo file = new FileInfo("a.dat"); <br>if( file.Exists )                         | if( Directory.Exists("a") )                              | DirectoryInfo dir = new DirectoryInfo("a"); <br>if( dir.Exists )                                   |
  | 속성 조회      | Console.WriteLine( <br>  File.GetAttributes("a.dat") ); | FileInfo file = new FileInfo("a.dat"); <br>Console.WriteLine( <br>file.Attributes ); | Console.WriteLine( <br>  Directory.GetAttributes("a") ); | DirectoryInfo dir = new DirectoryInfo("a"); <br>Console.WriteLine( dir.Attributes );               |
  | 하위 디렉터리 조회 | -                                                       | -                                                                                    | string[] dirs = <br>  Directory.GetDirectories("a");     | DirectoryInfo dir = new DirectoryInfo("a"); <br>DirectoryInfo[] dirs =  <br> dir.GetDirectories(); |
  | 하위 파일 조회   | -                                                       | -                                                                                    | string[] files = Directory.GetFiles("a");                | DirectoryInfo dir = new DirectoryInfo("a"); <br>FileInfo[] files = dir.GetFiles();                 |
  



<br>

### 18.1.1 예제 프로그램 : 디렉터리/파일 정보 조회하기

- 실습
  ```cs
  using System; using System.Linq; using System.IO;
  namespace Dir {
      class MainApp {
          static void Main( string[] args ) {
              string directory;
              if ( args.Length < 1 )  directory = ".";
              else                    directory = args[0];
  
              Console.WriteLine( $"{directory} directory Info" );
              
              // Directories
              Console.WriteLine( "- Directories :" );
  
              var directories = (from dir in Directory.GetDirectories(directory)
                                 let info = new DirectoryInfo(dir)
                                 select new
                                 {
                                     Name = info.Name,
                                     Attributes = info.Attributes,
                                 } ).ToList();
  
              foreach ( var d in directories )
                  Console.WriteLine( $"{d.Name} : {d.Attributes}" );
  
  
              // Files
              Console.WriteLine("- Files" );
  
              var files = ( from file in Directory.GetFiles( directory )
                           let info = new FileInfo( file )
                           select new
                           {
                               Name = info.Name,
                               FileSize = info.Length,
                               Attributes = info.Attributes
                           } ).ToList();
  
              foreach ( var f in files )
                  Console.WriteLine( $"{f.Name} : {f.FileSize}, {f.Attributes}" );
          }
  
  
      }
  
  }
  ```

>>>

### 실행 결과
\>"18.1.1 Dir.exe" d:\  <br><br>
d:\ directory Info
\- Directories :  
\$RECYCLE.BIN : Hidden, System, Directory  
Git_Hub_Repository : Directory  
Mirero_My_Wish : Directory  
System Volume Information : Hidden, System, Directory  
\- Files  
SW사용규정.xlsx : 20160, Archive  

>>>


<br>

### 18.1.2 예제 프로그램 : 디렉터리/파일 생성하기

- 실습
  ```cs
  using System;
  using System.Linq;
  using System.IO;
  namespace Touch {
    class MainApp {
          static void OnWrongPathType(string type) {
              Console.WriteLine($"{type} is wrong type");
              return;
          }
          static void Main(string[] args) {
              if (args.Length == 0) {
                  Console.WriteLine("Usage : Torch.exe <Path> [Type:File/Firectory]");
                  return;
              }
              string path = args[0];
              string type = "File";
              if (args.Length > 1)
                  type = args[1];
              if(File.Exists(path) || Directory.Exists(path)) {
                  if (type == "File")
                      File.SetLastWriteTime(path, DateTime.Now);
                  else if (type == "Directory")
                      Directory.SetLastWriteTime(path, DateTime.Now);
                  else {
                      OnWrongPathType(path);
                      return;
                  }
                  Console.WriteLine($"Updated {path} {type}");
              } else {
                  if (type == "File")
                      File.Create(path).Close();
                  else if (type == "Directory")
                      Directory.CreateDirectory(path);
                  else {
                      OnWrongPathType(path);
                      return;
                  }
                  Console.WriteLine($"Created {path} {type}");
              }
          }
      }
  }
  ```
>>>
### 실행 결과

\>"18.1.2 Torch.exe" a.dat <br><br>
Created a.dat File  
<br><br>
\>"18.1.2 Torch.exe" a.dat <br><br>
Updated a.dat File  
<br><br>  
  
\>"18.1.2 Torch.exe" a Directory <br><br>
Created a Directory  
<br><br>
\>"18.1.2 Torch.exe" a Directory <br><br>
Updated a Directory  
<br><br>  

>>>

<br><br>

## 18.2 파일을 읽고 쓰기 위해 알아야 할 것들

- 파일의 내용을 읽고 쓰는 방법, 파일의 입력과 출력에 대한 내용을 안다.
- 스트림(Stream)은 영어로 시내, 강 도는 도로의 차선을 뜻한다.
- 파일을 다룰 때 스트림은 "데이터가 흐르는 통로"를 뜻한다. 하드디스크와 메모리 사이에 스트림을 놓은 후 파일에 담긴 데이터를 바이트 단위로 메모리를 차례차례 옮겨온다.
- 순차 접근(Sequential Access) 방식, 처음부터 끝까지 순서대로 읽고 쓰는 것이 보통이다.
- 임의 접근(Random Access) 방식, 임의의 주소에 있는 데이터에 접근하는 것을 뜻한다.

<br>

### 18.2.1 System.IO.Stream 클래스

- Stream 클래스는 그 자체로 입력 스트림, 출력 스트림의 역할을 모두 할 수 있으며, 파일을 읽고 쓰는 방식 역시 순차 접근 방식과 임의 접근 방식 모두 지원한다.
- Stream 클래스는 추상 클래스이기에, 인스턴스를 직접 만들어 사용할 수 없고 이 클래스로부터 파생된 클래스를 이용해야 한다.
- Stream 클래스는 다음과 같은 것들이 있다.
  - FileStream : 디스크 파일에 데이터를 기록한다.
  - NetworkStream : 네트워크 피어에 데이터를 전송한다.
  - GZipStream : 데이터를 GZip(GNU ZIP)형식으로 압축한다.
  - BufferedStream : 데이터를 파일이나 네트워크에 즉시 기록하는 대신, 메모리 버퍼에 담아뒀다가 일정량이 쌓일 때마다 기록한다.
- File Stream 클래스의 인스턴스는 다음과 같이 생성한다.
  ```cs
  Stream stream1 = new FileStream("a.dat", FileMode.Create); // 새 파일 생성
  Stream stream2 = new FileStream("b.dat", FileMode.Open); // 파일 열기
  Stream stream3 = new FileStream("c.dat", FileMode.OpenOrCreate); // 파일 열기, 없음 생성 후 열기
  Stream stream4 = new FileStream("d.dat", FileMode.Truncate); // 파일 비워서 열기
  Stream stream5 = new FileStream("e.dat", FileMode.Append); // 덧 붙이기 모드로 열기

  public override void Write(
    byte[] array, // 쓸 데이터가 담긴 byte 배열
    int offset, // byte 배열 내의 시작 오프 셋
    int count // 기록할 데이터의 총 길이(단위는 바이트)
  );
  public override void WriteByte( byte value );
  public override void Read(
    byte[] array, // 읽을 데이터를 담을 byte 배열
    int offset, // byte 배열 내의 시작 오프셋
    int count // 읽을 데이터의 최대 바이트 수
  );
  public override int ReadByte();

  // 파일 읽기
  long someValue = 0x123456789ABCDEF0;

  Stream outStream = new FileStream("a.dat", FileMode.Create); // 1) 파일 스트림 생성
  byte[] wBytes = BitConverter.GetBytes( someValue ); // 2) someValue를 byte로 변환
  outStream.Write( wBytes, 0, wBytes.length ); // 3) 변환한 byte 배열을 파일 스트림을 통해 파일에 기록
  outStream.Close(); // 4) 파일 스트림 닫기

  // 파일 쓰기
  byte[] rByres = new byte[8];;

  Stream inStream = new FileStream("a.dat", FileMode.Open); // 1) 파일 스트림 생성
  inStream.Read(rByte, 0, rByte.length); // 2) rByte의 길이만큼 데이터를 일어 rByte에 저장
  long readValue = BitConverter.ToInt64(rBytes, 0); // 3) BitConverter.ToInt64(rBytes, 0);
  inStream.Close(); // 4) 파일 스트림 닫기
  ```
- 파일에 내용을 순차적으로 쓰거나 읽는 방식을 "순차 접근(Sequential Access)"라고 한다.
- 파일 내의 임의의 위치에 Position이 위치하도록 하는 "임의 접근(Random Access)" 방식이 있다.
  ```cs
  Stream outStream = new FileStream("a.dat", FileMode.Create); 
  //...//
  outStream.Seek(5, SeekOrigin.Current); // 현재 위치에서 5바이트 뒤로 이동
  outStream.WriteByte(0x004);
  ```

<br><br>

## 18.3 실수를 줄여주는 using 선언

- 파일을 열어서 실컷 이용한 다음 자원을 해제하지 않는 것과 같은 실수들이 잇다.
- 이와 같은 파일 닫기는 using 선언을 통해 다음과 같이 수정할 수 있다.
  ```cs
  {
    using Stream outStream = new FileStream("a.dat", FileMode.Create);

    byte[] wBytes = BitConverter.GetBytes(simeValue);

    outStream.Write(wBytes, 0, wBytes.length);
  } // using 선언을 통해 선언된 객체는 코드블록이 끝나면서 outStream.Dispose() 호출

  //또 다른 using 선언 방식
  using ( Stream outStream = new FileStream("a.dat", FileMode.Create) ) {
    byte[] wBytes = BitConverter.GetBytes(simeValue);

    outStream.Write(wBytes, 0, wBytes.length);
  }
  ```

<br><br>

## 18.4 이진 데이터 처리를 위한 BinaryWriter/BinaryReader

- BinaryWriter는 스트림에 이진 데이터(Binary Data)를 기록하기 위한 목적이고, BinaryReader는 스트림에 이진 데이터를 읽어 들이기 위한 목적으로 만들어졌다.
- BinaryWriter와 FileStream을 함께 사용한다면, BinaryWriter의 이진 데이터 쓰기 기능을 파일 기록
  ```cs
  BinaryWriter bw = new BinaryWriter( new FileStream("a.dat", FileMode.Create) );

  bw.Write(32); // 알아서 비트 단위로 저장해준다.
  bw.Write("Good Morning!");
  bw.Write(3.14); // 모든 데이터 형식 가능
  bw.Close():

  BinaryReader br = new BinaryReader( new FileStream("a.dat", FileMode.Open) );

  int a = br.ReadInt32();
  string b = br.ReadString();
  double c = br.ReadDouble(); // 읽을 형식의 데이터 형식 별로 제공
  br.Close();
  ```

<br><br>

## 18.5 텍스트 파일 처리를 위한 StreamWriter/StreamReader

- .NET에서 텍스트 파일을 쓰고 읽을 수 있도록 StreamWriter/StreamReader를 제공한다.
  ```cs
  StreamWriter sw = new StreamWriter( new FileStream("a.dat", FileMode.Create) );

  sw.Write(32);
  sw.WriteLine("Good Morning!");
  sw.WriteLine(3.14);
  sw.Close();

  StreamReader sr = new StreamReader( new FileStream("a.dat", FileMode.Open) );
  while ( sr.EndOfStream == false ) {
    Console.WriteLine(sr.ReadLine());
  }
  sr.Close();
  ```


<br><br>

## 18.6 객체 직렬화 하기

- 복합 데이터 형식을 기록하고 읽으려면 그 형식이 가진 필드의 값을 저장할 숭서를 정한 후, 이 순서대로 저장하고 읽는 코드를 작성해야 한다. 이를 한번에 저장하는 방법이다.
- [Serializable] 애트리뷰트를 클래스 선언부 앞에 붙여주면 이 클래스는 메모리나 영구 저장 장치에 저장할 수 있는 형식이 된다.
  ```cs
  [Serializable]
  class MyClass {
    //...//
  }

  Stream ws = new FileStream("a.dat", FileMode.Create);
  BinaryFormatter serializer = new BinaryFormatter();

  MyClass obj = new MyClass(); // obj 필드에 값 저장

  serializer.Serialize(ws, obj); // 직렬화
  ws.Close();  

  Stream rs = new FileStream("a.dat", FileMode.Open);
  BinaryFormatter deserializer = new BinaryFormatter();

  MyClass obj2 = (MyClass)deserializer.Deserialize(rs); // 역직렬화
  rs.Close();
  ```
- 상태를 저장하고 싶지 않은 필드가 있으면, [NonSerialized] 애트리뷰트를 수식해준다.
  ```cs
  [Serializable]
  calss MyClass {
    public int myField1;
    public int myField2;

    [NonSerialized]
    public int myField3; // myField3을 제외한 나머지 필드만 직렬화 된다.

    public int myField4;
  }
  ```
- List를 비롯한 컬렉션들도 직렬화를 지원한다.
- 실습
  ```cs
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Runtime.Serialization.Formatters.Binary;
  
  namespace SerializingCollection
  {
      [Serializable]
      class NameCard
      {
          public NameCard(string Name, string Phone, int Age)
          {
              this.Name = Name;
              this.Phone = Phone;
              this.Age = Age;
          }
  
          public string Name;
          public string Phone;
          public int Age;
      }
  
      class Mainapp
      {
          static void Main(string[] args)
          {
              using (Stream ws = new FileStream("a.dat", FileMode.Create))
              {
                  BinaryFormatter serializer = new BinaryFormatter();
  
                  List<NameCard> list = new List<NameCard>();
                  list.Add(new NameCard("박상현", "010-123-4567", 33));
                  list.Add(new NameCard("김연아", "010-123-4567", 22));
                  list.Add(new NameCard("장미란", "010-123-4567", 26));
  
                  serializer.Serialize(ws, list);
  
              }
  
              using Stream rs = new FileStream("a.dat", FileMode.Open);
              BinaryFormatter deserializer = new BinaryFormatter();
  
              List<NameCard> list2;
              list2 = (List<NameCard>)deserializer.Deserialize(rs);
  
              foreach (NameCard nc in list2)
              {
                  Console.WriteLine($"Name : {nc.Name}, Phone : {nc.Phone}, Age : {nc.Age}");
              }
          }
      }
  }
  ```

>>>
### 실행 결과
Name : 박상현, Phone : 010-123-4567, Age : 33  
Name : 김연아, Phone : 010-123-4567, Age : 22  
Name : 장미란, Phone : 010-123-4567, Age : 26  
>>>
