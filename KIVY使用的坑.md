## 1.kivy安装
`pip install kivy` 
这里需要注意，python3版本必须为3.8以下的版本，否则安装不成功，目前使用的是3.7
## 2.第一个示例
kv文件：test.kv
```yaml
<IndexPage>
    Button:
        text:"Hello world"
```
py文件：
```python
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
class IndexPage(BoxLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class TestApp(App):  #必须与上面kv文件的名字相同，即xxxx+APP的形式
    def build(self):
        return IndexPage()

if __name__=="__main__":
    TestApp().run()
```

## 3.程序打包

## 4.页面布局
### （1）基本布局
在kv文件中可以进行相应的排版：
```yaml
<IndexPage>
    Button:
        text:'pos'
        #注意，所有跟大小，位置相关的参数都在0-1之间
        size_hint:.2,.15 #即0.2与0.15，也就是当前窗口宽高的比值，宽为当前窗口的0.2倍，高为当前窗口的0.15倍
        pos_hint：{'x':0.2,'y':.6} #整个窗口左下角为（0，0）的一个坐标系，然后控件横向有x（左），right（右），center_x（中）三个位置，纵向有top（上），center_y（中），y（下）
```
### （2）FloatLayout布局
floatlayout是浮动布局可以根据不同分辨率同比例适应空间大小
py文件
```python
from kivy.app import App
from kivy.uix.floatlayout import FloatLayout

class FloatLayoutWidget(FloatLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class floatlayoutApp(App):
    def build(self):
        return FloatLayoutWidget()

if __name__=='__main__':
    floatlayoutApp().run()

```
新建kv文件flaoatlayout.kv：
```yaml
##该button为后面所有button公用属性
<Button>:  
  font_size:40
  size_hint:0.3,0.3

<FloatLayoutWidget>:
  canvas:  ##画布背景属性
    Color:
      rgba:[1,1,1,1]
    Rectangle:
      size:self.size
      pos:self.pos
  Button:
    text:"Bt0"
    background_color:0.1,0.5,0.6,1 ##按钮颜色
    pos_hint:{"x":0,"top":1}   ##按钮位置

  Button:
    text:"Bt1"
    background_color:1,0,0,1
    pos_hint:{"x":0.35,"y":0.3}

  Button:
    text:"Bt2"
    background_color:0.4,0.5,.6,1
    pos_hint:{"x":0.7,"y":0}

  Button:
    text:"Bt3"
    background_color:0,0,1,1
    pos_hint:{"x":0.7,"top":1}

  Button:
    text:"Bt4"
    background_color:0.1,0.5,0.6,1
    pos_hint:{"x":0,"y":0}
```
### （3）boxlayout
类似于横向整行或者纵向整列的布局
首先建立py文件：
```python
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout

class BoxLayoutWidget(BoxLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class boxlayoutApp(App):
    def build(self):
        return BoxLayoutWidget()

if __name__=='__main__':
    boxlayoutApp().run()
```
然后建立boxlayout.kv
```yaml
<BoxLayoutWidget>:
  orientation:'vertical' ##排列布局：纵向
  Button:
    text:"Btn0"
    background_color:0,1,1,1
    font_size:40

  Button:
    text:"Btn1"
    background_color:0,1,0,1
    font_size:20

  Button:
    text:"Btn2"
    background_color:0,0,1,1
    font_size:35

  Button:
    text:"Btn3"
    background_color:1,0,1,1
    font_size:30

  Button:
    text:"Btn4"
    background_color:1,0,0,1
    font_size:25

  BoxLayout:
    orientation:'horizontal' ##排列布局：横向
    padding:[1,1,1,1]      ##四参数边距分别为左上右下，两参数为水平和垂直
    spacing:20        ##子项之间间距
    Button:
      text:'first'
      size_hint_y:.2

    Button:
      text:'second'
      size_hint_y:.3
```
### （4）AnchorLayout布局
首先建立py文件：
```python
from kivy.app import App
from kivy.uix.anchorlayout import AnchorLayout

class AnchorLayoutWidget(AnchorLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class anchorlayoutApp(App):
    def build(self):
        return AnchorLayoutWidget()

if __name__=="__main__":
    anchorlayoutApp().run()

```
然后建Anchorlayout.kv
```yaml
<AnchorLayoutWidget>:
  padding:20
  AnchorLayout:
    anchor_x:'left'
    anchor_y:'top'
    Button:
      text:'hello'
      size_hint:.3,.2

  AnchorLayout:
    anchor_x:'right'
    anchor_y:'bottom'
    Button:
      text:'hello'
      size_hint:.3,.2
```
### (5)Gridlayout布局
首先建立py文件：
```python
from kivy.app import App
from kivy.uix.gridlayout import GridLayout

class GridLayoutWidget(GridLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class GridlayoutApp(App):
    def build(self):
        return GridLayoutWidget()

if __name__=='__main__':
    GridlayoutApp().run()
```
然后建立gridlayout.kv文件
```yaml
<GridLayoutWidget>:
  padding:20
  spacing:20
  cols:3 #每行有多少列
  col_force_default:True #强制使用默认列宽度，默认为关闭
  col_default_width:120  #列宽度
  row_force_default:True #强制使用默认行高，默认为关闭
  row_default_height:40  #行高
  cols_minimum:{0:130,1:140} #单独设置每一列的列宽，本例中为第一列为130px,第二列为140px
  rows_minimum:{0:60}  #单独设置每一行的行高，本例中为第一行行高为60px
  Button:
    text:"1"

  Button:
    text:"2"

  Button:
    text:"3"

  Button:
    text:"4"

  Button:
    text:"5"
```
### （6）pagelayout布局
首先建立py文件：
```python
from kivy.app import App
from kivy.uix.pagelayout import PageLayout


class PageLayoutWidget(PageLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class pagelayoutApp(App):
    def build(self):
        return PageLayoutWidget()

if __name__=="__main__":
    pagelayoutApp().run()
```
然后建立pagelayout.kv文件：
```yaml
<PageLayoutWidget>:
  
  border:'100dp' #两边边界宽窄
  anim_kwargs:{'d':10,'t':linear} #设置翻页动画以及持续时间
  page：2 #设置默认第几页为当前页
  swipe_threshold:.8  #翻页灵敏度


  Button:
    text:'page0'
    background_color:.3,.3,.3,1

  Button:
    text:'page1'
    background_color:.9,.3,.3,1

  Button:
    text:'page2'
    background_color:.3,.9,.3,1

  Button:
    text:'page3'
    background_color:.3,.3,.9,1
```
### (7)relativelayout布局
首先py文件：
```python
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout

class RelativeLayoutWidget(BoxLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class relativelayoutApp(App):
    def build(self):
        return RelativeLayoutWidget()

if __name__=='__main__':
    relativelayoutApp().run()
```
然后relativelayout.kv
```yaml
<Button>:
  font_size:28
  size_hint:.2,.2


<RelativeLayoutWidget>:

  BoxLayout:

  RelativeLayout:
    Button:
      text:'1'
      background_color:.1,.5,.6,1
      pos_hint:{'right':1,'top':1}

    Button:
      text:'2'
      background_color:1,0,0,1
      pos_hint:{'x':0,'top':1}

    Button:
      text:'3'
      background_color:.4,.5,.6,1
      pos_hint:{'center_x':.5,'top':.5}

    Button:
      text:'4'
      background_color:0,0,1,1
      pos_hint:{'x':0,'y':0}

    Button:
      text:'5'
      background_color:.8,.9,.2,1
      pos_hint:{'right':1,'y':0}
```
### (8)Scatterlayout布局
该布局可以随意拖动，首先py文件：
```python
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout

class ScatterLayoutWidget(BoxLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)

class scatterlayoutApp(App):
    def build(self):
        return ScatterLayoutWidget()

if __name__=='__main__':
    scatterlayoutApp().run()
```
然后Scatterlayout.kv
```yaml
<ScatterLayoutWidget>:
  ScatterLayout:
    Image:
      source:'1.jpg'
```
### （9）stacklayout布局
首先建立py文件：
```python

```
然后建立stacklayout.kv
```yaml

```
### (10)布局小实例
时钟实例：
首先编写kv文件，确定布局：
```yaml
<ClockBoxLayout>:
  orientation:'vertical'

  Label:  #添加一个标签
    id:time_label_id  #设置id属性
    text:'[b]00[/b]:00:00'   #标签显示的文本
    font_size:60      #字体大小
    markup:True    #使用标记

  BoxLayout:
    orientation:'vertical'
    padding:20
    height:200
    spacing:20
    size_hint:(1,None)

    Button:
      id:start_stop_button_id
      text:'Start'
      font_size:25
      bold:True
      border:[2,2,2,2]
      on_press:root.start_or_stop()
    
    Button:
      text:'Reset'
      on_press:root.reset_clock()

  Label:
    id:stopwatch
    text:'00:00.[size=40]00[/size]'
    font_size:60
    markup:True
```
然后编写py文件：
```python
from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.clock import Clock
from time import strftime


class ClockBoxLayout(BoxLayout):
    def __init__(self,**kwargs):
        super().__init__(**kwargs)
        self.on_start()
        self.timing_flag=False
        self.second_record=0

    def on_start(self):
        #self.ids.time_label_id.text='1234'
        Clock.schedule_interval(self.update_time,0)


    def update_time(self,nap):
        if self.timing_flag==True:
            self.second_record+=nap

        m,s=divmod(self.second_record,60)
        self.ids.stopwatch.text=('%02d:%02d.[size=40]%02d[/size]'%(int(m),int(s),int(s*100%100)))
        self.ids.time_label_id.text=strftime('[b]%H[/b]:%M:%S')


    def start_or_stop(self):
        self.timing_flag=not self.timing_flag
        if self.timing_flag==True:
            self.ids.start_stop_button_id.text='Stop'
        elif self.timing_flag==False:
            self.ids.start_stop_button_id.text='start'

    def reset_clock(self):
        self.second_record=0
        self.ids.stopwatch.text='00:00.[size=40]00[/size]'
        if self.timing_flag:
            self.ids.start_stop_button_id.text = 'start'
            self.timing_flag = not self.timing_flag

class ClockApp(App):
    def build(self):
        return ClockBoxLayout()


if __name__=="__main__":
    from kivy.core.window import Window
    Window.clearcolor=[.8,.8,.8,1]
    ClockApp().run()
```
## 5.图形绘制
### （1）窗口纯色，图片
新建py文件，掠过
新建kv文件：
```yaml
#主要是在最外层加上canvas属性
canvas：
    Color:
        rgba:[1,1,1,1] #使用纯色作为背景
    Rectangle:
        size:self.size
        pos:self.pos
        source：'back.jpg' #使用图片作为背景
```
### （2）图形绘制
新建kv文件：
```yaml
#主要是在最外层加上canvas属性
<RelativeWidget>: #一般为relativelayout下使用
    canvas：
        Color:
            rgba:[1,1,1,1] #使用纯色作为背景
        Rectangle: #绘制------方形，下面由图表说明
            size:self.width*0.2,self.height*0.15
            pos:self.x+10,self.y+10
            source：'back.jpg' #使用图片作为背景
```
绘制图形列表：
|图形|代码|
|----|----|
|矩形|Rectangle|
|椭圆|Ellipse|
|半圆|Ellipse：带上属性angel_start，angle_end角度|
|三角形|Ellipse:带上属性segments:3,或者使用Triangle:带上属性points为三个点坐标|
|多边形|Quad：带上points几个点坐标即可|
|点|Point，带上points属性为几个点的坐标，pointsize为点的大小|
|线|Line，带上points两点为起止点坐标|
|形状边界|使用Line，然后带上ellipse椭圆（位置x，位置y，长，宽，起点角度，终点角度，边数）width为线宽，circle圆（参数同理），rectangle矩形（x,y,长，宽），triangle三角形（三点坐标）|
### （3）canvas.before与canvas.after
新建canvas.kv
```yaml
<RelativeWidget>:
  canvas.before:
    Color:
      rgba:[1,1,1,1]
    Rectangle:
      pos:self.pos
      size:self.size

  canvas:
    Color:
      rgba:[.5,.1,.1,1]
    Rectangle:
      pos:self.pos
      size:300,300

  canvas.after:
    Color:
      rgba:[.1,.5,.1,1]
    Rectangle:
      pos:200,300
      size:300,300

  Button:
    text:'Button 0'
    pos:100,100
    size:300,300
    canvas.after:
      Color:
        rgba:[.1,.1,.5,1]
      Rectangle:
        pos:self.pos
        size:100,100
```
### (4)旋转与缩放
只需要在每个canvas中加入以下属性：
```yaml
<imagetest@image>:
    source:'test.png'
    pos:self.parent.pos
    size_hint:.5,.4
    canvas:
        PushMatrix  #保存上下文环境
        Roster: #旋转
            axis:(0,0,1)  #轴
            angle:60  #旋转角度
            origin:self.center  #旋转的点
        PopMatrix #恢复保存上下文环境
```
但是其中的图像并未旋转，所以需要加上canvas.before跟canvas.after
```yaml
<imagetest@image>:
    source:'test.png'
    pos:self.parent.pos
    size_hint:.5,.4
    canvas.before:
        PushMatrix
        Roster:
            axis:(0,0,1)  #轴
            angle:60  #旋转角度
            origin:self.center  #旋转的点

        Translate:    #平移
            x:100
            y:20
            z:0
        
        Scale:
            xyz:(1.25,1.25,0) #缩放

    canvas:
        Color:
            rgba:(1,1,1,1)
    canvas.after:
        Popmatrix
```






