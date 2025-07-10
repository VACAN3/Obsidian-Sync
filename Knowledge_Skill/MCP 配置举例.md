# 如何在 Claude AI 桌面应用中安装 Lara Translate MCP 服务器

本文将指导您在 macOS 上将 Lara Translate MCP 服务器安装到 Claude AI Desktop 应用程序中 。本教程主要介绍 Mac 系统的安装，但 Windows 用户也可以遵循类似的步骤。

它以 MCP 服务器到 Claude AI Desktop 的基本 MCP 安装步骤为基础（详情请见[此处](https://modelcontextprotocol.io/quickstart/user)），并包含针对集成 Lara 翻译服务而定制的具体说明。

### 先决条件

- 克劳德人工智能账户
- 您的 Mac 上已安装 Claude AI Desktop 应用程序
- [您的 Mac 上安装了Node.js](https://nodejs.org/en/download)（版本 22 或更高版本）

### 步骤1：安装Claude AI Desktop

![克劳德-AI-桌面](https://support.laratranslate.com/hs-fs/hubfs/claude-ai-desktop.webp?width=670&height=377&name=claude-ai-desktop.webp)

1. [创建 Claude AI 帐户](https://claude.ai/)或登录您现有的帐户
2. [](https://claude.ai/download)从官方网站[下载Claude AI Desktop应用程序](https://claude.ai/download)
3. 在 Mac 上安装应用程序
4. 从桌面应用程序启动并登录您的帐户

### 第 2 步：访问配置设置

![克劳德-AI-打开配置文件](https://support.laratranslate.com/hs-fs/hubfs/claude-ai-open-configuration-file.webp?width=670&height=377&name=claude-ai-open-configuration-file.webp)

1. **打开****Claude AI 桌面**应用程序
2. **转到****主应用程序菜单**并单击**“** Claude”>“**设置**...”
3. 在“设置”窗口中，**单击**“**开发人员**”，然后**单击**“**编辑配置**”
4. 这将打开一个文件夹并突出显示 Claude AI 配置文件
5. 使用任何文本编辑器**打开**Claude AI**配置文件**

### 步骤 3：添加 Lara Translate MCP 配置

![lara-translate-integration-claude-ai-desktop](https://support.laratranslate.com/hs-fs/hubfs/lara-translate-integration-claude-ai-desktop.webp?width=670&height=377&name=lara-translate-integration-claude-ai-desktop.webp)

1. **复制**下面的**Lara Translate 配置** 文本  
      
    `{`  
      `"mcpServers": {`  
        `"lara-translate": {         "command": "npx",         "args": [           "-y",           "@translated/lara-mcp@latest"         ],         "env": {           "LARA_ACCESS_KEY_ID": "**<YOUR_ACCESS_KEY_ID>**",           "LARA_ACCESS_KEY_SECRET": "**<YOUR_ACCESS_KEY_SECRET>**"         }       }     }   }`
2. 将其粘贴到您打开的**配置文件中**
3. **添加** **您的 Lara Translate API 凭据**
4. **保存**配置**文件  
    **

如果您在 Claude AI 上激活了其他 MCP 服务器，只需在该部分内附加以下文本即可`mcpServers`。

`"lara-translate": {         "command": "npx",         "args": [           "-y",           "@translated/lara-mcp@latest"         ],         "env": {           "LARA_ACCESS_KEY_ID": "**<YOUR_ACCESS_KEY_ID>**",           "LARA_ACCESS_KEY_SECRET": "**<YOUR_ACCESS_KEY_SECRET>**"         }       }`

确保在最后一个 mcp 服务器后添加逗号。

 用您的 Lara Translate API 凭证替换 `**<YOUR_ACCESS_KEY_ID>**` 和 参见下面的示例：**`<YOUR_ACCESS_KEY_SECRET>`**  
  

![lara-translate-claude-ai-desktop 与其他 mcp 的集成](https://support.laratranslate.com/hs-fs/hubfs/lara-translate-integration-claude-ai-desktop-with-other-mcp.webp?width=670&height=377&name=lara-translate-integration-claude-ai-desktop-with-other-mcp.webp)

### 步骤4：激活并测试

1. **重新启动****Claude AI Desktop**应用程序
2. 当应用程序重新打开时，**您应该会看到一个锤子图标，**表示 MCP 处于活动状态
3. **测试**Lara**翻译功能**

**![测试-lara-translate-integration-claude-ai](https://support.laratranslate.com/hs-fs/hubfs/testing-lara-translate-integration-claude-ai.webp?width=670&height=377&name=testing-lara-translate-integration-claude-ai.webp)**

完成这些步骤后，您的 Claude AI Desktop 应用程序现在应该与 Lara Translate MCP Server 集成，从而允许您在 Claude 界面中使用翻译功能。

### 故障排除

如果在安装过程中遇到任何问题，请验证：

- 您的 Node.js 安装正常运行
- 您已安装最新版本的 Node.js
- Node.js 的多个版本可能会导致 MCP 服务器失败
- 您已正确输入 Lara API 凭据
- 配置文件格式正确
- 您在进行更改后重新启动了 Claude AI Desktop 应用程序

享受将 Lara Translate 与 Claude AI Desktop 应用程序一起使用的乐趣！

### 有用的链接

- [](https://github.com/translated/lara-mcp)[Claude AI MCP 配置指南](https://modelcontextprotocol.io/quickstart/user)[](https://github.com/translated/lara-mcp)
- [Nodejs安装文件及指南](https://nodejs.org/en/download)
- [Lara 翻译 MCP 服务器代码](https://github.com/translated/lara-mcp)
- [如何为 Lara 翻译生成 API 密钥](https://support.laratranslate.com/en/api-key-for-laras-api?hsLang=en)
- [Lara 翻译 API 文档](https://developers.laratranslate.com/docs/introduction)
- [模型上下文协议——人工智能和多智能体系统指南](https://blog.laratranslate.com/model-context-protocol/)
- [用于语言翻译的 MCP 服务器应该做什么？](https://blog.laratranslate.com/translation-mcp-server/)

---

### 本文是关于

- 在 Claude AI Desktop 中安装 Lara Translate MCP Server 的分步指南
- 配置桌面应用程序以集成翻译功能的说明
- 从基本 MCP 设置到添加 Lara API 凭据的完整过程