# How to Install Lara Translate MCP Server into Claude AI Desktop App

This article will guide you through the process of installing the Lara Translate MCP Server into your Claude AI Desktop application on macOS. While the tutorial focuses on Mac installation, a similar process can be followed for Windows users.

It builds on the basic MCP installation steps of a MCP server into Claude AI Desktop (details [here](https://modelcontextprotocol.io/quickstart/user)) and includes specific instructions tailored for integrating the Lara translation service.

### Prerequisites

- A Claude AI account
- Claude AI Desktop application installed on your Mac
- [Node.js](https://nodejs.org/en/download) installed on your Mac (version 22 or superior)

### Step 1: Install Claude AI Desktop

![claude-ai-desktop](https://support.laratranslate.com/hs-fs/hubfs/claude-ai-desktop.webp?width=670&height=377&name=claude-ai-desktop.webp)

1. [Create a Claude AI account](https://claude.ai/) or log into your existing account
2. [Download the Claude AI Desktop](https://claude.ai/download) application from the official website
3. Install the application on your Mac
4. Launch and log into your account from the desktop app

### Step 2: Access Configuration Settings

![claude-ai-open-configuration-file](https://support.laratranslate.com/hs-fs/hubfs/claude-ai-open-configuration-file.webp?width=670&height=377&name=claude-ai-open-configuration-file.webp)

1. **Open** the **Claude AI Desktop** app
2. **Go to** the **main app menu** and **click** "Claude" > "**Settings**..."
3. In the Settings window, **click** "**Developer**" and **then** "**Edit Config**"
4. This will open a folder and highlight the Claude AI configuration file
5. **Open** the Claude AI **configuration file** with any text editor

### Step 3: Add Lara Translate MCP Configuration

![lara-translate-integration-claude-ai-desktop](https://support.laratranslate.com/hs-fs/hubfs/lara-translate-integration-claude-ai-desktop.webp?width=670&height=377&name=lara-translate-integration-claude-ai-desktop.webp)

1. **Copy** the **Lara Translate configuration text** below  
      
    `{`  
      `"mcpServers": {`  
        `"lara-translate": {         "command": "npx",         "args": [           "-y",           "@translated/lara-mcp@latest"         ],         "env": {           "LARA_ACCESS_KEY_ID": "**<YOUR_ACCESS_KEY_ID>**",           "LARA_ACCESS_KEY_SECRET": "**<YOUR_ACCESS_KEY_SECRET>**"         }       }     }   }`
2. **Paste it into the configuration file** you opened
3. **Add** **your Lara Translate API credentials**
4. **Save** the configuration **file**

If you have other MCP Servers activate on your Claude AI, just append the text below inside the `mcpServers` section.

`"lara-translate": {         "command": "npx",         "args": [           "-y",           "@translated/lara-mcp@latest"         ],         "env": {           "LARA_ACCESS_KEY_ID": "**<YOUR_ACCESS_KEY_ID>**",           "LARA_ACCESS_KEY_SECRET": "**<YOUR_ACCESS_KEY_SECRET>**"         }       }`

Make sure to add a comma after the last mcp server.

Replace `**<YOUR_ACCESS_KEY_ID>**` and **`<YOUR_ACCESS_KEY_SECRET>`** with your Lara Translate API credentials  
  
See example below:

![lara-translate-integration-claude-ai-desktop-with-other-mcp](https://support.laratranslate.com/hs-fs/hubfs/lara-translate-integration-claude-ai-desktop-with-other-mcp.webp?width=670&height=377&name=lara-translate-integration-claude-ai-desktop-with-other-mcp.webp)

### Step 4: Activate and Test

1. **Restart** the **Claude AI Desktop** application
2. When the app reopens, **you should see a hammer icon** indicating that MCP is active
3. **Test** the **Lara Translate functionality**

**![testing-lara-translate-integration-claude-ai](https://support.laratranslate.com/hs-fs/hubfs/testing-lara-translate-integration-claude-ai.webp?width=670&height=377&name=testing-lara-translate-integration-claude-ai.webp)**

After completing these steps, your Claude AI Desktop application should now be integrated with Lara Translate MCP Server, allowing you to use translation capabilities within the Claude interface.

### Troubleshooting

If you encounter any issues during installation, verify that:

- Your Node.js installation is working correctly
- You've the latest version of Node.js installed
- Having multiple versions of Node.js can cause the MCP Server to fail
- You've correctly entered your Lara API credentials
- The configuration file is properly formatted
- You've restarted the Claude AI Desktop application after making changes

Enjoy using Lara Translate with your Claude AI Desktop application!

### Useful links

- [](https://github.com/translated/lara-mcp)[Claude AI MCP configuration guide](https://modelcontextprotocol.io/quickstart/user)[](https://github.com/translated/lara-mcp)
- [Nodejs installation file and guide](https://nodejs.org/en/download)
- [Lara Translate MCP Server Code](https://github.com/translated/lara-mcp)
- [How to Generate an API Key for Lara translate](https://support.laratranslate.com/en/api-key-for-laras-api?hsLang=en)
- [Lara Translate API documentation](https://developers.laratranslate.com/docs/introduction)
- [Model Context Protocol – A Guide for AI and Multi-Agent Systems](https://blog.laratranslate.com/model-context-protocol/)
- [What Should an MCP Server for Language Translation Do?](https://blog.laratranslate.com/translation-mcp-server/)