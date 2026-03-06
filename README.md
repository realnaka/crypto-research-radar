# Crypto Research Radar

加密货币行业调研 Skill。监控 Perp DEX、交易所、预测市场、AI×Crypto、DeFi 协议的最新产品发布和功能更新。

安装到 Cursor 或 Claude Code 后，直接对话即可获取调研报告。

## 安装

### Cursor

```bash
cd ~/.cursor/skills/
git clone https://github.com/realnaka/crypto-research-radar.git
```

### Claude Code

```bash
cd ~/.claude/skills/
git clone https://github.com/realnaka/crypto-research-radar.git
```

安装后重启 IDE，Skill 自动加载。

## 配置 API Key

本 Skill 使用 [6551](https://6551.io/mcp) 的 OpenNews 和 OpenTwitter API 获取数据。

### 获取 Token

1. 访问 https://6551.io/mcp
2. 注册并获取 API Token

### 设置方式（二选一）

**方式 A：环境变量**

```bash
export OPENNEWS_TOKEN="你的token"
```

可以写入 `~/.zshrc` 或 `~/.bashrc` 使其持久化。

**方式 B：MCP Server 配置（推荐）**

在 `~/.cursor/mcp.json` 中添加：

```json
{
  "mcpServers": {
    "opennews": {
      "command": "uv",
      "args": ["--directory", "/path/to/opennews-mcp", "run", "opennews-mcp"],
      "env": {
        "OPENNEWS_TOKEN": "你的token"
      }
    },
    "opentwitter": {
      "command": "uv",
      "args": ["--directory", "/path/to/opentwitter-mcp", "run", "twitter-mcp"],
      "env": {
        "TWITTER_TOKEN": "你的token"
      }
    }
  }
}
```

MCP Server 源码：
- https://github.com/6551Team/opennews-mcp
- https://github.com/6551Team/opentwitter-mcp

### Clawfeed（可选）

[Clawfeed](https://github.com/kevinho/clawfeed) 的公共 API 无需认证，Skill 会自动调用。

## 使用

安装配置完成后，在 Cursor / Claude Code 中直接对话：

- "帮我调研一下过去一天加密圈有什么新动态"
- "Perp DEX 最近有什么更新？"
- "交易所推出了什么新功能？"
- "预测市场有什么变化？"
- "跑一下今天的行业日报"

## 自定义

### 监控列表

编辑 `references/competitors.md` 可以增减监控的项目和 Twitter 账号。

### 重要性标准

编辑 `SKILL.md` 中的"重要性分级"部分，可以根据自己的业务调整哪些动态标记为重要。默认配置关注 Perp DEX 竞品和行业大事件。

### 输出格式

编辑 `references/output_templates.md` 中的模板，可自定义报告格式。

## 配套自动化

如需每日定时生成报告并推送到 Telegram，可配合自动化脚本使用。详见 SKILL.md 中的"自动化配套"部分。

## License

MIT
