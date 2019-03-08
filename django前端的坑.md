# study
begin to learn new language(python) and how to use Github
django做的电商网站 关于坑： 
1.在订单提交的时候必须输入{% csrf_token %} 
2.关于models，在只能有一个autofield或者id找不到的时候，进行migrate是正确的选择 
3.templates中html找不到：settings的templates中的dir=[BASE_DIR+"/templates",],
4.model找不到，在app里面加上model的名称。
5.前端使用静态文件的时候，加上
  STATICFILES_DIRS = [
      os.path.join(BASE_DIR, "static"),
  ]
  并在根目录新建文件夹：static
6.网页使用html的时候，加上{% load staticfiles %},并且所有的静态文件如css，js等，地址都要写成<link rel="stylesheet" href="{% static 'layui/css/layui.css' %}">的形式。
