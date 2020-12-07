# 1.使用系统指令
首先导入os，然后`os.system('')`就可以
```python
#该文件名称为test.py
import os

def test():
    os.system('ls')

test()
```
然后在命令行使用指令:`python3 test.py`就可以 
# 动态生成二维码：
```python
from MyQR import myqr

myqr.run(
    words='http://weixin.qq.com/r/kzlje9TEE4lsrZAY92yB',
    # 扫描二维码后，显示的内容，或是跳转的链接
    version=5,  # 设置容错率
    level='H',  # 控制纠错水平，范围是L、M、Q、H，从左到右依次升高
    picture='she.gif',  # 图片所在目录，可以是动图
    colorized=True,  # 黑白(False)还是彩色(True)
    contrast=1.0,  # 用以调节图片的对比度，1.0 表示原始图片。默认为1.0。
    brightness=1.0,  # 用来调节图片的亮度，用法同上。
    save_name='Python.gif',  # 控制输出文件名，格式可以是 .jpg， .png ，.bmp ，.gif
    )
```