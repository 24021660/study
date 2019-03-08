# study
begin to learn new language(python) and how to use Github
django做的电商网站 关于坑： 
1.在订单提交的时候必须输入{% csrf_token %} 
2.关于models，在只能有一个autofield或者id找不到的时候，进行migrate是正确的选择 
3.templates中html找不到：settings的templates中的dir=[BASE_DIR+"/templates",],
4.model找不到，在app里面加上model的名称。
5.前端使用静态文件的时候，加上
