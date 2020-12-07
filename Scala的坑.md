## 1.helloworld
新建文件HelloWorld.scala然后输入一下内容：
```scala
object HelloWorld{
    def main(args: Array[String]) : Unit={
        println("helloscala")
    }
}
```
## 2.scala发送http请求
添加依赖
```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.32</version>
</dependency>

<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpcore</artifactId>
    <version>4.4.9</version>
</dependency>

<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.5</version>
</dependency>
```
get请求
```java
def getResponse(url: String, header: String = null): String = {
    val httpClient = HttpClients.createDefault()    // 创建 client 实例
    val get = new HttpGet(url)    // 创建 get 实例

    if (header != null) {   // 设置 header
      val json = JSON.parseObject(header)
      json.keySet().toArray.map(_.toString).foreach(key => get.setHeader(key, json.getString(key)))
    }

    val response = httpClient.execute(get)    // 发送请求
    EntityUtils.toString(response.getEntity)    // 获取返回结果
  }

```
post请求
```java
def postResponse(url: String, params: String = null, header: String = null): String ={
    val httpClient = HttpClients.createDefault()    // 创建 client 实例
    val post = new HttpPost(url)    // 创建 post 实例

    // 设置 header
    if (header != null) {
      val json = JSON.parseObject(header)
      json.keySet().toArray.map(_.toString).foreach(key => post.setHeader(key, json.getString(key)))
    }

    if (params != null) {
      post.setEntity(new StringEntity(params, "UTF-8"))
    }

    val response = httpClient.execute(post)    // 创建 client 实例
    EntityUtils.toString(response.getEntity, "UTF-8")   // 获取返回结果
  }
```