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
| `notion-orchestrator.md` | `[編輯部]` | 主腦：掃描「AI產稿中心 v2」Notion 資料庫，自動派發 Skill 批次產稿 |
| `lint-vault.md` | `[編輯部]` | Vault 健康檢查：掃描 Obsidian vault 孤兒筆記、缺連結、過時內容 |
| `bwt-style-guide.md` | `[編輯部]` | 《數位時代》統一風格規範（其他 Skills 依賴讀取） |

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

**deep-analysis / draft-polish 完成後**，Claude 會自動判斷是否將研究內容寫回 vault（此行為由個人 `CLAUDE.md` 中的 Wiki Ingest 規則驅動，需在你的工作資料夾根目錄建立對應的 CLAUDE.md；`bn-claude-code-init.md` 初始化指南會協助生成）：

- 涉及的公司、產業主題 → `research/`
- 涉及的人物 → `people/`
- 每次寫入後同步更新 `research/_index.md`

**執行流程**：任務完成後 Claude 列出建議寫回項目 → 確認後寫入，不需手動整理。

### 快速上手

新同事可使用 `bn-claude-code-init.md` 初始化指南，將檔案丟給 Claude Code 說「照這個幫我設定」，即可自動完成 Skills 安裝、資料夾建立與 CLAUDE.md 生成。

### 注意事項

- Vault 架構可透過初始化指南自動建立，或手動建立
- `lint-vault` skill 可定期掃描 vault 健康狀態（孤兒筆記、過時內容等）

---

## 版本紀錄

> **更新標準流程**：每次更新 Skills 後，在此區塊補上版本號、日期、異動說明，再 commit & push。
> 版本號規則：新增 Skill → 次版號 +1（v2.x.0）；修改既有 Skill → 修訂號 +1（v2.x.x）。

---

### v2.6.1 — 2026-04-15

**跨部門同事友善化**
- 新增 [ADAPTATION.md](./ADAPTATION.md)：回答跨部門同事三個問題——哪些 Skill 可直接用、想做自己的 Skill 怎麼開始、要改編輯部 Skill 要改哪
- README Skills 一覽表加上 `[通用]` / `[編輯部]` 標籤，一眼看出適用範圍
- README 前置需求表區分「必要 / 編輯部需要 / 選配」，釐清 Playwright MCP 定位（不作為一般網頁讀取備援）
- README 頂部加上「給非編輯部同事」導覽提示

**文件一致性修正**
- `retrospective`：修正硬寫的個人 memory 路徑，改為動態 cwd 判斷，其他使用者安裝後可正常運作
- `bn-claude-code-init`：Skills 觸發方式表補上 `notion-orchestrator`、`retrospective`、`lint-vault` 三支；資料夾結構補上 `personal/`
- README：Vault 整合段落標註「需配合個人 CLAUDE.md 的 Wiki Ingest 規則」，避免同事誤以為是 Skill 內建行為

---

### v2.6.0 — 2026-04-15

**notion-orchestrator（架構擴展）**
- Step 2c 新增來源預讀：本地路徑（`/` 或 `~/` 開頭的 `.md`）由 orchestrator 先 Read 成全文，再傳給子代理
- Step 2c-bis 新增來源失敗彙整：所有子代理回傳後統一回報 `SOURCE_FAILED`，一次向使用者索取原文，不逐篇中斷
- Step 2d 新增自動事實查核：寫回 Notion 前呼叫 `article-checker` 三層查核，可自動修正處套用、無法核實處加 `[編按]` 標註
- Notion 流程稿件不再寫入 `draft/`，Notion 頁面即為 single source of truth
- Step 3 完成回報補上 inbox 檔案去留確認（依稿件類型給預設建議）
- 明文禁止硬寫：來源讀取失敗時子代理須回傳 `SOURCE_FAILED`，不得用二手來源改寫

**deep-analysis（分析框架擴展）**
- Step 1 新增「產業戰役定位」「示範效應與擴散」「反共識自檢」三個推演步驟
- 新增多源交叉查證要求：動筆前搜尋至少兩個媒體來源
- 新增「禁止誇大媒體反應」規範：不得自行推斷外界輿論，必須具體到媒體名稱
- 來源讀取不再使用 Playwright 備援，失敗即停止索取原文

**article-checker**
- 新增「來源矛盾處理（Contradiction Protocol）」：矛盾在報告中如實記錄，不強行收斂
- 報告模板新增「🔀 來源矛盾」區塊
- 前置作業明文要求先讀 `bwt-style-guide.md`

**news-daily / social-post / tutorial-article / bn-claude-code-init**
- 全面支援本地檔案路徑作為來源（inbox/ 流程對接）
- 來源失敗處理統一為「立即停止並索取原文」，不使用 Playwright 備援

**bwt-style-guide**
- 破折號使用規範收緊：全文僅用於關鍵停頓或補充說明；不足 2,000 字稿件最多 1 次；超過 2,000 字稿件每 2,000 字最多 1 次；禁用於連接句、堆疊形容詞、情緒渲染

**headline-generator**
- 前置作業明文要求先讀 `bwt-style-guide.md`

**lint-vault**
- 第 7 類 draft 清理與「每日收工流程」協調，不重複提醒已處理檔案

---

### v2.5.0 — 2026-04-08

**新增**
- `lint-vault.md`：Vault 健康檢查 Skill，掃描孤兒筆記、缺連結、過時內容、格式問題
- `bn-claude-code-init.md`：新同事初始化指南，丟給 Claude Code 即可自動設定完整工作環境

**README 更新**
- Skills 一覽表新增 lint-vault
- Vault 資料夾結構補上 draft/ 和 personal/
- 新增「快速上手」段落，指向初始化指南

---

### v2.4.1 — 2026-04-07

**README**
- 新增「進階：Obsidian Vault 整合」段落，說明如何搭配 Karpathy wiki 架構讓產稿知識自動累積

---

### v2.4.0 — 2026-04-02

**新增 Skill**

- `retrospective`：新增工作覆盤 Skill，session 結束後掃描對話，整理值得保留的偏好與教訓，產生記憶或 Skill 更新建議

**修改 Skills**

- `deep-analysis`、`news-daily`：更新內容邏輯
- `notion-orchestrator`：精簡執行步驟

---

### v2.3.3 — 2026-03-26

**draft-polish、social-post**
- 新增風格規範讀取步驟，開始前先讀取 `bwt-style-guide.md`
- `social-post` 說明限縮為「人名、公司名與兩岸用語」，避免過度套用不適用的寫作規則

---

### v2.3.2 — 2026-03-26

**notion-orchestrator**
- Bloomberg 讀取改為條件式：腳本存在才執行，不存在自動 fallback 到 Playwright，其他使用者不受影響
- 硬寫的 `/Users/terrylee` 路徑改為 `$HOME`，跨機器相容

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
