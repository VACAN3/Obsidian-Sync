#### 认识
###### 基于 Express 的服务端框架，可使用 TS，更加模块化。


#### 核心概念：
###### Controller
	控制器，接收客户端URL请求，调用对应 Service 并将 Service 响应返回给客户端，本身不做任何业务逻辑。
###### Service
	处理业务逻辑。
###### Module
	功能划分单元，把 Controller 和 Service 组织到一起，每个模块专注于一个独立的功能。
