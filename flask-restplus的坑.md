# 1.关于安装后出现
查询后得知这是因为werkzeug新版本里需要显式导入该模块
在报错的文件（我这里是werkzeug/init.py）里添加以下代码可以解决该问题
```
from werkzeug.utils import cached_property
```