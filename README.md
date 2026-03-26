# 《數位時代》Skills 庫

《數位時代》編輯團隊使用的 Claude Code Skill 集合，配合 [AI產稿中心 v2](https://www.notion.so/a438232ce0a94fe6b70d9f2e9199a32a) Notion 資料庫運作。

---

## 安裝

### 第一次安裝

```bash
git clone https://github.com/terrylee-lang/BN_next.git ~/.claude/agents
```

### 更新至最新版本

```bash
git -C ~/.claude/agents pull
```

---

## 前置需求

| 項目 | 說明 |
|------|------|
| Claude Code | 需登入 claude.ai 帳號 |
| Notion MCP | 在 Claude Code 設定中連接 Notion |
| Playwright MCP | `claude mcp add playwright npx @playwright/mcp@latest` |
| NOTION_TOKEN | 見下方說明 |

### 設定 NOTION_TOKEN

在 `~/.claude/settings.local.json` 的 `env` 區塊加入：

```json
{
  "env": {
    "NOTION_TOKEN": "請向 Terry 索取"
  }
}
```

Token 為公司內部整合金鑰，可供全體同仁共用，請向 Terry 索取，請勿外流至公司以外。

完整環境建置教學請參閱：[AI 自動產稿系統 — 環境建置教學](https://www.notion.so/32769f170dad819cac44cea570f03eb9)

---

## Skills 一覽

| 檔案 | Skill 名稱 | 用途 |
|------|-----------|------|
| `notion-orchestrator.md` | notion-orchestrator | 主腦：掃描 Notion 資料庫，自動派發對應 Skill 批次產稿 |
| `news-daily.md` | news-daily | 即時新聞編譯：外文新聞 → 繁體中文即時稿 |
| `deep-analysis.md` | deep-analysis | 深度分析：PDF / 長文 / 影片 → 深度商業分析文章 |
| `draft-polish.md` | draft-polish | 潤稿：AI 草稿 → 去 AI 味、符合編輯風格的正式稿件 |
| `social-post.md` | social-post | 社群貼文：文章 → Facebook / IG 貼文 |
| `article-checker.md` | article-checker | 文章查核：錯字、邏輯、事實查核 + 貨幣換算 |
| `tutorial-article.md` | tutorial-article | 教學文：X 方法論 / 工具技巧 → 教學型文章 |
| `headline-generator.md` | headline-generator | 標題生成：文章 → 多角度標題選項，含 SEO 推薦 |

---

## 觸發方式

在 Claude Code 對話框輸入：

```
跑今天的稿件
跑先泰的稿件
處理 Notion 的待辦
```

Claude 會自動掃描 AI產稿中心 v2，篩選「未開始」條目，依稿件類型派發對應 Skill，完成後寫回 Notion。

---

## 版本紀錄

> **更新標準流程**：每次更新 Skills 後，在此區塊補上版本號、日期、異動說明，再 commit & push。
> 版本號規則：新增 Skill → 次版號 +1（v2.x.0）；修改既有 Skill → 修訂號 +1（v2.x.x）。

---

### v2.3.1 — 2026-03-26

**修正**

- `news-daily`、`deep-analysis`、`tutorial-article`：`bwt-style-guide.md` 路徑從硬寫的 `/Users/terrylee/...` 改為 `~/.claude/agents/bwt-style-guide.md`，其他使用者安裝後可正常讀取
- `bwt-style-guide.md` 加入 repo，安裝後即可使用，不需額外手動複製

---

### v2.3.0 — 2026-03-25

**新增 Skill**

- `headline-generator`：新增標題生成 Skill，支援《數位時代》與《商周》兩種風格
  - 固定 5 種角度（企業轉型、人物決策、產業衝擊、數字驅動、工具教學）+ 動態補充角度
  - 工具教學型標題有獨立寫作規則（字數、感嘆號、利益承諾）
  - 輸出含首選/備選推薦理由，支援互動迭代

---

### v2.2.0 — 2026-03-24

**notion-orchestrator**

- 新增「建議標題」欄位支援（子代理動筆前優先採用編輯建議標題）
- 新增「社群貼文」、「字數」屬性欄位寫回
- 新增「負責人」欄位說明（支援「跑先泰的稿件」觸發模式）
- 稿件全文寫回策略改為 `replace_content`，解決含 code block 長文的 JSON 解析錯誤
- 新增 Bloomberg 付費內文特殊讀取路徑（使用 `bloomberg-fetch.js`），含 session 過期的處理說明
- 摘要欄位字數上限從「重點摘要」三合一格式拆分為獨立的「摘要」（75字）＋「重點一/二/三」（各50字）

---

### v2.1.0 — 2026-03-20

**notion-orchestrator**
- Step 1 改用 Notion API `curl` 查詢，取代原本無法列出條目的 `notion-fetch`
- 新增 `$NOTION_TOKEN` 環境變數支援
- 新增「負責人」篩選能力（支援「跑先泰的稿件」類觸發方式）
- 新增情境 A（指定負責人）/ 情境 B（全部待辦）兩種查詢模式

**環境設定**
- Playwright MCP 改用 persistent profile（`--user-data-dir`），保留登入 Cookie，解決 WSJ / Bloomberg 付費牆問題
- `settings.local.json` 新增常用工具 allow 清單，減少手動批准頻率

### v2.0.0 — 2026-03 以前

初始版本，包含 7 個 Skills 基礎架構。
