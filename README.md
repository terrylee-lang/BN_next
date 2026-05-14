# 《數位時代》Skills 庫

《數位時代》編輯團隊使用的 Claude Code Skill 集合，配合 [AI產稿中心 v2](https://www.notion.so/a438232ce0a94fe6b70d9f2e9199a32a) Notion 資料庫運作。

> **給非編輯部同事**：這個 repo 是編輯部的實作範例。部分 Skills 可直接使用（見下方標記 `[通用]`），部分依賴《數位時代》特定環境（`[編輯部]`）。想為你的部門打造類似 Skills 庫，請先讀 [ADAPTATION.md](./ADAPTATION.md)。

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

| 項目 | 必要性 | 說明 |
|------|--------|------|
| Claude Code | 必要 | 需登入 claude.ai 帳號 |
| Notion MCP | 編輯部需要 | 在 Claude Code 設定中連接 Notion，供 `notion-orchestrator` 使用 |
| NOTION_TOKEN | 編輯部需要 | 見下方說明 |
| Playwright MCP | 選配 | `claude mcp add playwright npx @playwright/mcp@latest`。僅用於需互動操作的頁面（如付費牆登入後取內容）；**不作為一般網頁讀取的備援**，一般網頁讀取失敗應直接回報使用者並索取原文 |

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

標籤說明：
- `[通用]` — 任何部門可直接安裝使用，無特殊環境依賴
- `[編輯部]` — 依賴《數位時代》特定環境（Notion 資料庫 / Obsidian vault / 編輯部風格規範），其他部門需客製（見 [ADAPTATION.md](./ADAPTATION.md)）

| 檔案 | 標籤 | 用途 |
|------|------|------|
| `article-checker.md` | `[通用]` | 文章查核：錯字、邏輯、事實查核 + 貨幣換算 |
| `headline-generator.md` | `[通用]` | 標題生成：文章 → 多角度標題選項，含 SEO 推薦 |
| `retrospective.md` | `[通用]` | 工作覆盤：掃描對話，整理記憶與 Skill 更新建議 |
| `news-daily.md` | `[編輯部]` | 即時新聞編譯：外文新聞 → 繁體中文即時稿 |
| `deep-analysis.md` | `[編輯部]` | 深度分析：PDF / 長文 / 影片 → 深度商業分析文章 |
| `draft-polish.md` | `[編輯部]` | 潤稿：AI 草稿 → 去 AI 味、符合編輯風格的正式稿件 |
| `social-post.md` | `[編輯部]` | 社群貼文：文章 → Facebook / IG 貼文（含《數位時代》品牌風格） |
| `tutorial-article.md` | `[編輯部]` | 教學文：X 方法論 / 工具技巧 → 教學型文章 |
| `etf-explainer.md` | `[編輯部]` | ETF 懶人包：ETF 規格 + 公開說明書 + 媒體素材 → SEO 流量型懶人包稿件（9 個 H2 + Q&A） |
| `notion-orchestrator.md` | `[編輯部]` | 主腦：掃描「AI產稿中心 v2」Notion 資料庫，自動派發 Skill 批次產稿 |
| `lint-vault.md` | `[編輯部]` | Vault 健康檢查：掃描 Obsidian vault 孤兒筆記、缺連結、過時內容 |
| `visual-brief.md` | `[編輯部]` | 主視覺發想：稿件 → 給 Claude Design 或其他設計工具的結構化視覺 brief |
| `bwt-style-guide.md` | `[編輯部]` | 《數位時代》統一寫作風格規範（兩岸用語、日期、禁用詞彙、技術白話化等；其他 Skills 依賴讀取） |
| `bwt-design-standard.md` | `[編輯部]` | 美術製作標準總綱（BN Design System 完整 design tokens：色彩、字體、間距、圓角、強調系統；所有產出視覺配件的 Skill 都應遵守） |
| `bwt-html-table-component.md` | `[編輯部]` | HTML 表格元件子規範（inline style 範本 + 精緻化細節：斑馬紋、左側橘條等；引用 `bwt-design-standard.md` 取得 tokens）；**目前僅 `etf-explainer` 套用**，未來評估擴散至其他 Skill |

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

## 進階：Obsidian Vault 整合

這套 Skills 可以搭配 Obsidian vault 使用，讓每次產稿的研究成果自動累積成知識庫，形成複利效果。架構概念來自 [Andrej Karpathy 的 LLM wiki 模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。

### Vault 資料夾結構

```
~/Claude Project/
├── inbox/               ← Web Clipper 原料暫存區
├── research/            ← 編譯後的知識庫（公司、產業、技術）
│   └── _index.md        ← 知識庫入口索引（Claude 自動維護）
├── draft/               ← 產稿過程中的草稿與中間版本
│   └── _register.md     ← 檔案狀態追蹤表（Claude 自動維護）
├── people/              ← 人物資料卡
├── projects/            ← 中長期專案文件
├── daily-notes/         ← 每日工作日誌
├── archive/             ← 發布後值得收藏的文章
└── personal/            ← 私人內容（不參與自動寫回與 lint 掃描）
```

### 與 Skills 的整合方式

**Wiki Ingest 統一在每日收工時批次處理**（由個人 `CLAUDE.md` 中的規則驅動，需在你的工作資料夾根目錄建立對應的 CLAUDE.md；`bn-claude-code-init.md` 初始化指南會協助生成）：

- 說「收工」或「每日總結」觸發收工流程
- Step 2.5 掃描今日所有產稿（Notion 稿件、draft/ 新增、研究型對話），列出值得建筆記的公司、產業、人物、技術主題
- Terry 一次批次決定「建 / 合併 / 跳過」
- 每次寫入後同步更新 `research/_index.md`

**為什麼不在每次產稿後即時跑**：批次處理比中斷式 checkpoint 更低摩擦，也避免一次性主題堆積成無價值 stub。

### 快速上手

新同事可使用 `bn-claude-code-init.md` 初始化指南，將檔案丟給 Claude Code 說「照這個幫我設定」，即可自動完成 Skills 安裝、資料夾建立與 CLAUDE.md 生成。

### 注意事項

- Vault 架構可透過初始化指南自動建立，或手動建立
- `lint-vault` skill 可定期掃描 vault 健康狀態（孤兒筆記、過時內容等）

---

## 版本紀錄

最新版本：**v2.7.1（2026-05-06）** — notion-orchestrator 安全修正與寫回格式統一

完整異動紀錄請見 [CHANGELOG.md](./CHANGELOG.md)。

> **更新流程**：每次更新 Skills 後，在 [CHANGELOG.md](./CHANGELOG.md) 補上版本號、日期、異動說明，再 commit & push。
> 版本號規則：新增 Skill → 次版號 +1（v2.x.0）；修改既有 Skill → 修訂號 +1（v2.x.x）。
