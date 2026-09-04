# 《數位時代》Skills 庫

《數位時代》編輯團隊使用的 Claude Code Skill 集合：即時新聞編譯、長文、潤稿、社群貼文、查核、視覺配件，搭配個人的 Notion 產稿資料庫與 Obsidian vault 運作。

## ⚡ 開機指令（新同事從零安裝，看這一節就夠）

**人類要做的事只有一句話**：在 Claude Code（CLI 或 VSCode extension）裡說

> 照 https://github.com/terrylee-lang/BN_next 的開機指令幫我設定

其餘交給 AI。安裝過程會寫入 `~/.claude/`，遇到權限詢問請按允許；中途 AI 會停下來請你做幾件只有本人能做的事（見下方「需要本人動手的四件事」）；全部裝完**要重開 session**，Skills 清單才會重新載入。

**以下是給 AI 的指令。** 使用者要你依本 repo 設定產稿環境時，只做這三步，**不要憑這一頁推導安裝步驟**——網頁抓取工具會把本頁摘要改寫，指令與檢查點會失真。安裝細節一律 clone 後用 Read 讀本機檔案（無損）。

1. 確認你能執行 Bash 並寫入使用者家目錄。不能 → 停下回報「這套安裝必須在 Claude Code（CLI 或 VSCode extension）裡執行」，**不要**改用其他方式代替、也不要只印一份步驟叫使用者自己做。
2. 取得本體：

   ```bash
   git clone https://github.com/terrylee-lang/BN_next.git ~/.claude/agents
   ```

   `~/.claude/agents` 已存在且不是本 repo（裡面有使用者自己的 agent 檔）→ **不要覆蓋、不要用 `cp` 倒進去**，改照 `bn-claude-code-init.md` Step 2a 的三分支處理。
3. `Read ~/.claude/agents/bn-claude-code-init.md`，依 Step 0 → Step 8 執行到底，最後照 `SMOKE-TEST.md` 跑 T1–T11 並輸出回報表。T1–T5 沒全過不算安裝完成。

使用者**不在《數位時代》編輯部** → 先 `Read ~/.claude/agents/ADAPTATION.md`，依它分流再決定裝哪幾支，不要整套照裝。

### 需要本人動手的四件事

AI 不能代替你完成這些，安裝到對應步驟會停下等你：

| # | 事項 | 在哪一步 | 約需 |
|---|------|----------|------|
| 1 | 到 claude.ai → Settings → Connectors 授權 Notion 連接器 | Step 5a | 2 分鐘 |
| 2 | 自建 Notion 產稿資料庫 ＋ integration，並把 token 填進 `~/.claude/settings.local.json` | Step 1 第 7 題、Step 5b | 10 分鐘 |
| 3 | 建 GitHub public repo `bnext-visuals` 並開啟 Pages（做視覺配件才需要） | Step 6 | 5 分鐘 |
| 4 | 在互動式 session 執行 `/mcp` 完成 bnext-service（Pluto 後台）OAuth | Step 5c | 2 分鐘 |

### 更新至最新版本（已經裝過的人）

```bash
git -C ~/.claude/agents pull
```

> **給非編輯部同事**：這個 repo 是編輯部的實作範例。部分 Skills 可直接使用（見下方標記 `[通用]`），部分依賴《數位時代》特定環境（`[編輯部]`）。想為你的部門打造類似 Skills 庫，請先讀 [ADAPTATION.md](./ADAPTATION.md)。

---

## 前置需求

| 項目 | 必要性 | 說明 |
|------|--------|------|
| Claude Code | 必要 | 需登入 claude.ai 帳號；CLI 與 VSCode extension 共用 `~/.claude/` |
| `git` | 必要 | 安裝與更新本 repo |
| `jq` | 跑 Notion 流程必要 | 讀取 `NOTION_TOKEN`。brew 裝完**必須**在 `~/.local/bin` 建 symlink（Claude Code 的 Bash 不載入 `~/.zprofile`，PATH 不含 `/opt/homebrew/bin`） |
| `poppler`（pdftotext） | 選配、建議 | PDF 素材處理，同樣要建 symlink |
| Notion 連接器 | 跑 Notion 流程必要 | 走 claude.ai → Settings → Connectors 授權，**不要用 `claude mcp add` 自建**（工具前綴會不同，notion-orchestrator 全部呼叫失敗） |
| NOTION_TOKEN | 跑 Notion 流程必要 | 你自建的 integration secret，見下方說明 |
| bnext-service MCP | 選配 | Pluto 後台撈稿與流量，安裝見 `bn-claude-code-init.md` Step 5c |
| Playwright MCP | 選配 | `claude mcp add playwright -- npx @playwright/mcp@latest`。僅用於需互動操作的頁面（付費牆內容一律直接向使用者索取原文，不用 Playwright 繞過）；**不作為一般網頁讀取的備援**，一般網頁讀取失敗應直接回報使用者並索取原文 |

### 設定 NOTION_TOKEN

在 `~/.claude/settings.local.json` 的 `env` 區塊加入：

```json
{
  "env": {
    "NOTION_TOKEN": "ntn_你自己的 integration secret"
  }
}
```

Token 為**你自己建立**的 Notion internal integration secret（到 [notion.so/my-integrations](https://www.notion.so/my-integrations) 建立）。產稿資料庫各自獨立、不共用他人稿單，金鑰也不在人與人之間傳遞。建置細節見 [bn-claude-code-init.md](./bn-claude-code-init.md) Step 5b。

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
| `deep-analysis.md` | `[編輯部]` ⚠️ **過渡期退路** | 舊版深度分析。已由 `longform.md` 接手，只在使用者明確說「用舊的 deep-analysis」時才走；longform 驗證穩定後廢除 |
| `draft-polish.md` | `[編輯部]` | 潤稿：AI 草稿 → 去 AI 味、符合編輯風格的正式稿件 |
| `social-post.md` | `[編輯部]` | 社群貼文：文章 → Facebook / IG 貼文（含《數位時代》品牌風格） |
| `tutorial-article.md` | `[編輯部]` ⚠️ **過渡期退路** | 舊版教學文。已由 `longform.md` 接手，只在使用者明確說「用舊的教學文」時才走；longform 驗證穩定後廢除 |
| `etf-explainer.md` | `[編輯部]` | ETF 懶人包：ETF 規格 + 公開說明書 + 媒體素材 → SEO 流量型懶人包稿件（9 個 H2 + Q&A） |
| `longform.md` | `[編輯部]` | 長文（**1,500 字以上長稿的主線**）：合併 deep-analysis 與 tutorial-article，內建 grill 前置（動筆前先讀完素材、盤點來源立場、逼問切角到共識），再由大綱決定套論證型或步驟型結構。主觸發語是「幫我寫長文」；「幫我寫深度分析」「幫我寫教學文」也預設導向這裡。⚠️ 過渡期（2026-07-29 起）：目前僅驗證過論證型路徑，步驟型尚未實測，要走舊版須明確說「用舊的 deep-analysis／教學文」 |
| `notion-orchestrator.md` | `[編輯部]` | 主腦：掃描「AI產稿中心 v2」Notion 資料庫，自動派發 Skill 批次產稿 |
| `lint-vault.md` | `[編輯部]` | Vault 健康檢查：掃描 Obsidian vault 孤兒筆記、缺連結、過時內容 |
| `visual-brief.md` | `[編輯部]` | 主視覺發想：稿件 → 給 Claude Design 或其他設計工具的結構化視覺 brief |
| `visual-asset.md` | `[編輯部]` | 互動視覺配件：文章資料 → iframe 嵌入式 HTML 配件（時間軸、橫條排行、地圖等），桌機 + 手機雙軌設計，托管至 GitHub Pages |
| `bwt-style-guide.md` | `[編輯部]` | 《數位時代》統一寫作風格規範（兩岸用語、日期、禁用詞彙、技術白話化等；其他 Skills 依賴讀取） |
| `bwt-design-standard.md` | `[編輯部]` | 美術製作標準總綱（BN Design System 完整 design tokens：色彩、字體、間距、圓角、強調系統；所有產出視覺配件的 Skill 都應遵守） |
| `bwt-html-table-component.md` | `[編輯部]` | HTML 表格元件子規範（inline style 範本 + 精緻化細節：斑馬紋、左側橘條等；引用 `bwt-design-standard.md` 取得 tokens）；**目前僅 `etf-explainer` 套用**，未來評估擴散至其他 Skill |
| `bwt-iframe-visual-component.md` | `[編輯部]` | iframe 互動視覺配件子規範（HTML 結構模板 + 雙軌設計強制規則 + Logo 加入規範；引用 `bwt-design-standard.md` 取得 tokens）；目前僅 `visual-asset` 套用 |
| `bwt-visual-checklist.md` | `[編輯部]` | 視覺任務入口與驗收 rubric（設計優先序、產品類型判斷、16:9 主視覺 SOP、交付 checklist）；**所有視覺類任務動手前先讀此檔**，由它指路其餘視覺規範 |

---

## 這個 repo 交接什麼、不交接什麼

**隨附（clone 就有，足以跑完「素材進來 → 產稿 → 查核 → 存檔」主線）**：上表所有 Skills 與 `bwt-*` 規範、安裝器 `bn-claude-code-init.md`、個人設定範本 `CLAUDE-template.md`、驗收清單 `SMOKE-TEST.md`、跨部門改造指引 `ADAPTATION.md`。

**刻意不隨附**（屬個人層，缺了不影響產稿，AI 不必去找、也不要自建空檔充數）：

| 缺的東西 | 缺了會怎樣 | 要的話 |
|----------|------------|--------|
| `bwt-voice-reference.md`（個人語氣指紋） | 產稿正常，少一層個人風格校準。repo 內所有引用都是條件句 | 自己寫一份，路徑寫進個人 CLAUDE.md |
| 每日收工 SOP（含 Wiki Ingest 判準） | 沒有「收工」自動化：inbox 清理、Register 對齊、research 建檔要自己做 | 向 Terry 索取範例 |
| `ops/` 四件套（派工 playbook、判斷 rubric、派工模板、規範維護協定） | 沒有跨 session 的派工與判斷準則，屬進階最佳化 | 向 Terry 索取範例 |
| memory 政策、research／archive 筆記格式範本 | 記憶與筆記格式靠 Claude 臨場判斷，較容易漂移 | 向 Terry 索取範例 |
| `bwt-lint.sh`（機械檢查腳本） | `article-checker` Step 0 自動降級，查核照跑 | 向 Terry 索取 |

先把主線跑順再談這些，不要在安裝階段卡在這一欄。

---

## 觸發方式

在 Claude Code 對話框輸入：

```
跑今天的稿件
跑 [負責人] 的稿件
處理 Notion 的待辦
```

Claude 會掃描**你自己的**產稿資料庫（URL 寫在你的 `~/.claude/CLAUDE.md`），篩選待辦條目（「未開始」，並回收上一輪未寫回的「進行中」空白頁），依稿件類型派發對應 Skill，完成後寫回 Notion。

---

## 進階：Obsidian Vault 整合

這套 Skills 可以搭配 Obsidian vault 使用，讓每次產稿的研究成果自動累積成知識庫，形成複利效果。架構概念來自 [Andrej Karpathy 的 LLM wiki 模式](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。

### Vault 資料夾結構

```
~/Claude Project/
├── inbox/               ← Web Clipper 原料暫存區
├── research/            ← 編譯後的知識庫（公司、產業、技術）
│   └── _index.md        ← 知識庫入口索引（Claude 自動維護）
├── draft/               ← 產稿工作副產品暫存區
│   └── _register.md     ← 檔案狀態追蹤表（Claude 自動維護，≤ 5,000 bytes）
├── people/              ← 重要人物資料卡
├── projects/            ← 中長期專案文件（含 bnext-visuals/ 配件本地副本）
├── daily-notes/         ← 每日工作日誌（YYYY-MM-DD.md）
├── archive/             ← 發布後值得收藏的文章
├── pic/                 ← 圖片素材（不參與 lint 掃描）
├── personal/            ← 私人內容（不參與自動寫回與 lint 掃描）
└── .trash/              ← 刪除緩衝區（先移入 .trash/YYYYMMDD/，14 天後清空）
```

### 與 Skills 的整合方式

**Wiki Ingest 統一在每日收工時批次處理**，不在每次產稿後即時跑：批次處理比中斷式 checkpoint 更低摩擦，也避免一次性主題堆積成無價值 stub。說「收工」或「每日總結」觸發，流程會掃描今日產稿與研究型對話，把值得累積的公司、產業、人物、技術主題自動建檔或合併進 `research/`、`people/`，並同步更新 `research/_index.md`。

⚠️ **收工流程本身屬個人層、不隨本 repo 交接**（見上方「交接什麼、不交接什麼」）。這裡描述的是它長什麼樣子，clone 完不會自動有；需要的話向 Terry 索取範例，或自己寫一份規則掛進 vault 的 `CLAUDE.md`。

### 注意事項

- Vault 架構由開機指令的 Step 4 自動建立（也可手動建）
- `lint-vault` skill 可定期掃描 vault 健康狀態（孤兒筆記、過時內容等）

---

## 版本紀錄

最新版本：**v3.2.7（2026-09-04）** — 體檢糾正判讀長出三條規則：`article-checker` Codex 協議補「口徑修正不加免責句」與「屬性欄同步」；`bwt-style-guide` AI 味總表新增「重複觀點與過度細節」列（冗長是 8 月第二大糾正類型）；`longform` 步驟型補「實作精簡」rubric 與 checklist。v3.2.6：首次實推 Pluto 後補齊三缺口：`notion-orchestrator` 新增 draft/→Notion 回填程序（2f-ter）與字數唯一定義、修正 permlink「自動生成」誤述；`bwt-pluto-push-spec` 新增推送參數映射（writer／permlink／page_title／content＋reference）；`article-checker` 新增「文體／讀感最終確認」五項檢查。v3.2.5：素材讀取新增「先掃 inbox 再 web_search」前置步驟（`bwt-style-guide.md` 共用章節＋`longform.md` Step 1a），避免只給題目沒指路徑時漏看已備素材、大綱重工。v3.2.4：新增「正文的來源歸屬分界」，區分客觀數據直接敘述 vs 媒體詮釋才掛出處。v3.2.3：鐵律新增「無幽靈段落」子條，規範裡的「必答」槽位是檢查提示、不是生產指令，素材撐不住就略過。v3.2.2：對齊 Pluto 後台推送規格——資料來源行確立為結構化 `reference` 資料、新增「文章元件語意標記」（抽言／延伸閱讀／重點框／影音）、Notion 資料庫欄位對齊實際 select 選項（負責人 6 人、稿件類型 3+1 種）、新增 `bwt-pluto-push-spec.md`。v3.2.1：事實查核鐵律補「摘要不算讀到」子條，`web_search`／`web_fetch` 摘要僅供判斷來源、引用一律回原文核對。v3.2.0：交接包可自助化：README 第一屏新增「⚡ 開機指令」（貼 repo 網址給 Claude Code 即可自助安裝，因網頁抓取會摘要失真，指令刻意壓到三步、細節一律 clone 後讀本機檔）、人工事項前置到 Step 1、新增「交接什麼／不交接什麼」清單；同步修掉安裝器 17 處過時與互相矛盾（Notion 資料庫「各自建置」口徑未改乾淨、Step 5b 反向依賴已跑完的 Step 3、stub 名單三份文件三套、longform 未列入檔案清單與個人 CLAUDE.md 範本、`.trash/` 未建、Playwright 指令缺 `--`）。v3.1.0：新增 `longform.md` 長文 Skill。v3.0.2：三大重點字數統一 50 字、etf 字數例外確認保留。v3.0.1：Notion 產稿資料庫改各自建置（不共用稿單與 token）。v3.0.0（2026-07-28）：全系統總體檢：修復 8 處規範互相打架的活矛盾、約束單點化（`bwt-style-guide` 升為跨 Skill 共用段落唯一權威、全稿種字數上限統一 3,500 字）、`article-checker` 三軸改造、交接包改造（新增 `CLAUDE-template.md`＋`SMOKE-TEST.md`）

完整異動紀錄請見 [CHANGELOG.md](./CHANGELOG.md)。

> **更新流程**：每次更新 Skills 後，在 [CHANGELOG.md](./CHANGELOG.md) 補上版本號、日期、異動說明，再 commit & push。
> 版本號規則：新增 Skill → 次版號 +1（v3.x.0）；修改既有 Skill 或文件 → 修訂號 +1（v3.x.x）。
