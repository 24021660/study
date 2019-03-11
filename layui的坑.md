# study
begin to learn new language(python) and how to use Github

1.layui使用中的坑，table中reload中的where原来是以部传输，需要在后端进行get接收后进行处理。
2.关于在js中的转义字符，如将字符串ctx['rlt']输入到js中，则前端应该为{{rlt|safe}}
3.关于下拉框，需要加上layui.form的js才行
4.关于流加载需要先用user加载flow，然后需要在想要复制的图标的父节点id进行绑定，然后就可以使用了（字符串换行用‘\’）
  layui.use(['flow'],function(){
var flow=layui.flow;
  var m=[];
flow.load({
    elem: '#LAY_demo1' //流加载容器
    ,scrollElem: '#LAY_demo1' //滚动条所在元素，一般不用填，此处只是演示需要。
    ,done: function(page, next){ //执行下一页的回调

      //模拟数据插入

      setTimeout(function(){
        var lis = m
        for(var i = 0; i < 8; i++){
          lis.push('<div class="layui-col-md6" lay-filter="demo">\
      <div class="layui-card">\
        <div class="layui-card-header">\
          卡片面板\
        </div>\
        <div class="layui-card-body">\
          卡片式面板面板通常用于非白色背景色的主体内<br>\
          从而映衬出边框投影\
        </div>\
      </div>\
    </div>')
        }
        //执行下一页渲染，第二参数为：满足“加载更多”的条件，即后面仍有分页
        //pages为Ajax返回的总页数，只有当前页小于总页数的情况下，才会继续出现加载更多
        next(lis.join(''), page < 10); //假设总页数为 10
      }, 500);
    }
  });
5.关于向后端传输数据：用ajax向后端页面userinfotable发送数据。
  xmlhttp=new XMLHttpRequest();           xmlhttp.open("GET","/userinfotable/page=1&limit=10&keyword="+key+"&editvalue=1&username=22222&password="+data['password']+"&test="+data);
  xmlhttp.send();
6.关于表格的edit，监听用on，例如
table.on('edit(test)', function(obj){ //注：edit是固定事件名，test是table原始容器的属性 lay-filter="对应的值"
  layer.msg('编辑操作');
  //obj.value为修改后的值
  //obj.field为字段名称
});
 
