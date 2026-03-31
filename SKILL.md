---
name: crypto-research-radar
description: 加密货币行业调研雷达。监控 Perp DEX、交易所、预测市场、AI×Crypto、DeFi 协议的最新产品发布和功能更新。通过 6551 OpenNews/OpenTwitter API 和 Clawfeed 获取数据，生成结构化调研报告。触发场景包括："最近有什么新动态"、"帮我调研一下"、"过去一天 DeFi 有什么新产品"、"Perp DEX 最近有什么更新"、"交易所推出了什么新功能"、"预测市场有什么变化"、"AI crypto"、"行业日报"。
---

# Crypto Research Radar

加密货币行业新产品/新功能调研技能。聚焦"什么项目推出了什么新东西"，而非价格分析或投资建议。

## 数据源

本技能依赖三个外部数据源。调用前检查可用性，不可用时跳过并标注。

### 1. 6551 OpenNews API

加密新闻聚合，自带 AI 评分和中文摘要。

```bash
curl -s -X POST \
  -H "Authorization: Bearer $OPENNEWS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"q":"搜索关键词","limit":20,"page":1}' \
  "https://ai.6551.io/open/news_search"
```

**返回字段**：`text`、`url`、`ts`（ISO 时间戳）、`newsType`、`aiRating.score`（0-100）、`aiRating.summary`、`aiRating.signal`、`coins`

**时间过滤**：使用 `ts` 字段过滤，只保留 24 小时内的数据。

### 2. 6551 OpenTwitter API

推特搜索和用户推文获取。

```bash
# 关键词搜索
curl -s -X POST \
  -H "Authorization: Bearer $OPENNEWS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"keywords":"搜索词","maxResults":10,"product":"Latest","excludeReplies":true}' \
  "https://ai.6551.io/open/twitter_search"
```

**返回字段**：`text`、`userScreenName`、`favoriteCount`、`retweetCount`、`createdAt`（如 `Wed Feb 18 13:40:00 +0000 2026`）、`id`

**时间过滤**：解析 `createdAt` 字段，只保留 24 小时内的推文。

### 3. Clawfeed API

AI 生成的新闻聚合摘要，覆盖广。

```bash
curl -s "https://clawfeed.kevinhe.io/api/digests?type=daily&limit=1"
```

---

## 监控列表

### Perp DEX
Twitter 账号：`@HyperliquidX` `@GMX_IO` `@dYdX` `@DriftProtocol` `@vertex_protocol` `@OstiumLabs` `@lighter_xyz` `@JupiterExchange` `@paradex_app` `@grvt_io` `@variational_io` `@StandX_Official` `@BasedOneX` `@extendedapp` `@pacifica_fi` `@tryliquid` `@o1_exchange` `@nadoHQ`

### 交易所
Twitter 账号：
- Binance 系：`@binance` `@binancezh` `@BinanceWallet` `@TrustWallet` `@heyibinance` `@cz_binance`
- OKX 系：`@okx` `@okxchinese`
- Gate 系：`@Gate` `@gate_io` `@Gate_zh`
- 其他：`@coinbase` `@BackpackExchange` `@Bybit_Official` `@MEXC_Official` `@Bitget_zh` `@BingXOfficial` `@tradexyz`

### 预测市场
Twitter 账号：`@Polymarket` `@MyriadMarkets` `@Kalshi` `@predictdotfun` `@opinionlabsxyz`

### AI × Crypto
Twitter 账号：`@ai16zdao` `@elizaOS` `@Phala_Network` `@SaharaLabsAI`
关键词："crypto MCP"、"exchange MCP"、"exchange AI agent"、"exchange CLI"、"wallet AI agent"、"crypto AI skills"、"Gate for AI"

**分类特别说明**：AI × Crypto 不是按发布者归属，而是按内容主题分类。任何公司（交易所、钱包、DeFi 协议等）发布的与 AI 技术相关的动态都归入此类。判断标准：内容涉及 MCP Server、CLI 工具、AI Agent、AI Skills、AI 接口开放、AI 驱动的产品功能。

### DeFi 协议
Twitter 账号：`@AaveAave` `@LidoFinance` `@MorphoLabs` `@CurveFinance` `@0xfluid` `@VenusProtocol` `@ResolvLabs` `@pendle_fi` `@ethena_labs` `@solana` `@kamino` `@Pumpfun` `@Dreamcash`

---

## 调研工作流

当用户触发调研请求时，按以下步骤执行：

### Step 1: 确定调研范围

- **时间范围**：默认"过去 24 小时"，用户可指定
- **覆盖类别**：默认全部，输出顺序为 Perp DEX → 交易所 → 预测市场 → AI×Crypto → DeFi 协议
- **深度**：快速扫描（10 分钟）vs 深度调研（30 分钟）

### Step 2: 并行数据拉取

从三个数据源获取数据，使用上述监控列表中的账号和关键词。

### Step 3: 时效过滤（关键步骤）

对每条信息严格判断：
- **保留**：明确在时间范围内发生的新事件（产品发布、功能上线、TGE、合作公告、治理投票通过、重大人事变动）
- **丢弃**：项目一般性介绍、超出时间范围的旧新闻、营销推广、价格预测、交易建议

**判断依据**：推文 `createdAt` 字段、新闻 `ts` 字段、AI 评分 score

### Step 4: 重要性分级

🔴 **重要** — 满足以下任一条件：
- 与自身业务直接相关的竞品动态（可在此处自定义，例如：Perp DEX 竞品、预测市场、钱包架构等）
- Perp DEX 头部项目的重大动态（Hyperliquid、dYdX、GMX、Vertex、Drift 等）
- 高讨论度/高互动量的行业大事件（大额融资、重大安全事故、监管突变等）

🔵 **常规** — 其他符合时效性的新事件

> 可根据团队业务方向自定义重要性标准。

### Step 5: 分类整理

每条信息必须包含：
- 事件描述（一句话）
- 具体内容（关键细节）
- 来源链接（至少一个可点击的 URL）
- 重要性标记（🔴 或 🔵）

### Step 6: 格式化输出

使用分隔线和 emoji 区分板块。参考 `references/output_templates.md`。

---

## 质量检查清单

- [ ] 每条信息都在要求的时间范围内（核对时间戳）
- [ ] 每条信息都有至少一个来源链接
- [ ] 没有混入项目一般性介绍
- [ ] 没有混入营销内容或付费推广
- [ ] AI × Crypto 分类基于内容主题而非发布者
- [ ] 重要性标记准确

---

## 自动化配套

本 Skill 配套 GitHub Actions 自动化方案：[crypto-daily-report](https://github.com/realnaka/crypto-daily-report)
- 每天早上 9:00（UTC+8）自动运行
- 支持同时推送到 Telegram 频道和 Lark（飞书）群
- 修改 `config/search_queries.yaml` 可调整监控列表
