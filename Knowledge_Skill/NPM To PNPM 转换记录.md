### 一、[WHY（PNPM 优势）](https://pnpm.io/zh/motivation)
1. **磁盘空间更节省**：
	- PNPM 通过硬链接和符号链接在全局存储中共享依赖包，避免重复安装。
	- 如果项目中依赖项存在多个版本，只会将不同版本间有差异的文件添加到仓库，而不会因为单个改变而克隆多一整个依赖。
2. **安装速度更快**：
	- 通过创建链接而不是复制文件。
	- 并行安装依赖。
3. **依赖管理更严格**：
	- pnpm 创建了非扁平的 node_modules 结构，只有直接依赖会出现在 node_modules 的顶层，确保每个包只能访问其显式声明的依赖，避免了幽灵依赖问题（访问未在 package.json 中声明的依赖）



![](Knowledge_Skill/Images/4382FB4A-C70A-47c1-A6F9-7BF95257DB45.png)
# build-job-test
![](Knowledge_Skill/Images/Pasted%20image%2020241226163846.png)

