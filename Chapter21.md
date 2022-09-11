# 21. 네트워크 프로그래밍

## 21.1 네트워크 프로그래밍에 앞서 알아둬야 할 기초

- 통신기능을 수행하는 C#에 대해서 알아둔다.

<br>

### 21.1.1 인터넷의 유래

- 네트워크(NetWork)는 그물(Net)에서 파생된 단어로, 어떤 물건이나 사람 등이 상호 연결된 체계를 말한다.
- 1957년 소련의 스푸트니크 위성에 의해 미국은 각 분야에 수많은 변화가 일어났다.
- 1958년 DARPA(Defense Advanced Research Project Agency)의 설립이 있었다.
  - DARPANET이라고 불리던 이 네트워크는 더 많은 대학과 연구 기관으로, 세계의 연구 기관과 민간으로 연결되기 시작해서 1980년대 말에는 인터넷이라는 국제 통신망을 형성하게 되었다.

<br>

### 21.1.2 TCP/IP 스택

- 컴퓨터끼리 네트워크에서 데이터를 주고받기 위해서는 그 네트워크에서 통용되는 "프로토콜(Protocol)"을 따라야 한다. (통신규약 을 말한다.)
- TCP/IP는 표준 프로토콜로, 인터넷에서 데이터를 주고받는 데 필요한 일련의 프로토콜 모음(Suite)이다.
- TCP/IP는 애플리케니션 계층 - 전송 계층 - 인터넷 계층 - 링크 계층으로 구성되어 있다.

<br>

### 링크 계층

- 컴퓨터가 네트워크에 전화선의 모뎀으로 연결되어 있든, LAN(Local Area Network)에 이더넷 케이블로 연결되어 있든, 혹은 WiFi에 연결되어 있든간에 TCP/IP는 네트워크의 물리적인 구성으로부터 독립적인 프로토콜이다.
- 링크 계층에서 네트워크의 물리적인 연결매체를 통해 패킷을 주고 받는 작업을 담당해주기 때문에 가능한 것이다.

<br>

### 인터넷 계층

- 인터넷 계층은 패킷을 수신해야 할 상대의 주소를 지정하고, 나가는 패킷에 대해서는 적절한 크기로 분할하며, 들어오는 패킷에 대해서는 재조립을 수행한다.
- 이 계층에서 사용하는 규약이 바로 인터넷 프로토콜(Internet Protocol), 즉 IP이다.

<br>

### 전송 계층

- 전송 계층(Transport Layer)에는 이름 그대로 패킷의 "운송"을 담당하는 프로토콜들이 정의되어 있다.
- 전송 제어 프로토콜(Transmission Control Protocol : TCP)은 송신 측과 수신 측 간의 연결성을 제공하며, 신뢰할 수 있는 패킷 전송 서비스를 제공한다.
- 여러개의 패킷을 송신하는 경우 패킷 사이의 순서를 보장하며, 패킷이 유실되기라도 하면 재전송을 해주기까지 한다.
- 웹문서를 전달하는 기능을 하는 HTTP를 비롯한 수많은 응용 프로토콜들이 바로 이 TCP와 IP 프로토콜 위에서 동작한다.
- 단점 : TCP는 IP가 제공하지 않는 연결성화 신뢰성을 제공하느라 성능에서 손실을 본다.
- 이를 위한 대안으로 전송계층에서는 UDP(User Datagran Protocol)라는 프로토콜이 정의되어 있다.

<br>

### 애플리케이션 계층

- 각 응용 프로그램 나름의 프로토콜들이 정의되는 곳이다.
- 웹문서를 주고 받기 위한 HTTP(Htper Text Transfer Protocol), 파일 교환을 위한 FTP(File Transfer Protocol), 네트워크 관리를 위한 SNMP(Simple Network Management Protocol) 등이 애플리케이션 계층에서 정의된 프로토콜의 대표적인 예이다.

<br>

### 21.1.3 TCP/IP의 주소 체계 : IP 주소

- 인터넷에서 사용하는 주소를 일컬어 "IP주소 (IP Adress)"라고 한다.
- 기존 IP주소는 부호가 없는 8비트 정수 4개로 구성된다. 이러한 주소 체계를 IPv4라고 하는데 40억개가 넘는 IP주소를 만들 수 있다.  
  `211.56.101.37`
- IPv6는 주소의 길이가 128비트에 이르며 사실상 무한에 가까운 IP 주소를 할당할 수 있다.  
  `3FFE:FFFF:7654:FEDA:1245:BA98:3210:4562`  

<br>

### 21.1.4 포트

- 컴퓨터도 네트워크 패킷이 드나들려면 "주소"뿐만 아니라 출입문이 필요하다.
- 이러한 출입문을 포트(Port)라고 부른다.
- 포트는 부호가 없는 16비트 정수로 0~65535 사이의 값을 이용한다.
- 예를 들어 웹 브라우저를 사용하는 HTTP는 80번 포트를 사용하고, FTP는 21번, Telnet은 23번을 사용한다.
  - HTTP : 80
  - HTTPS : 443
  - FTP : 21
  - Telnet : 23
  - SMTP : 25
  - IRC : 194
  - IIOP : 535
- 잘 알려진 포트 번호는 1~1023 사이의 수를 사용하므로, 새로운 어플리케이션 프로토콜을 정의할 때는 이 범위를 피해서 정하는 것이 좋다.

<br>

### 21.1.5 TCP/IP의 동작 과정

- TCP/IP는 서버/클라이언트 방식으로 동작한다.
- TCP/IP 통신을 위해서는 먼저 서버가 서비스를 시작해야 한다. 클라이언트가 접속할 수 있도록 준비를 한다.
- 다음은 TCP/IP의 서버/클라이언트의 동작 과정을 나타낸다.
  ```mermaid
  sequenceDiagram
      participant A as 서버
      participant J as 클라이언트
      A->>A: 서버 시작
      J->>A: 연락 수락 <-- 연결 요청
      J->>A: 데이터 수신 <-- 데이터 전송
      J->>A: 데이터 수신 <-- 데이터 전송
      A->>J: 데이터 전송 --> 데이터 수신
      J->>A: <-- 연결 종료 (서버 측에서도 연결 종료 가능)
      A->>A: 서버 종료
  ```

<br><br>

## 21.2 TcpListener와 TcpClient

- TcpListener와 TcpClient는 .Net이 TCP/IP 통신을 위해 제공하는 클래스이다.
- System.Net.Sockets 네임스페이스에서는 보다 다양한 옵션과 메소드를 제공하는 Socket 클래스도 있지만, 사용이 복잡하다는 단점이 있기 때문에 TcpListener와 TcpClient 클래스를 이용한 TCP/IP 프로그래밍을 다룬다.
- TcpListener 클래스는 서버 애플리케이션에서 사용하며, 클라이언트의 연결요청을 기다리는 역할을 한다.
- TcpClient 클래스는 서버 애플리케이션과 클라이언트 애플리케이션 양쪽에서 사용된다.
- 서버와 클라이언트가 갖고 있는 TcpClient는 GetStream()이라는 메소드를 갖고 있어, 양쪽 응용프로그램은 이 메소드가 반환하는 NetworkStream 객체를 통해 데이터를 주고 받는다.
- 다음은 TCP/IP 통신을 수행하기 위해 호출되는 TcpListener와 TcpClient, NetworkStream 클래스들의 메소드 흐름을 나타낸다.
  ```mermaid
  sequenceDiagram
      participant A as 서버
      participant J as 클라이언트
      A->>A: TcpListener.Start()
      J->>A: TcpListener.AcceptTcpClient() <-- TcpClient.Connect()
      J->>A: NetworkStream.Read() <-- NetworkStream.Write() 
      J->>A: NetworkStream.Read() <-- NetworkStream.Write() 
      A->>J: NetworkStream.Write() --> NetworkStream.Read() 
      J->>A: NetworkStream.Close(), TcpClient.Close() <-- NetworkStream.Close(), TcpClient.Close()
      A->>A: TcpListener.Stop()
  ```
- 서버의 TcpListener를 시작하는 코드는 다음과 같다.
  ```cs
  // IPEndPoint는 IP 통신에 필요한 IP 주소와 포트(출입구)를 나타낸다.
  IPEndPoint localAddress = new IPEndPoint(IPAddress.Parse("192.168.100.17"), 5425);

  TcpListener server = new TcpListener(localAddress);

  server.start(); // server 객체는 클라이언트가 TcpClient.Connect()를 호출하여 연결 요청해오기를 기다리기 시작한다.
  ```
- 클라이언트에서 TcpClient 객체를 생성하고 연결을 요청하는 코드는 다음과 같다.
  ```cs
  // 포트를 0으로 지정하면 OS에서 임의의 번호로 포트를 할당해준다.
  IPEndPoint clientAdress = new IPEndPoint(IPAddress.Parse("192.168.100.18"), 0);

  TcpClient client = new TcpClient(clientAdress);

  IPEndPoint serverAdrss = new IPEndPoint(IPAddress.Parse("192.168.100.17"), 5425);

  client.Connect(serverAdrss); // 서버가 수신 대기하고 있는 IP 주소와 포트 번호를 향해 연결 요청을 수행한다.
  ```
- 다시 서버에서는 연결요청이 오면 메소드는 클라이언트와 통신할 수 있도록 TcpClient 형식의 객체를 반환한다.
  ```cs
  TcpClient client = server.AcceptTcpClient();
  ```
- TcpClient 객체가 NetworkStream 객체를 반환하고, NetworkStream 객체를 이용하여 데이터를 읽고 쓰는 예제이다.
  ```cs
  // TCPClient를 통해 NetworkStream 객체를 얻는다.
  NetworkStream stream = client.GetStream();

  int length;
  string data = null;
  byte[] bytes = new byte[256];

  // 상대방과 연결이 끊어지기 전까지는 계속된다.
  while ((length = stream.Read(bytes, 0, bytes.Length)) != 0) { 
    data = Encoding.Default.GetString(bytes, 0, length);
    Console.WriteLine(String.Format("수신: {0}", data));

    byte[] msg = Encoding.Default.GetBytes(data);

    stream.Write(msg, 0, msg.Length);
    Console.WriteLine(String.Format("송신 : {0}", data));
  }
  ```

### 실습

- 서버와 클라이언트 프로젝트를 따로 만들어 통신한다.
- 서버
  <details>
  <summary>MainApp.cs</summary>

  ```cs
  using System;
  using System.Diagnostics;
  using System.Net;
  using System.Net.Sockets;
  using System.Text;
  
  namespace EchoServer
  {
      class MainApp
      {
          static void Main(string[] args)
          {
              if (args.Length < 1)
              {
                  Console.WriteLine("사용법 : {0} <Bind IP>", Process.GetCurrentProcess().ProcessName);
                  return;
              }
  
              string bindIp = args[0];
              const int bindPort = 5425;
              TcpListener server = null;
  
              try
              {
                  IPEndPoint localAddress = new IPEndPoint(IPAddress.Parse(bindIp), bindPort);
  
                  server = new TcpListener(localAddress);
  
                  server.Start();
  
                  Console.WriteLine("메아리 서버 시작...");
  
                  while(true)
                  {
                      TcpClient client = server.AcceptTcpClient();
                      Console.WriteLine("클라이언트 접속 : {0} ", ((IPEndPoint)client.Client.RemoteEndPoint).ToString());
                      
                      NetworkStream stream = client.GetStream();
  
                      int length;
                      string data = null;
                      byte[] bytes = new byte[256];
  
                      while((length = stream.Read(bytes, 0, bytes.Length)) != 0)
                      {
                          data = Encoding.Default.GetString(bytes, 0, length);
                          Console.WriteLine(String.Format("수신: {0}", data));
  
                          byte[] msg = Encoding.Default.GetBytes(data);
  
                          stream.Write(msg, 0, msg.Length);
                          Console.WriteLine(String.Format("송신 : {0}", data));
                      }
  
                      stream.Close();
                      client.Close();
  
                  }
              }
              catch (SocketException e)
              {
                  Console.WriteLine(e);
              }
              finally
              {
                  server.Stop();
              }
  
              Console.WriteLine("서버를 종료합니다.");
  
          }
      }
  }
  ```

  </details>

- 클라이언트
  <details>
  <summary>MainApp.cs</summary>

  ```cs
  using System;
  using System.Diagnostics;
  using System.Net;
  using System.Net.Http.Headers;
  using System.Net.Sockets;
  using System.Text;
  
  namespace EchoClient
  {
      class MainApp
      {
          static void Main(string[] args)
          {
              if (args.Length < 4)
              {
                  Console.WriteLine("사용법 : {0} <Bind IP> <Bind Port> <Server IP> <Message>",   Process.GetCurrentProcess().ProcessName);
                  return;
              }
  
              string bindIp = args[0];
              int bindPort = Convert.ToInt32(args[1]);
              string serverIp = args[2];
              const int serverPort = 5425;
              string message = args[3];
  
              try
              {
                  IPEndPoint clientAddress = new IPEndPoint(IPAddress.Parse(bindIp), bindPort);
                  IPEndPoint serverAddress = new IPEndPoint(IPAddress.Parse(serverIp), serverPort);
  
                  Console.WriteLine("클라이언트 : {0}, 서버 : {1}",
                      clientAddress.ToString(), serverAddress.ToString());
  
                  TcpClient client = new TcpClient(clientAddress);
  
                  client.Connect(serverAddress);
  
                  byte[] data = System.Text.Encoding.Default.GetBytes(message);
  
                  NetworkStream stream = client.GetStream();
  
                  stream.Write(data, 0, data.Length);
  
                  Console.WriteLine("송신 : {0}", message);
  
                  data = new byte[256];
  
                  string responseData = "";
  
                  int bytes = stream.Read(data, 0, data.Length);
                  responseData = Encoding.Default.GetString(data, 0, bytes);
                  Console.WriteLine("수신 : {0}", responseData);
                  
                  stream.Close();
                  client.Close();
  
              }
              catch (SocketException e)
              {
                  Console.WriteLine(e);
              }
  
              Console.WriteLine("클라이언트를 종료합니다.");
  
          }
      }
  }
  ```
  </details>

- 결과  
  <img src="/uploads/aa1a761add0ff7d2547ebab1ae3392e0/image.png" width="70%">

<br><br>

## 21.3 흐르는 패킷

- TCP/IP는 편지를 주고받는 방식이 아니다.
- TCP 통신 애플리케이션도 댐과 같은 역할을 하는 버퍼(Buffer)가 있다.
- 송 수신 과정에서 이 버퍼를 반드시 거친다.

<br><br>

## 21.4 프로토콜 설계와 네트워크 애플리케이션 프로그래밍 예제

- TCP 네트워크 프로그래밍을 복잡하다.
- FTP와 같은 큰 프로토콜 보다는 작은 간단한 프로토콜을 직접 만들어본다.

<br>

### 21.4.1 파일 업로드 프로토콜

- 우리의 파일 업로드 프로토콜 (FUD : File Upload Protocol은 헤더와 바디의 두 부분으로 나뉜다.
- 바디에는 실제로 전달하고자 하는 데이터를 담고, 헤도는 본문의 길이를 비롯해 메시지의 속성 몇가지를 담는다.
- 바디의 길이는 달라지지만, 헤더의 길이는 언제나 16바이트로 일정하다.
- 16바이트를 가장 먼저 확인해서 메시지의 속성을 확인하고, 그 다음에 바디의 길이만큼 읽어서 하의 메시지의 끝을 끊어내야 한다.
- 헤더(16바이트, MSGID-MSGTYPE-BODYLEN-(FRAGMENTED-LASTMSG-SEQ)) - 바디
  - MSGID (4 바이트) : 메시지 식별번호
  - MSGTYPE (4 바이트) : 메시지 종류
    - 0x01 : 파일 전송 요청
    - 0x02 : 파일 전송 요청 응답
    - 0x03 : 파일 전송 데이터
    - 0x04 : 파일 수신 결과
  - BODYLEN (4 바이트) : 메시지 본문의 길이 (단위 : 바이트)
  - FRAGMENTED (1 바이트) : 메시지 분할 여부
    - 0x0 : 미분할
    - 0x1 : 분할
  - LASTMSG (1 바이트) : 분할된 메시지가 마지막인지 여부
    - 0x0 : 마지막 아님
    - 0x1 : 마지막
  - SEQ (2 바이트) : 메시지의 파편 번호
- MSGTYPE의 종류에 따라 바디가 달라진다.
  - 0x01 파일 전송 요청의 경우
    - FILESIZE (8 바이트) : 전송할 파일 크기
    - FILENAME (BODYLEN - FILESIZE (8byte)) : 전송할 파일의 이름
  - 0x02 파일 전송 요청에 대한 응답
    - MSGID (4 바이트) : 파일 전송 요청 메시지(0x01)의 메시지 식별 번호
    - RESPONSE (1 바이트) : 파일 전송 승인 여부
      - 0x0 : 거절
      - 0x1 : 승인
  - 0x03 파일 전송 데이터의 경우
    - DATA (헤더의 BODYLEN) : 파일 내용
  - 0x04 파일 수신 결과
    - MSGID (4 바이트) : 파일 전송 데이터(0x03)의 식별 번호
    - RESULT (1 바이트) : 파일 전송 성공 여부
      - 0x0 : 실패
      - 0x1 : 성공
  ```mermaid
  sequenceDiagram
      participant A as 서버
      participant J as 클라이언트
      J->>A: 파일 전송 요청(0x01)
      A->>J: 파일 전송 요청(0x02)
      J->>A: 파일 데이터 (0x03)
      J->>A: 파일 데이터 (0x03)
      J->>A: 파일 데이터 (0x03)
      A->>J: 파일 전송 결과 (0x04)
  ```

<br>

### 21.4.2 파일 업로드 서버와 클라이언트 구현하기

- 다음 순서대로 구현한다.
  - 서버/클라이언트 공용 클래스 라이브러리 구현
  - 서버 구현
  - 클라이언트 구현

<br>

### 21.4.3 서버/클라이언트가 같이 사용할 클래스 라이브러리 만들기

- 서버와 클라이언트는 모드 FUP 프로토콜을 따라야 한다.
- 다음 절차에 따라 FUP 프로토콜 클래스 라이브러리를 만든다.
1. Visual Studio > 새 프로젝트 > 클래스 라이브러리(.NET Core) > 프로젝트 이름을 "FUP"로 입력 < 만들기
2. "Class1.cs" 파일을 제거한다.
3. FUP 프로젝트를 선택한 다음 <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>A</kbd> 키를 눌러 [새 항목 추가] 대화상자를 띄운다. > 클래스 선태후 "Message.cs"라고 이름을 변경 > 추가
4. Message.cs에 다음 코드를 추가한다.
   <details>
   <summary>📝 Mseeage.cs</summary>  

   ```cs
   namespace FUP
   {
       public class CONSTANTS
       {
           public const uint REQ_FILE_SEND = 0x01;
           public const uint REP_FILE_SEND = 0x02;
           public const uint FILE_SEND_DATA = 0x03;
           public const uint FILE_SEND_RES = 0x04; // 메시지 타입 상수 정의 (MSGTYPE)
   
           public const byte NOT_FRAGMENTED = 0x00;
           public const byte FRAGMENTED = 0x01;
   
           public const byte NOT_LASTMSG = 0x00;
           public const byte LASTMSG = 0x01;
   
           public const byte ACCEPTED = 0x01;
           public const byte DENIED = 0x00;
   
           public const byte FAIL = 0x00;
           public const byte SUCCESS = 0x01;
       }
   
       public interface ISerializable // 메시지 헤더 바디는 모두 이 인터페이스에서 상속
       {
           byte[] GetBytes();
           int GetSize();
   
       }
   
       public class Mesage : ISerializable // FUP의 메시지를 나타내는 클래스, 헤더와 바디로 구성
       {
           public Header Header { get; set; }
           public ISerializable Body { get; set; }
   
           public byte[] GetBytes()
           {
               byte[] bytes = new byte[GetSize()];
   
               Header.GetBytes().CopyTo(bytes, 0);
               Body.GetBytes().CopyTo(bytes, Header.GetSize());
   
               return bytes;
           }
   
           public int GetSize()
           {
               return Header.GetSize() + Body.GetSize();
           }
       }
   }
   ```

   </details>

5. 3)와 같이 "Header.cs"를 추가하고 다음 코드를 입력한다.
   <details>
   <summary>📝 Header.cs</summary>

   ```cs
   namespace FUP
   {
       internal class Header : ISerializable
       {
           public uint MSGID { get; set; }
           public uint MSGTYPE { get; set; }
           public uint BODYLEN { get; set; }
           public byte FRAGMENTED { get; set; }
           public byte LASTMSG { get; set; }
           public ushort SEQ { get; set; }
   
           public Header() { }
           public Header(byte[] bytes)
           {
               MSGID = BitConverter.ToUInt32(bytes, 0);
               MSGTYPE = BitConverter.ToUInt32(bytes, 4);
               BODYLEN = BitConverter.ToUInt32(bytes, 8);
               FRAGMENTED = bytes[12];
               LASTMSG = bytes[13];
               SEQ = BitConverter.ToUInt16(bytes, 14);
           }
   
           public byte[] GetBytes()
           {
               byte[] bytes = new byte[16];
   
               byte[] temp = BitConverter.GetBytes(MSGID);
               Array.Copy(temp, 0, bytes, 0, temp.Length);
   
               temp = BitConverter.GetBytes(MSGTYPE);
               Array.Copy(temp, 0, bytes, 4, temp.Length);
   
               temp = BitConverter.GetBytes(BODYLEN);
               Array.Copy(temp, 0, bytes, 8, temp.Length);
   
               bytes[12] = FRAGMENTED;
               bytes[13] = LASTMSG;
   
               temp = BitConverter.GetBytes(SEQ);
               Array.Copy(temp, 0, bytes, 14, temp.Length);
   
               return bytes;
           }
   
           public int GetSize()
           {
               return 16;
           }
       }
   }
   ```

   </details>
6. 마찬가지로 Body.cs를 추가하고 코드를 입력한다.
   <details>
   <summary>📝 Body.cs</summary>

   ```cs
   namespace FUP
   {
       public class BodyRequest : ISerializable
       {
           public long FILESIZE;
           public byte[] FILENAME;
   
           public BodyRequest() { }
           public BodyRequest(byte[] bytes)
           {
               FILESIZE = BitConverter.ToInt64(bytes, 0);
               FILENAME = new byte[bytes.Length - sizeof(long)];
               Array.Copy(bytes, sizeof(long), FILENAME, 0, FILENAME.Length);
           }
           
           public byte[] GetBytes()
           {
               byte[] bytes = new byte[GetSize()];
               byte[] temp = BitConverter.GetBytes(FILESIZE);
               Array.Copy(temp, 0, bytes, 0, temp.Length);
               Array.Copy(FILENAME, 0, bytes, temp.Length, FILENAME.Length);
   
               return bytes;
           }
   
           public int GetSize()
           {
               return sizeof(long) + FILENAME.Length;
           }
   
       }
   
       public class BodyResponse : ISerializable
       {
           public uint MSGID;
           public byte RESPONSE;
           public BodyResponse() { }
           public BodyResponse(byte[] bytes)
           {
               MSGID = BitConverter.ToUInt32(bytes, 0);
               RESPONSE = bytes[4];
           }
   
           public byte[] GetBytes()
           {
               byte[] bytes = new byte[GetSize()];
               byte[] temp = BitConverter.GetBytes(MSGID);
               Array.Copy(temp, 0, bytes, 0, temp.Length);
               bytes[temp.Length] = RESPONSE;
   
               return bytes;
   
           }
   
           public int GetSize()
           {
               return sizeof(uint) + sizeof(byte);
           }
   
       }
   
       public class BodyData : ISerializable
       {
           public byte[] DATA;
   
           public BodyData(byte[] bytes)
           {
               DATA = new byte[bytes.Length];
               bytes.CopyTo(DATA, 0);
           }
   
           public byte[] GetBytes()
           {
               return DATA;
           }
   
           public int GetSize()
           {
               return DATA.Length;
           }
       }
   
       public class BodyResult : ISerializable
       {
           public uint MSGID;
           public byte RESULT;
   
           public BodyResult() { }
           public BodyResult(byte[] bytes)
           {
               MSGID = BitConverter.ToUInt32(bytes, 0);
               RESULT = bytes[4];
           }
           public byte[] GetBytes()
           {
               byte[] bytes = new byte[GetSize()];
               byte[] temp = BitConverter.GetBytes(MSGID);
               Array.Copy(temp, 0, bytes, 0, temp.Length);
               bytes[temp.Length] = RESULT;
   
               return bytes;
           }
           public int GetSize()
           {
               return sizeof (uint) + sizeof(byte);
           }
       }
   
   }

   ```

   </details>
7. 마찬가지로 "MessageUtil.cs"를 추가한다.
   <details>
   <summary>📝 MessageUtil.cs</summary>

   ```cs
   using System;
   using System.IO;
   
   namespace FUP
   {
       public class MessageUtil
       {
        
           public static void Send(Stream writer, Message msg)
           {
               writer.Write(msg.GetBytes(), 0, msg.GetSize());
           }
   
           public static Message Receive(Stream reader)
           {
               int totalRecv = 0;
               int sizeToRead = 16;
               byte[] hBuffer = new byte[sizeToRead];
   
               while (sizeToRead > 0)
               {
                   byte[] buffer = new byte[sizeToRead];
                   int recv = reader.Read(buffer, 0, sizeToRead);
                   if (recv == 0)
                       return null;
   
                   buffer.CopyTo(hBuffer, totalRecv);
                   totalRecv += recv;
                   sizeToRead -= recv;
               }
   
               Header header = new Header(hBuffer);
   
               totalRecv = 0;
               byte[] bBuffer = new byte[header.BODYLEN];
               sizeToRead = (int)header.BODYLEN;
   
               while (sizeToRead > 0)
               {
                   byte[] buffer = new byte[sizeToRead];
                   int recv = reader.Read(buffer, 0, sizeToRead);
                   if (recv == 0)
                       return null;
   
                   buffer.CopyTo(bBuffer, totalRecv);
                   totalRecv += recv;
                   sizeToRead -= recv;
               }
   
               ISerializable body = null;
               switch (header.MSGTYPE)
               {
                   case CONSTANTS.REQ_FILE_SEND:
                       body = new BodyRequest(bBuffer);
                       break;
                   case CONSTANTS.REP_FILE_SEND:
                       body = new BodyResponse(bBuffer);
                       break;
                   case CONSTANTS.FILE_SEND_DATA:
                       body = new BodyData(bBuffer);
                       break;
                   case CONSTANTS.FILE_SEND_RES:
                       body = new BodyResult(bBuffer);
                       break;
                   default:
                       throw new Exception(
                           String.Format("Unknown MSGTYPE : {0}", header.MSGTYPE));
               }
   
               return new Message() { Header = header, Body = body };
   
           }
   
       }
   }
   ```

   </details>
8. 솔루션 탐색기 > 'FUP 프로젝트' 항목 위 마우스 오른쪽 > '빌드' 를 통해 클래스 라이브러리를 빌드한다.
   - 빌드를 진행하면 bin/Debug 나 bin/Release 폴더에 FUP.dll이 생성된 것을 확인할 수 있다.
<br>

### 21.4.4 파일 업로드 서버 구현하기

1. Visual Studio > 새 프로젝트 만들기 > '콘솔 앱(.NET Core)' > 프로젝트 이름 : FileReceiver > 만들기
2. FUP.dll을 프로젝트 참조에 추가시킨다. 솔루션 탐색기 > 'FileReceiver' 아래 '종속성' 선택 > 마우스 오른쪽 > 'COM 참조 추가' > [참조 관리자] > 찾아보기 > FUP.dll 선택 > 확인
3. Program.cs의 이름을 MainApp.cs로 바꾸고 다음 코드를 작성한다.
   <details>
   <summary>📝 MainApp.cs</summary>

   ```cs
   using System;
   using System.Diagnostics;
   using System.IO;
   using System.Net;
   using System.Net.Sockets;
   using System.Text;
   using System.Xml;
   using FUP;
   
   namespace FileReceiver
   {
       class MainApp
       {
           static void Main(string[] args)
           {
               if (args.Length < 1)
               {
                   Console.WriteLine("사용법 : {0} <Directory>", Process.GetCurrentProcess().   ProcessName);
                   return;
               }
   
               uint msgId = 0;
   
               string dir = args[0];
               if ( Directory.Exists(dir) == false )
                   Directory.CreateDirectory(dir);
   
               const int bindPort = 5425; //서버 포트는 5425
               TcpListener server = null;
               try
               {
                   IPEndPoint localAddress = new IPEndPoint(0, bindPort);
                   server = new TcpListener(localAddress);
                   server.Start();
   
                   Console.WriteLine("파일 업로드 서버 시작...");
   
                   while (true)
                   {
                       TcpClient client = server.AcceptTcpClient();
                       Console.WriteLine("클라이언트 접속 : {0}", ((IPEndPoint)client.Client.   RemoteEndPoint).ToString());
   
                       NetworkStream stream = client.GetStream();
   
                       Message reqMsg = MessageUtil.Receive(stream);
   
                       if (reqMsg.Header.MSGTYPE != CONSTANTS.REQ_FILE_SEND)
                       {
                           stream.Close();
                           client.Close();
                           continue;
                       }
   
                       BodyRequest reqBody = (BodyRequest)reqMsg.Body;
   
                       Console.WriteLine("파일 업로드 요청이 왔습니다. 수락하시겠습니까? yes/no");
                       string answer = Console.ReadLine();
   
                       Message rspMsg = new Message();
                       rspMsg.Body = new BodyResponse()
                       {
                           MSGID = reqMsg.Header.MSGID,
                           RESPONSE = CONSTANTS.ACCEPTED
                       };
   
                       rspMsg.Header = new Header()
                       {
                           MSGID = msgId++,
                           MSGTYPE = CONSTANTS.REP_FILE_SEND,
                           BODYLEN = (uint)rspMsg.Body.GetSize(),
                           FRAGMENTED = CONSTANTS.NOT_FRAGMENTED,
                           LASTMSG = CONSTANTS.LASTMSG,
                           SEQ = 0
                       };
   
                       if (answer != "yes") 
                       {
                           rspMsg.Body = new BodyResponse()  // 사용자가 yes 라고 입력 x >    클라이언트에게 거부 응답
                           {
                               MSGID = reqMsg.Header.MSGID,
                               RESPONSE = CONSTANTS.DENIED
                           };
   
                           MessageUtil.Send(stream, rspMsg);
                           stream.Close();
                           client.Close();
   
                           continue;
                       }
                       else
                           MessageUtil.Send(stream, rspMsg);  // 사용자가 yes 라고 입력 o >    클라이언트에게 승낙 응답
   
   
                       Console.WriteLine("파일 전송을 시작합니다...");
   
                       long fileSize = reqBody.FILESIZE;
                       string fileName = Encoding.Default.GetString(reqBody.FILENAME);
                       FileStream file = new FileStream(dir + "\\" + fileName, FileMode.Create);
   
                       uint? dataMsgId = null;
                       ushort prevSeq = 0;
                       while ((reqMsg = MessageUtil.Receive(stream)) != null)
                       {
                           Console.Write("#");
                           if (reqMsg.Header.MSGTYPE != CONSTANTS.FILE_SEND_DATA)
                               break;
   
                           if (dataMsgId == null)
                               dataMsgId = reqMsg.Header.MSGID;
                           else
                           {
                               if (dataMsgId != reqMsg.Header.MSGID)
                                   break;
                           }
   
                           if (prevSeq++ != reqMsg.Header.SEQ) // 메시지 전송 순서가 어긋나면 전송    중단
                           {
                               Console.WriteLine("{0}. {1}", prevSeq, reqMsg.Header.SEQ);
                               break;
                           }
   
                           // 전송받은 스트림을 서버에서 생성한 파일에 기록
                           file.Write(reqMsg.Body.GetBytes(), 0, reqMsg.Body.GetSize());
   
                           if (reqMsg.Header.FRAGMENTED == CONSTANTS.NOT_FRAGMENTED)
                               break; // 분할 메시지가 아니라면 반복을 한번만 하고 빠져나온다.
                           if (reqMsg.Header.LASTMSG == CONSTANTS.LASTMSG)
                               break; // 마지막 메시지면 반복문을 빠져 나온다.
                       }
   
                       long recvFileSize = file.Length;
                       file.Close();
   
                       Console.WriteLine();
                       Console.WriteLine("수신 버퍼 크기 : {0} bytes", recvFileSize);
   
                       Message rstMsg = new Message();
                       rstMsg.Body = new BodyResult()
                       {
                           MSGID = reqMsg.Header.MSGID,
                           RESULT = CONSTANTS.SUCCESS
                       };
   
                       rstMsg.Header = new Header()
                       {
                           MSGID = msgId++,
                           MSGTYPE = CONSTANTS.FILE_SEND_RES,
                           BODYLEN = (uint)rstMsg.Body.GetSize(),
                           FRAGMENTED = CONSTANTS.NOT_FRAGMENTED,
                           LASTMSG = CONSTANTS.LASTMSG,
                           SEQ = 0
                       };
   
   
                       if (fileSize == recvFileSize)
                           MessageUtil.Send(stream, rstMsg); // 파일 전송 요청에 담겨온 파일 크기와    실제로 받은 파일의 크기를 비교하여, 같으면 성공 메시지를 보낸다.
                       else
                       {
                           rstMsg.Body = new BodyResult()
                           {
                               MSGID = reqMsg.Header.MSGID,
                               RESULT = CONSTANTS.FAIL
                           };
   
                           MessageUtil.Send(stream, rstMsg); // 파일 크기에 이상이 있다면 실패    메시지를 보낸다.
                       }
                       Console.WriteLine("파일 전송을 마쳤습니다.");
   
                       stream.Close();
                       client.Close();
                   }
               }
               catch (SocketException e)
               {
                   Console.WriteLine(e);
               }
               finally
               {
                   server.Stop();
               }
   
               Console.WriteLine("서버를 종료합니다.");
   
           }
       }
   }
   ```

   </details>
   
<br>

### 21.4.5 클라이언트 구현하기

1. Visual Studio > 새 프로젝트 만들기 > '콘솔 앱(.NET Core)' > 프로젝트 이름 : FileSender > 만들기
2. FUP.dll을 프로젝트 참조에 추가시킨다. 솔루션 탐색기 > 'FileReceiver' 아래 '종속성' 선택 > 마우스 오른쪽 > 'COM 참조 추가' > [참조 관리자] > 찾아보기 > FUP.dll 선택 > 확인
3. Program.cs의 이름을 MainApp.cs로 바꾸고 다음 코드를 작성한다.
   <details>
   <summary>MainApp.cs</summary>

   ```cs
   using System;
   using System.Diagnostics;
   using System.IO;
   using System.Net;
   using System.Net.Sockets;
   using FUP;
   
   namespace FileSender
   {
       class MainApp
       {
           const int CHUNK_SIZE = 4096;
   
           static void Main(string[] args)
           {
               if (args.Length < 2)
               {
                   Console.WriteLine("사용법 {0} <Server IP> <File Path>", Process.    GetCurrentProcess().ProcessName);
                   return;
               }
   
               string serverIP = args[0];
               const int serverPort = 5425;
               string filepath = args[1];
   
               try
               {
                   IPEndPoint clientAddress = new IPEndPoint(0, 0);
                   IPEndPoint serverAddress = new IPEndPoint(IPAddress.Parse(serverIP),     serverPort);
   
                   Console.WriteLine("클라이언트 : {0}, 서버 : {1}",
                       clientAddress.ToString(), serverAddress.ToString());
   
                   uint msgId = 0;
   
                   Message reqMsg = new Message();
                   reqMsg.Body = new BodyRequest()
                   {
                       FILESIZE = new FileInfo(filepath).Length,
                       FILENAME = System.Text.Encoding.Default.GetBytes(filepath)
                   };
   
                   reqMsg.Header = new Header()
                   {
                       MSGID = msgId++,
                       MSGTYPE = CONSTANTS.REQ_FILE_SEND,
                       BODYLEN = (uint)reqMsg.Body.GetSize(),
                       FRAGMENTED = CONSTANTS.NOT_FRAGMENTED,
                       LASTMSG = CONSTANTS.LASTMSG,
                       SEQ = 0
                   };
   
                   TcpClient client = new TcpClient(clientAddress);
                   client.Connect(serverAddress);
   
                   NetworkStream stream = client.GetStream();
   
                   MessageUtil.Send(stream, reqMsg); // 클라이언트는 서버에 접속 하자마자 파일 전송     요청 메시지를 보낸다.
   
                   Message rspMsg = MessageUtil.Receive(stream); // 서버의 응답을 받는다.
   
                   if (rspMsg.Header.MSGTYPE != CONSTANTS.REP_FILE_SEND)
                   {
                       Console.WriteLine("정상적인 서버 응답이 아닙니다. {0}", rspMsg.Header.    MSGTYPE);
                       return;
                   }
   
                   if (((BodyResponse)rspMsg.Body).RESPONSE == CONSTANTS.DENIED)
                   {
                       Console.WriteLine("서버에서 파일 전송을 거부했습니다.");
                       return;
                   }
   
                   using (Stream fileStream = new FileStream(filepath, FileMode.Open))
                   {
                       byte[] rbytes = new byte[CHUNK_SIZE];
   
                       long readValue = BitConverter.ToInt64(rbytes, 0);
   
                       int totalRead = 0;
                       ushort msgSeq = 0;
                       byte fragmented = (fileStream.Length < CHUNK_SIZE) ?
                           CONSTANTS.NOT_FRAGMENTED : CONSTANTS.FRAGMENTED;
                       while (totalRead < fileStream.Length)
                       {
                           int read = fileStream.Read(rbytes, 0, CHUNK_SIZE);
                           totalRead += read;
                           Message fileMsg = new Message();
   
                           byte[] sendBytes = new byte[read];
                           Array.Copy(rbytes, 0, sendBytes, 0, read);
   
                           fileMsg.Body = new BodyData(sendBytes);
                           fileMsg.Header = new Header()
                           {
                               MSGID = msgId,
                               MSGTYPE = CONSTANTS.FILE_SEND_DATA,
                               BODYLEN = (uint)fileMsg.Body.GetSize(),
                               FRAGMENTED = fragmented,
                               LASTMSG = (totalRead < fileStream.Length) ?
                               CONSTANTS.NOT_LASTMSG : CONSTANTS.LASTMSG,
                               SEQ = msgSeq++
                           };
   
                           Console.Write("#");
   
                           MessageUtil.Send(stream, fileMsg);
                       }
   
                       Console.WriteLine();
   
                       Message rstMsg = MessageUtil.Receive(stream);
   
                       BodyResult result = ((BodyResult)rstMsg.Body);
                       Console.WriteLine("파일 전송 성공 : {0}", result.RESULT == CONSTANTS.    SUCCESS);
                   }
   
                   stream.Close();
                   client.Close();
               }
               catch (SocketException e)
               {
                   Console.WriteLine(e);
               }
   
               Console.WriteLine("클라이언트를 종료합니다.");
   
           }
       }
   }
   ```

   </details>

<br>

### 21.4.6 파일 업로드 시험하기

- 결과를 확인한다.  
  <img src="/uploads/e108cba28eec0ca1cfca61d5298d0f11/image.png" width="70%">

