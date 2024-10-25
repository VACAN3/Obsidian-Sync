1. Chrome 插件程序结构
```
manifest.json  --> 配置文件，定义扩展的基本信息、权限等。

background script  --> 在 manifest V3 中，采用 service worker 这种轻量化后台脚本，在需求要时被激活，在任务完成后自动停止。

content script --> 注入到网页中的js

popup --> 点击插件时显示的界面
```

2. 项目文件目录
![[Pasted image 20241021183639.png]]
	1. manifest.json
	2. 