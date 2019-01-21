# WPF Study

## Xaml의 기초  

### -Namespace
- Xaml에 가장 먼저 익숙치 않은 문법들이 있는데 그 중에 하나가 바로 아래의 문법이다.

```xaml
<Button xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" //바로 이 부분
  Content="Stop"/>
```
위 코드에서 **xmlns** 바로 이것인데 풀어 보면 xaml namespace 라는 뜻이다. 위 코드를 C#으로 풀어 보면

```c#
Windows.UI.Xaml.Controls.Button b = new Windows.UI.Xaml.Controls.Button();
b.Content = "Stop";
```

> http://schemas.microsoft.com/winfx/2006/xaml/presentation 이 주소 값이 Button의 namespace가 되는 것이다.  

C# 코드와 비교 하니 의미가 더 잘 이해 되는 것 같다.

그럼 Visual Studio에서 처음 WPF 프로젝트를 만들면 자동으로 생성 되는 Window class 를 한번 보자.

>XAML Code
```XAML
<Window 
    x:Class="MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="Main Window" 
    Height="300" 
    Width="300" />
```
그럼 Window의 XAML 코드도 분석이 가능 할 듯 하다.

### -XAML Template



