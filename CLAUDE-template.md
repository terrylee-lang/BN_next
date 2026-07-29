# 全域 CLAUDE.md 範本（編輯部新同事用）

這份檔案是 `~/.claude/CLAUDE.md` 的可改寫範本。`~/.claude/CLAUDE.md` 是**你個人的**設定檔，不在共享 repo 內，也不會被 `git pull` 覆蓋——所以每個人都要從這份範本產生一份自己的。

**使用方式**（`bn-claude-code-init.md` 安裝流程 Step 3 會自動做這件事，手動做也可以）：

1. 複製本檔到 `~/.claude/CLAUDE.md`
2. 把所有 `<>` 佔位符換成你的值：`<你的名字>`、`<你的 vault 路徑>`、`<你的 GitHub 帳號>`
3. 刪掉本區塊（「全域 CLAUDE.md 範本」標題到分隔線為止）與內文所有「範本註記」引言
4. 不需要的區塊直接刪（例：不跑 Notion 流程就刪 MCP 的 Notion 段與 notion-orchestrator 那列）

**這份範本的內容不是喜好問題**：Skill 工具與 Agent 工具兩張名單、Notion 參數小抄，都是歷史上重複踩過幾十次的坑（呼叫機制錯 17 次、Notion 同形參數錯 26 次），保留原樣照抄，不要憑印象改寫。標「建議預設值」的段落才是可調的個人偏好。

---
---

# Claude Code 全域設定

## 使用者背景

<你的名字> 是《數位時代》(Business Next) 的科技媒體編輯。主要工作是 AI 輔助產稿，包含即時新聞編譯、深度分析長文、教學文、社群貼文生成與稿件查核。

**日常品管流程（Claude 要認得自己在其中的角色）**：Claude 產稿 → article-checker 查核 → 編輯貼入第二模型（Codex／其他）的查核意見 → Claude 執行修改 → 第二模型複查（查核強度逐輪遞減，避免無限循環）→ Claude 最終文體／讀感確認 → 發布。第二模型意見進來時，Claude 的職責是**執行修改並守住格式**（文末資料來源格式特別容易在多輪修改中跑掉，改完要回查）；若外部建議與已查證事實衝突，先 web_search 再決定，不盲從也不硬頂。

---

## 檔案儲存位置

所有在本機產生的檔案，預設儲存至 `<你的 vault 路徑>`。

> 範本註記：**強烈建議直接用 `~/Claude Project/`**。共享 repo 內有 6 個 Skill／規範檔內文寫死這個路徑（`lint-vault`、`visual-asset`、`bwt-style-guide`、`bwt-design-standard`、`bwt-iframe-visual-component`、`bwt-html-table-component`）。若你堅持用別的路徑，必須保留下面這行，否則 inbox 素材匹配與視覺配件流程會靜默失效：
>
> **路徑代換規則**：`~/.claude/agents/` 內任何 Skill 或規範檔出現的 `~/Claude Project/`，一律代換為 `<你的 vault 路徑>`。

---

## Skills 與 Agents（呼叫方式對照）

同樣住在 `~/.claude/agents/` 的檔案，呼叫機制分兩種，**用錯會直接報錯**（歷史上錯過 17 次）：

- **Skill 工具可呼叫**（`~/.claude/skills/<名稱>/SKILL.md` 有對應 stub 的才會出現在 Skill 清單）：news-daily、deep-analysis、tutorial-article、draft-polish、social-post、article-checker
- **只能走 Agent 工具（`subagent_type`）**：notion-orchestrator、longform、visual-asset、visual-brief、headline-generator、etf-explainer、lint-vault、retrospective
  - 其中只有 `notion-orchestrator` 在 `~/.claude/skills/` 有 stub（帶 `disable-model-invocation: true`：**使用者**可用 `/notion-orchestrator` 觸發，**Claude 自己**要主動呼叫則走 Agent 工具）。其餘幾支**沒有 stub**，打 `/名稱` 不會有東西，用自然語言觸發即可（「幫我寫長文」、「幫我做配件」、「幫我想標題」）
  - 所以 `ls ~/.claude/skills/` 應該是 7 個目錄：上面 Skill 工具那 6 支 ＋ notion-orchestrator。想讓某支 Agent-only 的也能用 `/名稱`，自己補一個帶 `disable-model-invocation: true` 的 stub（個人層檔案，不進共享 repo）
- 一律**以 session 開頭注入的可用清單為準**；工具不可用即改走另一機制或替代路徑（如 Bash `rg` 代替 Grep），同一工具不重試第三次

### 產稿 Skills

觸發語對照如下，工作內容細節以 session 注入的描述與各 Skill 檔案為準：

| Skill 名稱 | 觸發時機 |
| --- | --- |
| `notion-orchestrator` | 說「跑今天的稿件」，或「跑 [負責人] 的稿件」（「負責人」欄位的選項由你在自己的資料庫自行設定） |
| `news-daily` | 貼上外文新聞連結或說「幫我編譯這篇」（650-1000 字） |
| `longform`（**長文，1,500 字以上長稿的主線**） | 說「幫我寫長文」、「這篇寫成長文」；也接收「幫我寫深度分析」「幫我寫教學文」（論證型目標 2,500 字內／步驟型 800-1,500 字）。內建 grill 前置：先讀完全部素材、盤點來源立場、逼問切角到共識，再由大綱決定套論證型或步驟型結構 |
| `draft-polish` | 說「幫我潤稿」、貼上 NotebookLM 草稿（目標 2,000-2,500 字） |
| `deep-analysis` | **過渡期保留的退路**，明確說「用舊的 deep-analysis」才走；預設由 `longform` 接手 |
| `tutorial-article` | **過渡期保留的退路**，明確說「用舊的教學文」才走；預設由 `longform` 接手 |
| `social-post` | 貼文章連結，說「幫我寫社群貼文」（FB／IG） |
| `article-checker` | 說「幫我查核」、「幫我 fact check」 |
| `headline-generator` | 說「幫我想標題」、「幫我下標」、「給我幾個標題選項」 |
| `visual-brief` | 說「幫我想主視覺」、「幫我寫視覺 brief」、「這篇配圖要怎麼發想」 |
| `etf-explainer` | 貼 ETF 代號或公開說明書，說「幫我寫 00XXX 懶人包」、「幫我寫 ETF 流量文」（9 個 H2 + Q&A，3,500-4,500 字） |
| `visual-asset` | 說「幫我做配件」、「做張圖表」、「做個 tracker」、「做主視覺」、「做封面圖」、「做 16:9 圖卡」——動手前先判斷產品類型 |

### 工具型 Skills

| Skill 名稱 | 觸發時機 |
| --- | --- |
| `lint-vault` | 說「幫我檢查 vault」、「跑 lint」、「vault 健康檢查」 |
| `retrospective` | 說「幫我覆盤」、「跑 retrospective」——用於系統討論層級；產稿類 feedback 改用「丟發布連結比對差異」流程 |

---

## 內容讀取優先順序（所有產稿 Skill 共用）

**規則本體的唯一居所是 `~/.claude/agents/bwt-style-guide.md`「來源讀取優先序（跨 Skill 共用）」**，本檔不複寫，避免兩份規則各自漂移。取素材前讀那一節，重點只記三件事：

1. 本地檔案優先、URL 先掃 `<你的 vault 路徑>/inbox/` 的 frontmatter `source` 命中就讀本機全文，都沒有才 `web_fetch`
2. **讀不到就停**：立即回報並向使用者索取原文（用 Dia 瀏覽器萃取 Markdown 貼入），**不自行替換素材繼續產稿**
3. **不使用 Playwright 作為內容讀取備援**——snapshot 吃大量無關 DOM，浪費 token 且品質差

### 付費牆媒體（Bloomberg、WSJ、日經、Business Insider）

編輯部同事多半有公司付費帳號。`web_fetch` 失敗時**直接索取原文**，不嘗試繞過、不用摘要或二手引述硬寫——用不完整素材硬寫容易產生事實錯誤。

### PDF 處理

收到 PDF 一律先用 `pdftotext`（brew 裝 poppler 後在 `~/.local/bin` 建 symlink，見安裝指南 Step 0）萃取純文字到暫存目錄再讀；需要看版面／圖片才用 Read 的 pages 參數（單次 ≤5 頁）。不要讓大型 PDF base64 常駐對話（歷史上單則 23MB 的 PDF 貼入直接吃掉大半個 session）。

---

## 寫作風格規範

- **所有產稿 Skill 啟動前必讀** `~/.claude/agents/bwt-style-guide.md`：兩岸用語轉換、人名與公司名規範、禁止詞彙、**事實查核鐵律**、技術性稿件白話化原則、署名格式。
- **署名**：文末署名行的責任編輯 = 稿件負責人。走 Notion 流程時取「負責人」欄位；自己交辦的稿件寫 `<你的名字>`。
- **個人風格指紋（選配）**：若你有自己的語氣偏好檔（例如 `~/.claude/<你的名字>-voice-reference.md`），在這裡寫出路徑與「哪幾支 Skill 產稿時要額外讀」。**沒有這個檔就整段刪掉**——repo 內所有對個人風格檔的引用都是條件句（「若存在則讀」），缺檔不會報錯，不需要去建一個空檔案。
- **視覺類任務**（visual-asset、etf-explainer 等產出視覺製品時）：動手前先讀 `~/.claude/agents/bwt-visual-checklist.md`（工作流與交付 checklist），由它指路其餘規範——`bwt-design-standard.md`（design tokens 總綱，single source of truth，**禁止憑外部觀察自建色票**）、`bwt-iframe-visual-component.md`（iframe 實作）、`bwt-html-table-component.md`（表格元件）。

---

## MCP 整合

### Notion（產稿中心資料庫）

- 用途：AI 產稿中心資料庫（掃描待辦、寫回成品）。核心資料庫：**你自建的產稿資料庫**，URL：`<你的產稿資料庫 URL>`（欄位定義可參考 `~/.claude/agents/notion-orchestrator.md`「欄位說明」）。
- **覆寫規則**：`notion-orchestrator.md`「資料庫資訊」裡寫的 URL 與 Data Source ID 是 Terry 環境的預設值，**一律以本檔上面那行為準**。不要去改 repo 內的檔案，改了每次 `git pull` 都會衝突。
- **連接方式**：走 claude.ai 連接器（Settings → Connectors → Notion 授權），**不要用 `claude mcp add` 自建**——notion-orchestrator 內文寫死工具前綴 `mcp__claude_ai_Notion__*`，自建 server 的前綴不同會全部呼叫失敗。
- **參數小抄（同形錯誤曾跨 session 重犯 26 次，照抄不要猜）**：
  - `notion-fetch` 的參數名只有 `id`——傳 `url` 或 `page_id` 當參數名會報 validation error；`id` 的**值**可以放 UUID 或 notion.so 網址
  - `notion-update-page` 的 `replace_content` 參數名是 `new_str`，不是 `content`
  - 狀態欄合法值是「完成」不是「已完成」；寫任何 select 欄位前先 fetch 確認合法選項
  - date 屬性用展開格式；改寫前確認頁面未被 archived
- 報 "not connected" → 到 claude.ai 連接器設定重連，不要在對話裡想辦法繞。
- 寫回後驗證：每輪修改集中送出後，整頁回讀**一次**確認內文與欄位都進去了（**必做**，這是 notion-orchestrator Step 2f 的完成定義）；驗證通過後不再重複抓取。
- 批次掃描用的 `NOTION_TOKEN` 存在 `~/.claude/settings.local.json` 的 `env`，**只能用 inline command substitution 帶進 curl**（禁止 export、echo、寫暫存檔），完整安全規範見 notion-orchestrator.md Step 1。

### bnext-service（BN Media Pluto 後台 CMS）

- 工具前綴 `mcp__bnext-service__*`，連法見安裝指南 Step 5。用途兩類：
  - **撈稿與流量**：`bnext-article`（《數位時代》文章清單，回傳 pageview）、各刊工具（`managertoday-article`、`meet-article`、`future-commerce-article`、`shopping-design-article`、`eighty-life-article`）、`authorinfo`／`ranking-*`
  - **內容池**（`content-pool-add`／`-list`／`-update`，Pluto `ai-content-pool`，發布前暫存）
- **寫入紀律**：`content-pool-*` 寫的是正式後台。**只在使用者明確下令、且稿件已定稿時才推**，絕不自動推、不塞進 notion-orchestrator 自動流程。Notion 是 single source of truth，內容池是單向下游；推前先 `content-pool-list` 比對，不覆蓋人工已動過的條目。
- `permlink` 一律用英文 kebab-case slug（帶中文會被編碼成百分號亂碼）。

### Playwright

- 僅在需要互動操作的頁面使用（`browser_navigate` + `browser_snapshot`），以及量 iframe 配件的桌機高度；**不作為內容讀取備援**。snapshot 為一次性參考，**禁止存入 draft/ 或任何 vault 資料夾**。

---

## 視覺配件托管

- 你的配件 repo：`<你的 GitHub 帳號>/bnext-visuals`，GitHub Pages 網址 `https://<你的 GitHub 帳號>.github.io/bnext-visuals/`，本機工作副本在 `<你的 vault 路徑>/projects/bnext-visuals/`。
- **覆寫規則**：`visual-asset.md` 與 `bwt-iframe-visual-component.md` 內出現的 `terrylee-lang/bnext-visuals` 與 `terrylee-lang.github.io`，一律代換為上面你自己的帳號。
- **為什麼各自託管**：iframe 網址會永久嵌在已發布文章裡，不該綁在別人的個人帳號下。建立步驟見安裝指南 Step 6。
- Public repo：**禁止放未發布稿件、API key、客戶資料、內部財報數據**，每次上傳前自查。

---

## 工作方式偏好

> 範本註記：以下是**建議預設值**，可依自己的習慣調整；但每一條都對應過真實事故，改之前先確認你有更好的做法。

- 收到產稿任務時，主動判斷對應的 Skill 並執行，不需等待確認
- 來源無法讀取時，立即回報，不要自行替換素材繼續產稿
- 長稿不必事前問「這是深度分析還是教學文」——`longform` 的結構取向由大綱決定。要問的只有真正影響路由的分界：即時新聞（news-daily）vs 長文（longform）、ETF 規格懶人包（etf-explainer）vs 其他，判準見各 Skill frontmatter
- **一稿一 session**；長專案每個工作天開新 session，進度狀態寫進檔案（如稿件頂部 status 區塊），不依賴對話記憶——超長 session 是 token 上限與出錯率的最大單一來源
- **已確認過的內容進入修訂輪後**：任何改動先在對話中展示修改前後對照、使用者 OK 才寫回檔案；指令只涉排版／格式時，禁止動文字內容
- **修訂迴圈的讀寫紀律**：用 offset/limit 只讀受影響段落（整讀限初讀與定稿檢查各一次）；同一檔案的多個 Edit 逐一執行、不併發；`old_string` 從最近一次 Read 輸出複製（小心全半形標點與彎直引號）
- **質疑或改動最近一年內的事實前，必先 web_search**；查無反證就保留原文口吻（模型訓練知識晚於現實，先改再查已多次出錯）
- 查模型 ID、定價、發布狀態：用 WebSearch 或官方 docs
- 修改 `~/.claude/agents/` 內任何 Skill 或規範檔後，立即用 Bash `rg` 掃描下游引用（其他 Skill、CLAUDE.md 各區塊），一次修復並主動回報；**改動要推回共享 repo 前先跟 Terry 說**（規範檔是全編輯部共用）

---

## 注意事項

- 破壞性操作（刪除檔案、force push）執行前必須明確確認
- 匯率換算必須即時搜尋，不使用模型訓練資料中的靜態匯率
- 人名若無法查核中文譯名，保留英文原名並標記「人名待確認」
- 完稿後全文搜「明日／今日／稍晚／即將／目前」等相對時間詞，一律改絕對日期或加「截至 X 月 X 日」（相對日期見報即失效，歷史糾正 6 次）

---

## 進階制度檔（選配，個人層）

Terry 個人另有一層制度檔（`~/.claude/ops/` 的派工 playbook、判斷 rubric、規範維護協定，以及 vault 的每日收工 SOP、memory 政策、research 筆記格式）。**這些不在共享 repo 內、也不是啟動必需**——先把上面的基本流程跑順，需要時再向 Terry 索取範例。

---

# 附錄：專案層 CLAUDE.md 最小範本（用 Obsidian 才需要）

> 範本註記：以下內容不是放在 `~/.claude/CLAUDE.md`，而是存成 `<你的 vault 路徑>/CLAUDE.md`。作用是讓 Claude 在 vault 內工作時知道資料夾用途與檔名規則。不用 Obsidian、或還不想管知識庫的人可以整段跳過，之後再補。

```markdown
# Claude Code × Obsidian Vault 規範

這個資料夾同時是 Obsidian vault 與 Claude Code 專案根目錄。

## 資料夾結構

- `inbox/` — Web Clipper 原料暫存區
- `research/` — 編譯後的知識庫（公司、產業、技術）
- `research/_index.md` — 知識庫入口索引（Claude 自動維護）
- `people/` — 重要人物資料卡
- `projects/` — 中長期專案文件（含 `projects/bnext-visuals/` 配件本地副本）
- `draft/` — 產稿工作副產品暫存區
- `draft/_register.md` — 檔案狀態追蹤表（Claude 自動維護，每列備註 ≤ 100 字，全檔 ≤ 5,000 bytes）
- `daily-notes/` — 每日工作日誌（檔名 `YYYY-MM-DD.md`）
- `archive/` — 發布後值得收藏的文章
- `pic/` — 圖片素材（不參與 lint-vault 掃描）
- `personal/` — 工作以外的私人內容（不參與自動寫回、不被 lint-vault 掃描）
- `.trash/` — 刪除緩衝區（先移入 `.trash/YYYYMMDD/`，14 天後清空；移入此處為可逆操作，不需確認）

## 檔案命名規範

`draft/` 與 `archive/`：`YYYYMMDD-類型-中文主題.md`，類型代碼 `news`／`analysis`／`tutorial`／`polish`／`social`／`etf`（`longform` 產出沿用 `analysis`（論證型）或 `tutorial`（步驟型），暫無獨立代碼）。
`research/`：`YYYYMMDD-中文主題.md`（不需類型代碼）。
`inbox/`（Web Clipper 原始檔名）、`daily-notes/`、`people/`、`projects/`、`personal/` 不套用。

## Obsidian 格式規範

1. 內部連結一律使用 `[[Note Title]]`，不用 URL
2. 標題層級：H1 為標題，H2 為小節，H3 以下為細項
3. 有明顯關聯的筆記，在檔案底部加 Related 區塊（條列 `[[相關筆記]]`）
4. 內文中出現的專案名、人名、公司名，若已有對應筆記，自動加上 `[[]]` 連結

## 存檔規則

- 說「存到 vault」、「存到 research/」等，直接寫入對應資料夾並套用格式規範
- 每次新增或更新 `research/` 筆記後，同步更新 `research/_index.md`，並補上雙向 `[[]]` 連結
- **Notion 流程稿件**：直接寫回 Notion，**不存 `draft/`**（Notion 頁面即 single source of truth）
- **非 Notion 稿件**（inbox/ 或臨時交辦）：存入 `draft/`，同時更新 `draft/_register.md`
- `archive/` 存檔由人主動觸發（「這篇發了」、「存進 archive」），Claude 不主動判斷文章是否已發布
- `personal/` 完全排除於自動寫回與 lint-vault 掃描範圍之外

## inbox/ → 產稿流程

1. 讀取 inbox/ 素材（依指定檔名或主題掃描比對）
2. 判斷或確認稿件類型（不確定就問）
3. 執行對應產稿 Skill
4. 產出存入 `draft/`，更新 `draft/_register.md`
5. **一律自動跑一輪 article-checker 查核**；發現錯誤先修回原成品位置，才回報完成
6. 消化過的 inbox 原始檔先留著，定期自行移入 `.trash/YYYYMMDD/`（Skill 內文提到的「每日收工流程自動處理」屬個人層自動化，未隨共享 repo 交接；沒有它不影響產稿）
```
