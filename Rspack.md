
Rspack 是用 Rust 语言开发的应用，用来取代老牌的 JS 构建打包工具 Webpack，耗时大概是 Webpack 的十分之一，且可以**无缝替换**。属于字节开源出来的工具。
由于全盘继承 Webpack，Rspack 也同时继承了前者的体验问题：配置麻烦，上手不算容易。开发团队为了解决这个问题，**在 Rspack 的基础上，封装了一系列更简单易用的衍生工具：**

- [Rsbuild](https://rsbuild.dev/zh/): 专注于构建 Web 应用。
- [Rslib](https://rspress.dev/zh/): 专注于构建 JS 软件包。
- [Rspress](https://rspress.dev/zh/)：专注于生成静态站点，比如文档和博客。
- [Rsdoctor](https://rsdoctor.dev/zh/)：专注于构建分析。

▲ 以上这些工具，底层都是 Rspack，分别用于不同的用途，统称为 **Rspack 工具栈**。