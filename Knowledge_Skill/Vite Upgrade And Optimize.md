[官方文档](https://cn.vitejs.dev/)

## Vite 4 升级为 Vite 5
1. **Why**：
	1. Vite 5.0 于2023.11 发布，技术稳定且社区活跃，当前 Vite 已经迭代至 Vite 6 版本，若依已于 **v3.8.7** 版本开始使用 Vite 5
	2. Vite 5 集成了 Rollup 4，支持 Node 18，增强对原生 ESM 模块的处理，整体性能进一步优化（开发服务器的冷启动时间减少约 20%；HMR 速度也提升了约 15%；代码错误提示更加清晰具体等等）
2. typescript4 —> typescript5

#### 一、通过插件配置，升级 Vite 能力
1.  [@vitejs/plugin-vue-jsx](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue-jsx) Vue3 JSX 能力支持
	1. 当前 Vite 4 项目支持 plugin-vue-jsx 3 版本（Vite4 —> plugin-vue-jsx 3; Vite5+ —> plugin-vue-jsx 4+）