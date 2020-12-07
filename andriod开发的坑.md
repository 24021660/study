# study
begin to learn new language and how to use Github
java语言
混合开发用webview
全屏：
防止横竖屏
禁止方法：
修改AndroidManifest.xml，把所有的项都加上
android:configChanges=”orientation”



最近在做Android的播放器，遇到采用以上方法后，仍然出现屏幕旋转后触发Activity.onCreate，经查阅资料后，发现需要添加screenSize处理

具体代码为：


android:configChanges="orientation|keyboardHidden|screenSize"
