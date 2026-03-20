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
