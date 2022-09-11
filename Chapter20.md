# 20. WinForm으로 만든는 사용자 인터페이스

## 20.1 도대체 무슨 일이 일어나고 있는 걸까?

- WinForm에서 제공하는 폼 디자이너 툴은 프로그래머가 그림 그리듯 UI를 만들 수 있게 한다.
- 이른바 WYSIWYG(What You See Is What You Get, 위지위그)방식의 개발을 지원한다.
- C# 코드 만으로 GUI를 구성할 수 있어야 한다.

<br><br>

## 20.2 C# 코드로 WinForm 윈도우 만들기

- 다음은 OS에서 앱이 위도우를 생성하는 절차이다.
  1. 윈도우 클래스를 정의한다.
  2. 정의된 윈도우 클래스를 등록한다.
  3. 윈도우를 생성한다.
  4. 윈도우를 사용자에게 보여준다.
  5. 메시지 루프를 돌면서 프로그램을 시작한다.
- 이것은 Win32 API를 이용하여 위도우를 만들 때의 이야기이다.
- .NET은 이러한 과정들을 잘 포장해서 개발자들이 간편하게 윈도우를 만들 수 있도록 WinForm 클래스 라이브러리를 제공한다.
  1. System.Windows.Forms.Form 클래스에서 파생된 윈도우 폼 클래스를 선언한다.
  2. 1)에서 만든 클래스의 인스턴스를 System.Windows.Forms.Application.Run() 메소드에 인수로 넘겨 호출한다.

### 실습

- 비주얼 스튜디오를 실행 > 프로젝트 탬플릿 > "콘솔 앱" > 프로젝트 이름 설정 > 만들기
- WinForm을 사용할 수 있도록 프로젝트 파일(.csproj)을 손본다.
- 생성한 프로젝트 항목을 더블 클릭으로 프로젝트.csproj 편집기를 불러온다 > TargetFramework를 net5.0-windows로 수정 > UseWindowForm 옵션과 DisableWinExeOutputInference를 true로 추가
  ```cs
  <Project Sdk="Microsoft.NET.Sdk">
  
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>net5.0-windows</TargetFramework> // 수정
      <RootNamespace>_20._2_SimpleWindow</RootNamespace> // 제거해도 무관
      // <ImplicitUsings>enable</ImplicitUsings> 제거
      <Nullable>enable</Nullable> // 제거해도 무관
      <UseWindowsForms>true</UseWindowsForms> // 추가
      <DisableWinExeOutputInference>true</DisableWinExeOutputInference> // 추가
    </PropertyGroup>
  
  </Project>
  ```
- 소스코드에 다음 내용을 넣는다.
  ```cs
  using System;
  namespace SimpleWindow {
      
      class MainApp : System.Windows.Forms.Form {
          static void Main(string[] args) {
              System.Windows.Forms.Application.Run(new MainApp());
          }
      }

  }
  ```

<br><br>

## 20.3 Application 클래스

- Application 클래스는 크게 두 가지 역할을 수행하는데, 하나는 윈도우 응용프로그램을 시작하고 종료시키는 메소드를 제공하는것이고, 또 다른 하나는 윈도우 메시지를 처리하는 것이다.
- Application.Run()을 통해 응용 프로그램을 시작하도록 한다.
- Application.Exit() 메소드를 호출하면 응용프로그램은 종료된다.
  ```cs
  using System;
  using System.Windows.Forms;
  
  namespace UsingApplication
  {
      class MainApp : Form
      {
          static void Main(string[] args)
          {
              MainApp form = new MainApp();
  
              form.Click += new EventHandler( // 윈도우를 클릭했을 때 발생하는 이벤트
                  (sender, eventArgs) =>
                  {
                      Console.WriteLine("Closing Window...");
                      Application.Exit();
                  });
  
              Console.WriteLine("Starting Window Application...");
              Application.Run(form);
  
              Console.WriteLine("Exiting Window Application...");
          }
      }
  }
  ```

<br>

### 20.3.1 메시지 필터링

- 메시지 필터링(Message Filtering)에 대해서 알아본다.
- 마우스 클릭과 키보드 입력과 같은 이벤트들은 OS들이 일으킨 것이다.
  - 사용자가 마우스나 키보드를 제어하면 인터럽트가 발생하고, 이 인터럽트를 윈도우 OS가 받아들인다.
  - OS는 이 인터럽트를 바탕으로 윈도우 메시지를 만든 뒤 이벤트를 받아야 하는 응용프로그램에게 보내준다.
  - 윈도우 OS에서 정의하는 메시지 식별번호(ID)가 있다. WN_LBUTTOMDOWN 메시지는 ID가 0x201로 정의되어 있다.
  - Application 클래스는 특정 ID를 가진 메시지를 걸러내는 필터를 함께 등록해 뒀다가, 응용프로그램에 메시지가 전달되면 해당 필터를 동작시킨다. 그렇지 않으면 메시지를 거르지 않고 메시지를 받아야 하는 폼이나 컨트롤로 보내서 이벤트를 발생시킨다.
- Application.AddMessageFilter() 메소드는 응용 프로그램에 메시지 필터를 설치한다.
- 이 메소드는 IMessageFilter 인터페이스를 구현하는 파생 클래스의 인스턴스를 인수로 받으며, 다음과 같이 PreFilterMessage() 메소드를 구현해야 한다.
  ```cs
  puvlic interface IMessageFilter {
    bool PreFilterMessage(ref Message m);
  }

  public class MessgaeFilter : IMessageFilter {
    public bool PreFilterMessage(ref Message m){
      if (m.Msg >= 0x200 && m.Msg <= 0x20E) { // 마우스 이동, 왼쪽 오른쪽 가운데 버튼, 마우스 휠 굴림 메시지 포함
        Console.WriteLine("발생한 메시지 : " + m.Msg);
        retuen true;
      }
      return false;
    }
  }
  ```
- Message 구조체는 다음과 같은 프로퍼티를 갖고 있다.
  - HWnd : 메시지를 받는 윈도우 핸들이다. 윈도우의 인스턴스를 식별하고 관리하기 위해 운영체제가 붙여놓은 번호가 바로 핸들이다.
  - MSG : 메시지 ID 이다.
  - LParam : 메시지를 처리하는데 필요한 정보가 담겨 있다.
  - WParam : 메시지를 처리하는 데 필요한 부가 정보가 담겨있다.
  - Result : 메시지 처리에 대한 응답으로 윈도우 OS에 반환되는 값을 지정한다.
- 다음과 닽이 AddMessageFilter() 메소드를 호출하여 등록하면 된다.
  ```cs
  Application.AddMessageFilter( new MessageFilter() );
  ```

### 실습

- WM_PAINT (0x0F), WM_MOUSEMOVE(0x200), WM_TIMER(0x113) 메시지를 제외하고 전달받는 모든 메시지를 출력한다.
- WM_LBUTTOMDOWN(0x201) 메시지가 도착하면 Application.Exit()를 호출하여 종료한다.
  ```cs
  using System;
  using System.Windows.Forms;
  
  namespace MessageFilter
  {
  
      class MessageFilter : IMessageFilter
      {
          public bool PreFilterMessage(ref Message m)
          {
              if( m.Msg == 0x0F || m.Msg == 0xA0 || m.Msg == 0x200 || m.Msg == 0x113)
                  return false;
  
              Console.WriteLine($"{m.ToString()} : {m.Msg}");
  
              if (m.Msg == 0x201)
                  Application.Exit();
              
              return true;
          }
  
      }
      class MainApp : Form
      {
          static void Main(string[] args)
          {
              Application.AddMessageFilter(new MessageFilter());
              Application.Run(new MainApp());
          }
      }
  }
  ```

>>>
### 실행 결과
msg=0x31f hwnd=0x181054 wparam=0x1 lparam=0x0 result=0x0 : 799  
msg=0xc1ec hwnd=0x181054 wparam=0x0 lparam=0x0 result=0x0 : 49644  
msg=0xc0ff hwnd=0x181054 wparam=0x0 lparam=0x0 result=0x0 : 49407  
msg=0x7fff (WM_REFLECT + ???) hwnd=0x7e1046 wparam=0x1 lparam=0x0 result=0x0 : 32767  
msg=0x7fff (WM_REFLECT + ???) hwnd=0x7e1046 wparam=0x6 lparam=0x0 result=0x0 : 32767  
msg=0x287 hwnd=0xcc0fee wparam=0x20 lparam=0x0 result=0x0 : 647  
msg=0x204 (WM_RBUTTONDOWN) hwnd=0x181054 wparam=0x2 lparam=0x6b00a8 result=0x0 : 516  
msg=0x118 hwnd=0x181054 wparam=0xfffa lparam=0x118 result=0x0 : 280  
msg=0x205 (WM_RBUTTONUP) hwnd=0x181054 wparam=0x0 lparam=0x520092 result=0x0 : 51  
msg=0x20a (WM_MOUSEWHEEL) hwnd=0x181054 wparam=0x780000 lparam=0xad00e3 result=0x0 : 522  
msg=0x20a (WM_MOUSEWHEEL) hwnd=0x181054 wparam=0x780000 lparam=0xad00e3 result=0x0 : 522  
msg=0x118 hwnd=0x181054 wparam=0xfffa lparam=0x118 result=0x0 : 280  
msg=0x287 hwnd=0xcc0fee wparam=0x20 lparam=0x0 result=0x0 : 647  
msg=0x201 (WM_LBUTTONDOWN) hwnd=0x181054 wparam=0x1 lparam=0x5100c2 result=0x0 : 513  
>>>

<br><br>

## 20.4 윈도우를 포현하는 From 클래스

- 윈도우의 크기와 모양을 바꾸고 버튼도 올려본다.
  - Form에 정의된 이벤트와 이벤트 처리기 연결
  - Form의 프로퍼티를 조절하여 윈도우 모양 바꾸기
  - Form 위에 컨트롤 올리기

<br>

### 20.4.1 From에 정의된 이벤트와 이벤트 처리기 연결하기

- 윈도우 위에서 마우스 왼쪽 버튼을 누르면, WM_LBUTTONDOWN 메시지가 Form객체로 전달되고, Form 객체는 MouseDown 이벤트를 발생시킨다.
- 다음과 같이 이벤트 처리기 메소드를 선언하고 Form 클래스의 MouseDown 이벤트에 등록해본다.
  ```cs
  class MyForm : From {
    // 이벤트 처리기 선언
    private void Form_MouseDown(object sender, System.Windows.Froms.MouseEventArgs e){
      MessageBox.Show("안녕하세요!");
    }

    public MyForm(){
      // 이벤트 처리기를 이벤트에 연결
      this.MouseDown += System.Windows.Forms.MouseEventHandler (this.Form_MouseDown);
    }
  }
  ```
- MouseDown 이벤트의 선언이다.
  ```cs
  public event MouseEventHandler MouseDown;
  ```
- 대리자는 다음과 같이 선언되어 있다.
  ```cs
  public delegate void MouseEventHandler ( object sender, MouseEventArgs e );
  ```
- sender는 이벤트가 발생한 객체를 가리킨다.
- MouseEventArgs 형식은 다음과 같은 프로퍼티들을 제공함으로써 마우스 이벤트의 상세 정보를 제공한다.
  - Button : 오른쪽, 왼쪽, 가운데 버튼 이벤트가 발생했는지 나타낸다.
  - Clicks : 마우스 버튼 클릭 횟수를 나타낸다. 더블클릭했을 때만 어떤기능을 수행 : 값이 2를 확인한다.
  - Delta : 마우스 휠의 회전 방향과 회전한 거리를 나타낸다.
  - X : 마우스 이벤트가 발생한 폼 또는 컨트롤상의 x(가로) 좌표를 나타낸다.
  - Y : 마우스 이벤트가 발생한 폼 또는 컨트롤상의 y(세로) 좌표를 나타낸다.

### 실습

- 다음과 같이 코드를 구성한다.
  ```cs
  using System;
  using System.Windows.Forms;
  
  namespace MessageFilter
  {
      class MainApp : Form
      {
          public void MyMouseHandler(object sender, MouseEventArgs e)
          {
              Console.WriteLine($"Sender : {((Form)sender).Text}");
              Console.WriteLine($"X : {e.X}, Y : {e.Y}");
              Console.WriteLine($"Buttons : {e.Button}, Clicks : {e.Clicks}");
              Console.WriteLine();
          }
  
          public MainApp(string title)
          {
              this.Text = title;
              this.MouseDown += new MouseEventHandler(MyMouseHandler);
          }
  
          static void Main(string[] args)
          {
              Application.Run(new MainApp("Mouse Event Test"));
          }
      }
  }
  ```

<br>

### 20.4.2 From의 프로퍼티를 조절하여 윈도우 모양 바꾸기

- Form 클래스는 위도우 모양을 결정짓는 크기, 배경색, 전경색, 투명도, 제목, 폰트 등 여러가지 프로퍼티를 가지고 있다.
  - 창 크기
    - Witdh : 창의 너비
    - Height : 창의 높이
  - 창의 색
    - BackColor : 창의 배경색
    - BackgroundImage : 창의 배경 이미지
    - Opacity : 창의 투명도
  - 창 스타일
    - MaximizeBox : 최대화 버튼 여부
    - MinimizeBox : 최소화 버튼 여부
    - Text : 창 제목
- 창의 배경색은 BackColor 프로퍼티로 바꿀수 있다. System.Drawing.Color 형식이기 때문에 Color 클래스의 정적 메소드나 미리 정의된 상수값을 이용해서 값을 지정해야 한다.
  ```cs
  Form form = new Form();
  form.BackColor = Color.red;
  form.BackColor = Color.FromArgb(255, 255, 0, 0); // 불투명한 빨간색

  form.Opacity = 0.87; // 살짝 투명
  form.Opacity = 1.00; // 완전 불투명

  form.BackgroundColor = Image.FromFile("MyImage.jpg");
  ```
- MinimizeBox, MaximizeBox 프로퍼티는 boolean 형식을 통해 최대화 버튼과 최소화 버튼을 표시하고 감춘다.


### 실습

- 첫 번째 실습은 크기에 대해 다룬다. 다음과 같이 코드를 구성한다.
  ```cs
  using System;
  using System.Windows.Forms;
  
  namespace FormSize
  {
      class MainApp : Form
      {
          static void form_MouseDown(object sender, MouseEventArgs e)
          {
              Form form = (Form)sender;
              int oldWidth = form.Width;
              int oldHeight = form.Height;
  
              if (e.Button == MouseButtons.Left)
              {
                  if(oldWidth < oldHeight)
                  {
                      form.Width = oldHeight;
                      form.Height = oldWidth;
                  }
              }
              else if ( e.Button == MouseButtons.Right )
              {
                  if (oldWidth > oldHeight)
                  {
                      form.Width = oldHeight;
                      form.Height = oldWidth;
                  }
              }
  
              Console.WriteLine("윈도우의 크기가 변경되었습니다.");
              Console.WriteLine($"Width : {form.Width}, Height : {form.Height}");
                
          }
  
          static void Main(string[] args)
          {
              MainApp form = new MainApp();
              form.Width = 300;
              form.Height = 200;
  
              form.MouseDown += new MouseEventHandler(form_MouseDown);
  
              Application.Run(form);
          }
      }
  }
  ```
- 두 번째 실습에서는 배경색과 최대화 최소화를 다룬다. 다음과 같이 코드를 구성한다.
  ```cs
  using System;
  using System.Windows.Forms;
  using System.Drawing;
  
  namespace FormSize
  {
      class MainApp : Form
      {
          Random rand;
          public MainApp()
          {
              rand = new Random();
  
              this.MouseWheel += new MouseEventHandler(MainApp_MouseWheel);
              this.MouseDown += new MouseEventHandler(MainApp_MouseDown);
          }
  
          void MainApp_MouseWheel(object sender, MouseEventArgs e)
          {
              this.Opacity = this.Opacity + (e.Delta > 0 ? 0.1 : -0.1);
              Console.WriteLine($"Opacity : {this.Opacity}");
          } 
          void MainApp_MouseDown(object sender, MouseEventArgs e)
          {
              Form form = (Form)sender;
  
              if (e.Button == MouseButtons.Left)
              {
                  Color oldColor = this.BackColor;
                  this.BackColor = Color.FromArgb(rand.Next(0, 255), rand.Next(0, 255), rand.Next(0,   255));
  
                  form.MaximizeBox = true;
                  form.MinimizeBox = true;
                  form.Text = "최소화/최대화 버튼이 활성화 되었습니다.";
              }
              else if (e.Button == MouseButtons.Right)
              {
                  form.MaximizeBox = false;
                  form.MinimizeBox = false;
                  form.Text = "최소화/최대화 버튼이 비활성화 되었습니다.";
  
                  if (this.BackgroundImage != null)
                  {
                      this.BackgroundImage = null;
                      return;
                  }
  
                  string file = "sample.jpg";
                  if (System.IO.File.Exists(file) == false)
                      MessageBox.Show("이미지 파일이 없습니다.");
                  else
                      this.BackgroundImage = Image.FromFile(file);
              }
          }
  
  
          static void Main(string[] args)
          {
              MainApp form = new MainApp();
              form.Width = 400;
  
              Application.Run(form); // Application.Run(new MainApp());
          }
      }
  }
  ```

<br>

### 20.4.3 From 위에 컨트롤 올리기

- 윈도우 OS는 사용자 인터페이스를 위해 메뉴, 콤보박스, 리스트뷰, 버튼, 텍스트 박스 등과 같은 표준 컨트롤을 제공한다.
- 다음 과저을 거텨 컨트롤을 작성한다.
  1. 컨트롤의 인스턴스 생성
    - WinForm의 모든 컨트롤은 System.Windows.Forms.Control을 상속한다.
  ```cs
  Button button = new Button();
  ```
  2. 컨트롤의 프로퍼티에 값 저장
  ```cs
  button.Text = "Click Me!";
  button.Left = 100;
  button.Top = 50;
  ```
  3. 컨트롤의 이벤트에 이벤트 처리기 등록
  ```cs
  button.Click += (object sender, EventArgs e) => { MessageBox.Show("딸깍!"); };
  ```
  4. 폼에 컨트롤 추가
  ```cs
  MainApp form = new MainApp();
  form.Control.Add(button);
  ```

### 실습

- 다음과 같이 코드를 구성한다.
  ```cs
  using System;
  using System.Windows.Forms;
  
  namespace FormSize
  {
      class MainApp : Form
      {
          static void Main(string[] args)
          {
              Button button = new Button();
              button.Text = "Click Me!";
              button.Left = 100;
              button.Top = 50;
              button.Click += (object sender, EventArgs e) => { MessageBox.Show("딸깍!"); };
  
              MainApp form = new MainApp();
              form.Text = "Form & Control";
              form.Height = 150;
  
              form.Controls.Add(button);
  
              Application.Run(form); // Application.Run(new MainApp());
          }
      }
  }
  ```

<br><br>

## 20.5 폼 디자이너를 위한 WinFrom UI 구성

- 비주얼 스튜디오는 UI계의 포토샵이라고 할 만한, 사용하기는 쉬우면서도 기능이 강력한 폼 디자이너(Form Designer)를 제공한다.

<br>

### 20.5.1 새 프로젝트 만들기

- 새 프로젝트 > "Windows Form App (.NET)" 템플릿 선택 > 프로젝트 이름 지정 > 만ㄴ들기
- 솔루션 탐색기에서 Form1.cs의 파일 이름을 MainFrom.cs로 변경한다.

<br>

### 20.5.2 From

- MainForm의 속성을 "폼 디자이너를 이용해서" 변경 한다.
- 속성창을 본다. (없으면 [보기] > [속성창] 선택)
- 속성창 > 'Text' 프로퍼티 > 'Control Test'로 값을 변경해본다.
- 다른 속성 또한 쉽게 변경이 가능하다.

<br>

### 20.5.3 GroupBox, Lable, ComboBox, CheckBox, TextBox

1. 지금부터는 한 번에 여러 개의 컨트롤을 폼 위에 배치한다.
   - Label 컨트롤 (Name :lblFont, Text : Font)
   - TextBox 컨트롤 (Name : txtSampleText, Text : Hello, C#)
   - GroupBox 컨트롤 (Name : grpFont, Text : "ComboBox, CheckBox, TextBox")
   - CheckBox 컨트롤 (Name : chkBold, Text : 굵게)
   - CheckBox 컨트롤 (Name : chkItalic, Text : 이탤릭)
   - ComboBox 컨트롤 (Name : cboFont)  
   이후 MainForm의 속성창 > 번개아이콘 > Form 컨트롤 이벤트 > "Load"항목을 찾아 더블클릭한다.  
   그러면 IDE가 MainForm_Load() 이벤트 처리기의 껍데기를 만들면서 코드 편집창이 열린다.  
   ```cs
   private void MainForm_Load(object sender, EventArgs e) {
     var Fonts = FontFamily.Families; // 운영체제에 설치되어 있는 폰트 목록 검색
     foreach (FontFamily font in Fonts) 
       cboFont.Item.Add(font.Name);
   }
   ```
2. 이어서 다음 코드를 작성한다. 다음 코드는 cboFont와 chkBold, chkItalic 컨트롤의 이벤트 처리기에서 호출하기 위한 것이다.
   ```cs
   void ChangeFont() {
       if (cboFont.SelectedIndex < 0) return; // cboFont 에서 선택한 항목이 없으면 메소드 종료

       FontStyle style = FontStyle.Regular; // FontStyle 객체를 초기화 한다.
   
       if(chkBold.Checked)
           style |= FontStyle.Bold;
   
       if(chkItalic.Checked)
           style |= FontStyle.Italic;
   
       // txtSampleText의 Font 프로퍼티를 앞에서 만든 style로 수정
       txtSampleText.Font = new Font((string)cboFont.SelectedItem, 10, style);
   }
   ```
3. cboFont, chkBold, chkItalic 각 컨트롤에 대한 이벤트 처리기 껍데기를 만든다.  
   각 컨트롤을 클릭 > 번개표시 > 속성창에서 찾는다.
   - cboFont : cboFont_SelectedIndexChanged
   - chkBold : chkBold_CheckedChanged
   - chkItalic : chkItalic_CheckedChanged
   ```cs
   private void cboFont_SelectedIndexChanged(object sender, EventArgs e) {
       ChangeFont();
   }
   
   private void chkBold_CheckedChanged(object sender, EventArgs e) {
       ChangeFont();
   }
   
   private void chkItalic_CheckedChanged(object sender, EventArgs e) {
       ChangeFont();
   }
   
   ```
4. 결과를 확인한다.  

   <img src="https://user-images.githubusercontent.com/66783849/189506598-ad880af0-56a5-4c76-999d-d30e2cc62497.png" width="30%">

<br>

### 20.5.4 TrackBar, ProgressBar

1. TrackBar, ProgressBar 컨트롤을 폼 위에 배치한다. (20.5.3에 이어서 작업)
   - GroupBox 컨트롤 (Name : gtpBar, Text : "TrackBar && ProgressBar")
   - TrackBar 컨트롤 (Name : tbDummy, Maximum : 20)
   - ProgressBar 컨트롤 (Name : pgDummy, Maximum : 20)
2. tdDummy 컨트롤에 대한 tdDummy_Scroll 이벤트 처리기 껍데기를 만든다.  
   각 컨트롤을 클릭 > 번개표시 > 속성창에서 찾는다.  
   ```cs
   private void tbDummy_Scroll(object sender, EventArgs e)
   {
       pgDummy.Value = tbDummy.Value;
   }
   ```
3. 결과를 확인한다.  

   <img src="https://user-images.githubusercontent.com/66783849/189506587-9146bf7b-1d51-43ac-89fd-9bf62a3e6100.png" width="30%">

<br>

### 20.5.5 Button, From, Dialog

- 윈도우 프로그래밍에서 두 가지 모드의 자식 창을 띄울 수 있다. Modal, Modaless 두 가지가 있다.  
- Modal은 창을 띄우고 나면 그 창을 닫을 때 까지 프로그램의 다른 UI를 절대 사용할 수 없다.  
- Modaless는 창을 띄우고 난 뒤에도 프로그램의 다른 UI를 사용자가 접근할 수 있다.

1. GroupBox, Button 컨트롤을 폼 위에 배치한다. (20.5.4에 이어서 작업)
   - GroupBox 컨트롤 (Name : grpForm, Text : "Modal && Modaless")
   - Button 컨트롤 (Name :  btnModal, Text : Modal)
   - Button 컨트롤 (Name :  btnModaless, Text : Modaless)
   - Button 컨트롤 (Name :  btnMsgBox, Text : MessageBox)
2. button 컨트롤에 대한 btnModal_Click 이벤트 처리기 껍데기를 만든다.  
   각 컨트롤을 클릭 > 번개표시 > 속성창에서 찾는다.  
   ```cs
   private void btnModal_Click(object sender, EventArgs e) {
       Form frm = new Form();
       frm.Text = "Modal Form";
       frm.Width = 300;
       frm.Height = 100;
       frm.BackColor = Color.Red;
       frm.ShowDialog(); // Modal 창을 띄운다.
   }
   
   private void btnModaless_Click(object sender, EventArgs e) {
       Form frm = new Form();
       frm.Text = "Modaless Form";
       frm.Width = 300;
       frm.Height = 300;
       frm.BackColor = Color.Green;
       frm.Show(); // Modaless 창을 띄운다.
   }
   
   private void btnMsgBox_Click(object sender, EventArgs e) {
       MessageBox.Show(txtSampleText.Text,
           "MessageBox Test", MessageBoxButtons.OK, MessageBoxIcon.Exclamation);
   }
   ```
3. 결과를 확인한다.  

   <img src="https://user-images.githubusercontent.com/66783849/189506574-a15c7587-4a6f-4666-bbc4-274d3513c5d9.png" width="30%">



<br>

### 20.5.6 TreeView, ListView

1. TreeView, ListView 컨트롤을 폼 위에 배치한다. (20.5.4에 이어서 작업)
   - GroupBox 컨트롤 (Name : grpTreeList, Text : "TreeView && ListView")
   - Button 컨트롤 (Name :  btnAddRoot, Text : 루트 추가)
   - Button 컨트롤 (Name :  btnAddChild, Text : 자식 추가)
   - TreeView 컨트롤 (Name : tvDummy)
   - ListView 컨트롤 (Name : lvDummy, View : Details)
2. MainForm.cs에 코드를 추가한다. TreeView의 노드 이름으로 사용할 난수 생성기이다.
   ```cs
   public partial class MainFrom : Form
   {
      Random random = new Random(37);
      //...//
   ```
3. MainForm()에도 추가할 코드가 있다. lvDummy에 컬럼을 생성하는 코드를 넣는다.
   ```cs
   public MainFrom() {
       InitializeComponent();

       lvDummy.Columns.Add("Name");
       lvDummy.Columns.Add("Depth");
   }
   ```
4. TreeToList() 메소드도 추가한다.
   ```cs
   void TreeToList() {
       lvDummy.Items.Clear();
       foreach (TreeNode node in tvDummy.Nodes)
           TreeToList(node);
   }

   void TreeToList(TreeNode Node) {
       lvDummy.Items.Add( new ListViewItem(
               new String[] {
                   Node.Text, Node.FullPath.Count(f => f == '\\').ToString() 
               } // TreeNode 형식의 FullPath 프로퍼티는 루트 노드부터 현재 노드까지 경로를 '\'로 구분
           )
       );

       foreach (TreeNode node in Node.Nodes)
           TreeToList(node);
   }
   ```
5. button 컨트롤에 대한 btnAddRoot_Click 이벤트 처리기 껍데기를 만든다.  
   각 컨트롤을 클릭 > 번개표시 > 속성창에서 찾는다. 
   ```cs
   private void btnAddRoot_Click(object sender, EventArgs e)
   {
       tvDummy.Nodes.Add(random.Next().ToString());
       TreeToList();
   }

   private void btnAddChild_Click(object sender, EventArgs e)
   {
       if (tvDummy.SelectedNode == null)
       {
           MessageBox.Show("선택된 노드가 없습니다.",
               "TreeView Test", MessageBoxButtons.OK, MessageBoxIcon.Error);
           return;
       }

       tvDummy.SelectedNode.Nodes.Add(random.Next().ToString());
       tvDummy.SelectedNode.Expand();
       TreeToList();
   }
   ```
6. 결과를 확인한다.  

   <img src="https://user-images.githubusercontent.com/66783849/189506668-f3026450-70ce-4fea-ac58-5b8d874acbda.png" width="30%">

<br><br>

## 20.6 사용자 인터페이스와 비동기 작업

- 비동기 코드 async와 await를 활용하여, 파일 복사를 하는 윈도우 프로그램을 만들어본다.

<br>

### 20.6.1 새 프로젝트 만들기

- 새 프로젝트 > "Windows Form App (.NET)" 템플릿 선택 > 프로젝트 이름 지정 > 만ㄴ들기
- 솔루션 탐색기에서 Form1.cs의 파일 이름을 MainFrom.cs로 변경한다.

<br>

### 20.6.2 CopySync()/CopyAsync() 메소드 구현하기

- CopySync()/CopyAsync() 메소드 코드를 구현한다.

<br>

### 20.6.3 UI 구현하기

- 다음 소스 코드를 구현한다.
  ```cs
  private async Task<long> CopyAsync(string FromPath, string ToPath)
  {
      btnSyncCopy.Enabled = false;
      long totalCopied = 0;

      using (FileStream fromStream = new FileStream(FromPath, FileMode.Open))
      {
          using(FileStream toStream = new FileStream(ToPath, FileMode.Create))
          {
              byte[] buffer = new byte[1024 * 1024];
              int nRead = 0;
              while((nRead = await fromStream.ReadAsync(buffer, 0, buffer.Length)) != 0)
              {
                  await toStream.WriteAsync(buffer, 0, nRead);
                  totalCopied += nRead;

                  // 프로그래스바에 현재 파일 복사 상태 표시
                  pbCopy.Value = (int)(((double)totalCopied / (double)fromStream.Length) * pbCopy.Maximum);
              }
          }
      }

      btnSyncCopy.Enabled = true;
      return totalCopied;
  }

  private long CopySync(string FromPath, string ToPath)
  {
      btnAsyncCopy.Enabled = false;
      long totalCopied = 0;

      using (FileStream fromStream = new FileStream(FromPath, FileMode.Open))
      {
          using (FileStream toStream = new FileStream(ToPath, FileMode.Create))
          {
              byte[] buffer = new byte[1024 * 1024];
              int nRead = 0;
              while ((nRead = fromStream.Read(buffer, 0, buffer.Length)) != 0)
              {
                  toStream.WriteAsync(buffer, 0, nRead);
                  totalCopied += nRead;

                  // 프로그래스바에 현재 파일 복사 상태 표시
                  pbCopy.Value = (int)(((double)totalCopied / (double)fromStream.Length) * pbCopy.Maximum);
              }
          }
      }
      btnAsyncCopy.Enabled = true;
      return totalCopied;
  }


  private void Form1_Load(object sender, EventArgs e)
  {

  }


  private void btnCancle_Click(object sender, EventArgs e)
  {
      MessageBox.Show("UI 반응 테스트 성공");
  }
  private void btnSyncCopy_Click(object sender, EventArgs e)
  {
      long totalCopied = CopySync(txtSource.Text, txtTarget.Text);
  }

  private async void btnAsyncCopy_Click(object sender, EventArgs e)
  {
      long totalCopied = await CopyAsync(txtSource.Text, txtTarget.Text);
  }
  private void btnFindTarget_Click(object sender, EventArgs e)
  {
      SaveFileDialog dlg = new SaveFileDialog();
      if (dlg.ShowDialog() == DialogResult.OK)
          txtTarget.Text = dlg.FileName;
  }
  private void btnFindSource_Click(object sender, EventArgs e)
  {
      OpenFileDialog dlg = new OpenFileDialog();
      if (dlg.ShowDialog() == DialogResult.OK)
          txtSource.Text = dlg.FileName;
  }
  ```
  
### 결과 확인하기  
  <img src="https://user-images.githubusercontent.com/66783849/189506623-acc4ce42-02e7-4e94-b20f-610f0b710653.png" width="30%">

