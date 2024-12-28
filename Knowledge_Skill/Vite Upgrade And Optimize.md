[官方文档](https://cn.vitejs.dev/)

## Vite 4 升级为 Vite 5
1. **Why**：
	1. Vite 5.0 于2023.11 发布，技术稳定且社区活跃，当前 Vite 已经迭代至 Vite 6 版本，若依已于 **v3.8.7** 版本开始使用 Vite 5
	2. Vite 5 集成了 Rollup 4，支持 Node 18，增强对原生 ESM 模块的处理，整体性能进一步优化（开发服务器的冷启动时间减少约 20%；HMR 速度也提升了约 15%；代码错误提示更加清晰具体等等）
2. typescript4 —> typescript5

#### 一、通过插件配置，升级 Vite 能力
1.  [@vitejs/plugin-vue-jsx](https://github.com/vitejs/vite-plugin-vue/tree/main/packages/plugin-vue-jsx) Vue3 JSX 能力支持（需安装[Babel 转换插件](https://github.com/vuejs/babel-plugin-jsx/blob/main/packages/babel-plugin-jsx/README-zh_CN.md) @vue/babel-plugin-jsx）
	1. 当前 Vite 4 项目支持 plugin-vue-jsx 3 版本（Vite4 —> plugin-vue-jsx 3; Vite5+ —> plugin-vue-jsx 4+）
```
pnpm add @vitejs/plugin-vue-jsx -D
// vite.config.js
import vueJsx from '@vitejs/plugin-vue-jsx'
export default {
  plugins: [
    vueJsx({
      // options are passed on to @vue/babel-plugin-jsx
    }),
  ],
}

pnpm add @vue/babel-plugin-jsx -D
// babel.config.js
module.exports = {
  env: {
    development: {
      plugins: ['@vue/babel-plugin-jsx'],
    },
  },
};

// .eslintrc.js
module.exports = {
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
  },
};

// tsconfig.json
{
  "compilerOptions": {
    "jsx": "preserve", // 保留 JSX 代码不进行转换，让上面配置的 Babel 来处理 JSX 转换
  },
}

// 使用方式
// 方式一：vue 模板组件
// TsxDemo.vue
<script lang="tsx">
export default defineComponent({
  name: 'TsxDemo', // jsx/tsx
  props: {
    name: type: String,
  },
  setup(props, ctx) {
    return () => <div>Hello World {props.name}</div>;
  },
});
</script>

// 方式二：函数式组件
// TsxDemo.tsx
export default (props, ctx) => <div>Hello World from jsx</div>;
```
