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

### -Data Binding
**1. RelativeSource 를 사용 해서 바인딩**
  - Window 자신을 DataContext로 참조
```XAML
<Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:BindingProperties"
        mc:Ignorable="d"
        x:Class="BindingProperties.MainWindow"
        Title="MainWindow"
        Height="350"
        Width="525"
        Loaded="Window_Loaded"
        DataContext="{Binding RelativeSource={RelativeSource Self}}">
```

  - DataGrid Row에 Cell Button으로 delete를 구현할 때 RelativeSource를 사용한 예  
    부모인 DataGrid를 Relative 지정하고, DataContext.DeleteCustomerCommand 를 참조하고 CommandParameter는 현재 바인딩되어 있는객체
    여기서는 Row Data를 Command 파라미터로 설정 했다.
    
  
```XAML
                <DataGridTemplateColumn x:Name="deleteButtonColumn"
                                        Width="Auto">
                    <DataGridTemplateColumn.CellTemplate>
                        <DataTemplate>
                            <Button Command="{Binding RelativeSource={RelativeSource AncestorType=DataGrid}, Path=DataContext.DeleteCustomerCommand}"
                                    CommandParameter="{Binding}"
                                    Content="Delete" />
                        </DataTemplate>
                    </DataGridTemplateColumn.CellTemplate>
                </DataGridTemplateColumn>
            </DataGrid.Columns>
```

**2. Wpf Xaml , Covnerters**
IValueConverter를 상속 받아서 구현해야 한다.

```cs
    public class NegativeNumberConverter : IValueConverter
    {
        // Source -> Target
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            int input = (int)value;
            if (input >= 0) return input.ToString();
            else return string.Format("({0})", 0 - input);
        }

        //Target -> Source
        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            if (value.ToString().StartsWith("(") && value.ToString().EndsWith(")"))
            {
                return -int.Parse(value.ToString().Substring(1, value.ToString().Length - 2));
            }
            else return value;
        }
    }
```
 
-  Window Resource로 등록 해서 사용 한다.
```xaml
    <Window.Resources>
        <local:NegativeNumberConverter x:Key="NegConverter" />
    </Window.Resources>

    ...

    <TextBox x:Name="ageTextBox"
          Width="120"
          VerticalAlignment="Center"
          Text="{Binding Path=Age, Converter={StaticResource NegConverter}}"
          Grid.Row="3"
          Margin="3"
          Height="23"
          HorizontalAlignment="Left"
          Grid.Column="1" />
```

**3. Async Binding**

- IsAsync 속성을 True로 주면 Background Thread에서 Set동작을 하게 되는데, 여러 Target에서 수정을 할때 Concurrency(동시성)에 문제가 발생할 소지가 있음.

- Delay 속성은 ms 단위로 입력을 하며 Target에서 입력을 한 후 ms이후에 Source에 적용이 되도록 하는 Option이다.

```xaml
  <TextBox x:Name="phoneTextBox"
            Width="120"
            VerticalAlignment="Center"
            Text="{Binding Phone, IsAsync=True}"
            ...
            />
            
  <TextBox x:Name="TargetTextBox"
                 Text="{Binding ElementName=SourceTextBox, Path=Text, UpdateSourceTrigger=PropertyChanged, Delay=500}"
                 HorizontalAlignment="Left"
                 Height="23"
                 ...
                 />

```

**4. Fallback Values**
- Binding애 대한 오류 처리
 1) TargetNullValue : 연결 소스 속성이 null인 경우에 대한 처리
 2) FallbackValue : 연결 소스가 존재 하지 않을 경우에 대한 처리(Binding Path 오타로 인해 발생)

```xaml
            <TextBox x:Name="nameTextBox"
                     Width="120"
                     VerticalAlignment="Center"
                     Text="{Binding Path=Namex, Mode=Default, TargetNullValue='Not set', FallbackValue='no value'}"
/>

```
  
**5. DataTemplate**
- DataTemplate는 어떤 Control에 사용 할 수 있을까?
> 1) ContentControl.ContentTemplate
> 2) ItemsControl.ItemTemplate

  위 유형의 Control 에 사용 가능 함.

- DataTemplate Code Example
```xaml
<UserControl x:Class="DataTemplates.ExplicitDataTemplatesView"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" 
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008" 
             xmlns:local="clr-namespace:DataTemplates"
             mc:Ignorable="d" 
             d:DesignHeight="300" d:DesignWidth="300">
    <UserControl.Resources>
      <DataTemplate x:Key="ProductTemplate">
          <Grid>
              <Grid.ColumnDefinitions>
                  <ColumnDefinition Width="Auto"/>
                  <ColumnDefinition Width="Auto"/>
              </Grid.ColumnDefinitions>
              <Image x:Name="ProductImage" Source="{Binding Image}"  Margin="5" Width="100"/>
              <Grid Grid.Column="1" Height="{Binding ElementName=ProductImage, Path=Height}">
                  <Grid.RowDefinitions>
                      <RowDefinition Height="Auto"/>
                      <RowDefinition Height="Auto"/>
                  </Grid.RowDefinitions>
                  <TextBlock Text="{Binding Name}" FontFamily="Arial" FontSize="14" FontWeight="Bold"/>
                  <TextBlock Grid.Row="1" Text="{Binding Description}" TextWrapping="Wrap" Width="300"/>
              </Grid>
          </Grid>
      </DataTemplate>
    </UserControl.Resources>
    <Grid>
        <ListBox x:Name="ProductsListBox" ItemsSource="{Binding Products}"
                    ItemTemplate="{StaticResource ProductTemplate}">
        </ListBox>
    </Grid>
</UserControl>
```








