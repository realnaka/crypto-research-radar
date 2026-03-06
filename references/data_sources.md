# 数据源 API 速查

所有数据源的 API 端点、参数和返回格式速查。

---

## 1. 6551 OpenNews API

**Base URL**: `https://ai.6551.io`
**认证**: `Authorization: Bearer {OPENNEWS_TOKEN}`

### POST /open/news_search

搜索加密新闻。

**请求体**:
```json
{
  "q": "搜索关键词",
  "limit": 20,
  "page": 1
}
```

**返回示例**（数组）:
```json
[
  {
    "text": "新闻正文内容...",
    "url": "https://来源链接",
    "newsType": "Twitter | tree | News Summary | Coindesk | ...",
    "aiRating": {
      "score": 80,
      "summary": "AI 生成的中文摘要",
      "signal": "long | short | neutral"
    },
    "coins": [
      {"symbol": "BTC"},
      {"symbol": "ETH"}
    ],
    "twitterUserName": "用户名（仅 Twitter 类型）",
    "tweetId": "推文ID（仅 Twitter 类型）"
  }
]
```

**构造来源链接**:
- 如果 `url` 存在，直接使用
- 如果 `newsType` == "Twitter"，拼接：`https://x.com/{twitterUserName}/status/{tweetId}`

**推荐搜索关键词组**:

| 类别 | 关键词 |
|------|--------|
| DeFi | `"DeFi new launch product feature"` |
| Perp DEX | `"perpetual DEX new feature launch update"` |
| 交易所 | `"exchange launch new feature Binance Coinbase OKX"` |
| 预测市场 | `"prediction market Polymarket new"` |
| AI×Crypto | `"AI agent crypto onchain wallet"` |

---

## 2. 6551 OpenTwitter API

**Base URL**: `https://ai.6551.io`
**认证**: `Authorization: Bearer {OPENNEWS_TOKEN}`（与 OpenNews 使用同一个 Token）

### POST /open/twitter_search

按关键词搜索推文。

**请求体**:
```json
{
  "keywords": "搜索关键词或 from:username",
  "maxResults": 10,
  "product": "Latest",
  "excludeReplies": true
}
```

`product` 取值：`"Latest"`（最新）或 `"Top"`（热门）

**返回示例**:
```json
{
  "data": [
    {
      "text": "推文内容...",
      "userScreenName": "HyperliquidX",
      "favoriteCount": 1999,
      "retweetCount": 500,
      "createdAt": "Wed Feb 18 13:40:00 +0000 2026",
      "id": "2024116702154473737"
    }
  ]
}
```

**构造推文链接**: `https://x.com/{userScreenName}/status/{id}`

### POST /open/twitter_user_info

获取用户资料。

**请求体**: `{"screenName": "HyperliquidX"}`

### POST /open/twitter_user_tweets

获取用户最新推文。

**请求体**: `{"screenName": "HyperliquidX", "maxResults": 5}`

返回格式同 twitter_search 的 data 数组。

**搜索技巧**:
- 用 `from:username` 搜索特定账号的推文
- 用 `"Latest"` 获取最新内容（时效性调研必须用这个）
- 用 `"Top"` 获取高互动内容
- `excludeReplies: true` 过滤回复，只看原创推文

---

## 3. Clawfeed API

**Base URL**: `https://clawfeed.kevinhe.io`
**认证**: 只读模式无需认证

### GET /api/digests

获取 AI 生成的新闻摘要。

**参数**:
- `type`: `4h` | `daily` | `weekly` | `monthly`
- `limit`: 返回数量（默认 10）

**示例**:
```bash
curl -s "https://clawfeed.kevinhe.io/api/digests?type=daily&limit=1"
```

**返回示例**:
```json
[
  {
    "created_at": "2026-03-05T23:59:00",
    "content": "# ClawFeed 日报 | 2026-03-05\n\n## 今日头条\n..."
  }
]
```

`content` 是完整的 Markdown 格式日报，包含：
- 头条新闻（5 条左右）
- Feed 精选 Top 10（含推文链接）
- 推荐关注 / 建议取关
- 趋势观察

**注意**:
- Clawfeed 日报覆盖面广但偏向 AI/Tech，加密部分需要自行筛选
- 日报中的推文链接格式为 `https://x.com/user/status/id`，可直接使用
- 此为第三方 demo 服务，可能出现不可用的情况

---

## 调用最佳实践

1. **并行调用**：三个数据源互相独立，应该并行请求以节省时间
2. **容错处理**：任一数据源失败时跳过，用其他数据源补充
3. **去重**：同一事件可能在多个数据源出现，以最详细的版本为准
4. **链接验证**：确保拼接的 Twitter 链接格式正确
5. **时效判断**：优先信任 `createdAt` 字段，其次看文本中的时间表述
