最新版的已经使用 Go 语言重写，速度更快，安全加强，支持任意金额、任意方式的监听。

```
有需要的朋友我可以为你定制开发和部署，可加我微信 MCMenjoy 详谈。
```

此项目已不维护请不要使用在生产环境！

# springboot-pay-api

个人用户使用支付宝、微信收款实时到账监听系统

测试视频：[Bilibili](https://www.bilibili.com/video/av49083749/)  
演示地址：[测试页面](http://118.24.222.113/)

- APP 拉取当前公有的状态信息 CommonEntity 判断 hasOrder 字段真假，为真则等待五分钟后再进行支付操作
- 如果为假，表示当前没有正在支付的订单，即开个新订单，Task 开启五分钟后自动销毁订单，同时 hasOrder 为真表示其他用户不能进行支付操作
- 客户端监听到账信息，POST /submit/update 接口，即更新最近的订单 payed 字段为真，同时 hasOrder 为假，停止 Task
- APP 限制在五分钟内进行支付结果查询，GET /submit/last 接口，即查询最近的订单，判断 payed 字段表示是否支付

```
GET  /api/common                # 获取公有状态信息
POST /api/submit                # 新增订单
POST /api/submit/update         # 更新订单支付状态
GET  /api/submit/last           # 获取订单
POST /api/submit/destruction    # 取消当前正在支付的订单
```

逻辑查看 controller 层和 repository 层

### application.yaml

```
spring:
  data:
    mongodb:
      host: 127.0.0.1
      port: 27017
      database: pay-db
      username: # 数据库用户
      password: # 数据库密码
server:
  port: # API 端口

secret:
  api:    # /api/submit /api/submit/last 接口的 secret 字段
  order:  # /api/submit/update 接口的 secret 字段
```
### run

```
.\gradlew.bat build
.\gradlew.bat jar

java -jar .\build\libs\pay-0.0.1-SNAPSHOT.jar
```
