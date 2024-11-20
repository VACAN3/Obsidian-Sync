[Fetch API - MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)
#### Fetch 流式请求
1. fetch API 在现代浏览器更加常用，提供了 Promise 支持、流传输等；不同于 `XMLHttpRequest` 请求（AJAX / Axios 基于此封装）
2. fetch 通过 `ReadableStream` 接口来逐块读取响应数据：
	1. 响应对象 Response 有一个 `body` 属性，该属性是一个 `ReadableStream` 流，允许被逐块读取，不必等待完整响应体被完全下载
	2. 调用 `response.body.getReader()` 来**按块**获取一个 `ReadableStreamReader`
	3. 调用 `reader.read()` 返回一个包含两个属性的对象：
		1. value: 当前读取的数据块
		2. done: 表示流是否已结束 `boolean`
	4. 可以循环调用 `read()`，直到 `done` 为 `true`，表示所有数据都已读取。
3. 取消请求：[`AbortController`](https://developer.mozilla.org/zh-CN/docs/Web/API/AbortController)（例如 ChatGPT 停止回答）
	1. [认识 AbortController控制器对象 及其应用](https://blog.csdn.net/qq_45560350/article/details/130588101)


##### 部署注意事项
开发完本以为万事大吉，线上却出现问题，项目部署在nginx web服务器上，需要把默认开启的缓存关掉，否则nginx代理会因为http缓存等待数据返回再统一返回给客户端，使用流式数据的目的就达不到，需要添加配置（缓存默认都是打开的，有条件的话，可以考虑，fetch流式请求单独走一个代理，避免影响其他请求数据量大没有缓存造成的影响）



![[image/Pasted image 20241023143318.png]]