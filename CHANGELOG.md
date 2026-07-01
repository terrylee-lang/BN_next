# CHANGELOG

《數位時代》Skills 庫的完整版本紀錄。

> **更新流程**：每次更新 Skills 後，在此檔案最上方補上版本號、日期、異動說明，再 commit & push。
> 版本號規則：新增 Skill → 次版號 +1（v2.x.0）；修改既有 Skill → 修訂號 +1（v2.x.x）。

---

## v2.9.4 — 2026-07-01

**文末「資料來源 + 署名」格式強化，並在管線各手加強制保留（防多輪查核後文末被打散）**

源頭：先泰三篇即時新聞（Fable 5／Sonnet 5／Gemini）經 news-daily → article-checker → orchestrator 寫回多手後，文末資料來源被改成 `## 資料來源` 小標 + 條列（甚至英文「Sources:」），署名行整行遺失。格式規範原本就在，缺的是「多輪改寫後保留它」的強制點。

- `bwt-style-guide.md`：「署名格式」升級為最完整權威版——資料來源寫成單行、頓號分隔、加超連結（禁條列／禁 `## 資料來源` 小標／禁英文 Sources）；名稱慣例（新聞媒體用《》，官方公告／文件／定價／榜單不加《》）；署名行固定、責任編輯 = 負責人（Terry 為李先泰）；新增「多輪查核／改寫後最後一步必須確認文末兩行俱全」的強制條款
- `article-checker.md`：Step 3 新增文末資料來源／署名格式查核，被前一手打散時直接改回，且輸出修訂後全文須連同文末格式帶出
- `notion-orchestrator.md`：Step 2e 寫回結構新增文末資料來源／署名保留規則，禁止轉成小標 + 條列，署名取自負責人欄位
- `draft-polish.md`：內嵌署名副本對齊權威版（補《》慣例、責任編輯姓名、多輪保留提醒）

---

## v2.9.3 — 2026-07-01

**notion-orchestrator 新增「前景阻塞式派發」硬性規則（防背景派工提前結束回合）**

源頭：先泰名下 3 篇即時新聞（Fable 5／Mythos 5 解禁、Claude Sonnet 5、Gemini Nano Banana 2 Lite／Omni Flash）。orchestrator、news-daily、article-checker 三層各自用背景（`run_in_background`）派工後提前結束回合，反覆「假等待」僵住——已停止的代理不會因子代理完成而自動醒來（完成通知送到主迴圈／父代理，不喚醒已結束回合者），Step 2c-bis→2f 永遠走不到，稿件停在草稿、Notion 頁面全空，靠人工多次喚醒才走完寫回。既有 2f-bis 防的是「假完成回報」，攔不住這個「派完就結束回合」的上游肇因。

- `notion-orchestrator.md`：Step 2c 新增「派工方式硬性規則」——子代理一律前景（阻塞）派發（同一則訊息放多個 `Agent` 呼叫取得並發），禁止 `run_in_background`／非同步；必須在同一回合阻塞等所有子代理回傳才往下走 2c-bis→2f；2d 查核代理同理；附自我檢查（發現自己「正要派完就結束回合等通知」即為 bug）
- `notion-orchestrator.md`：2f-bis 補「孿生防線」交叉引用——本節防「沒寫回卻回報完成」，Step 2c 防「派完就結束回合、永遠走不到寫回」，同屬一個失敗家族的一體兩面

**補記錄：ETF 長青內容「發布後維護」功能（先前 session 完成、未即時 commit，本次一併補推）**

此功能實作於先前 session 但未寫入 CHANGELOG、也未推送，本次補上紀錄後隨這批一起上。內容為「ETF 懶人包是長青內容，發布後需定期更新成分股／配息／費率」的維護機制：

- `etf-explainer.md`：新增「發布後：歸檔與長期維護」一節——ETF 稿發布後移入 `archive/` 並定位為 living document（SSoT），附維護卡格式（線上位置／一手來源／配套配件／時效數據與複查時點）與「數月後成分股／配息變動時」的更新流程
- `lint-vault.md`：過時判定新增第 3 類 C「ETF 長青稿複查」——掃描 `archive/` 中 `-etf-` 稿的維護卡「下次複查：YYYY-MM」時點，到期提醒；`valid` 時效標記掃描範圍由 `research/` 擴及 `archive/`

---

## v2.9.2 — 2026-06-08

**notion-orchestrator 新增「完成的唯一定義」硬性閘門（防假完成回報）**

源頭：先泰名下 2 篇即時新聞，子代理產出草稿後直接回報「完成」，實際 Notion 兩頁仍停在「進行中」空白頁，靠人工回 Notion 核對才發現。既有 Step 2f 寫回驗證若確實執行就會攔下，缺口在「跳過 2e／2f 卻回報成功」。

- `notion-orchestrator.md`：Step 2f 後新增 2f-bis 子節。規定一筆稿件只有在 Step 2f 的 `notion-fetch` 實際回傳「內文非 blank-page ＋ 摘要／重點有值」後才能標「完成」；禁止以「草稿已產出」冒充「已寫回」；無法寫回時一律回報「僅產出草稿、尚未寫回」並附草稿全文，不得標完成、也不得標進行中後就結束（會變成下一輪才被 Step 1 回收的孤兒頁）

---

## v2.9.1 — 2026-06-04

**新增「單一受訪者／單一觀點來源的斷言收斂與觀點歸屬」原則（查核式收斂回饋升規範）**

源頭：先泰一篇 deep-analysis（Lenny's Podcast 逐字稿）經查核式收斂後的 10 處改動，收斂為可複用原則。把「受訪者觀點」誤寫成「作者強判斷」是 Podcast／talk 逐字稿類來源的系統性失誤。

- `deep-analysis.md`：「寫作原則」段新增完整原則（斷言收斂、觀點歸屬、不做無來源群體歸納、小標不替受訪者下結論，含對照表），出稿前自查清單補第 16、17 條
- `draft-polish.md`：新增精簡版同名原則（NotebookLM 草稿同屬單一講者來源高發區），自查清單補第 6 條，指向 deep-analysis.md 完整說明
- `article-checker.md`：Step 4 邏輯錯誤分析新增「斷言過度／來源歸屬不當」查核項，作為涵蓋所有稿型的 QC 網

**受訪者身分前置（同次回饋 A 項）**

- `deep-analysis.md`：「單一受訪者」原則補第 5 點（身分職稱與 credibility 必須在前兩段一次交代、不前缺後補）；開場 Hook 同步要求帶出受訪者身分

**破折號全面收緊為 0 次（產出文章一律不使用「——／—」）**

- `bwt-style-guide.md`：破折號規則由「限次數」改為「全文 0 次」，適用範圍涵蓋產稿與所有存檔 vault 筆記；唯對話即時生成放寬
- `draft-polish.md`（每 600 字最多 2 次 → 0）、`etf-explainer.md`（最多 2 次 → 0）同步歸零
- `article-checker.md`：Step 3 新增破折號 0 次查核（先前 QC 網未檢查破折號，是落實漏洞）
- `news-daily.md`：修正一個自身使用破折號的 ✅ 示範句
- `bwt-iframe-visual-component.md`：配件破折號由「最多 1 次」改為「0 次」，對齊 `bwt-visual-checklist.md`（修掉兩檔長期矛盾）
- news-daily／tutorial-article 無自有破折號條文，透過 bwt-style-guide 繼承生效

---

## v2.9.0 — 2026-06-01

**新增 `bwt-visual-checklist.md` 子規範（視覺配件工作流與交付 Checklist）**

- 從 memory 與既有規範收斂出單一 checklist：產品類型判斷（iframe vs 16:9 圖卡）、16:9 主視覺 SOP、iframe spot-check 8 項、禁用破折號、本地優先 iterate、預覽用 URL 不截圖
- 下游引用更新：`bwt-design-standard.md`（子規範清單）、`visual-asset.md`（讀取順序與破折號規則改引此檔）、`bn-claude-code-init.md`（安裝清單補視覺製品 Skills 與共用規範分層）

**`lint-vault.md` — Memory 健康檢查擴充（3 類 → 6 類）**

- 新增 D 類 retention 標記過期（`sunset-by` 到期、`quarterly` > 90 天、缺 `retention:` 欄位）
- 新增 E 類升級候選（同主題 ≥ 3 條 feedback memory 建議整併進 Skill）
- 新增 F 類容量警戒（memory ≥ 50 條、MEMORY.md > 12,000 字元）
- `project_*` memory 完成或 > 60 天無更新建議移 vault `projects/`

**`bwt-style-guide.md` — 白話化規則補充**

- 新增文學成語／軍事用詞對照表（事必躬親→親力親為等），要求出稿前 grep 自查
- 新增白話轉場詞密度節制（「白話說」「換句話說」單篇 ≤ 2 次）

**`notion-orchestrator.md` — 兩項更新**

- 摘要欄位公式依稿件類型分流：即時新聞以「講清楚事件」為先、不用疑問句結尾；教學／分析／潤稿可用「題旨 + 關鍵提問」
- **新增「進行中空白頁回收」規則**：掃描除「未開始」外一律加掃「進行中」，對內文為空的孤兒頁判定為上一輪寫回失敗、併入重跑（2026-06-01 踩坑：先泰名下五篇全卡在進行中空白頁）

---

## v2.8.0 — 2026-05-20

**新增 `visual-asset` Skill（互動視覺配件生成）**

- 新增 `visual-asset.md`：將文章資料、清單、CSV 轉化為符合 BN 視覺規範的 HTML 配件，支援兩種產品類型：
  - **iframe 嵌入式互動配件**（時間軸、氣泡圖、橫條排行、地圖等）：嵌入文章內、桌機 + 手機雙軌、自動托管至 GitHub Pages（`terrylee-lang/bnext-visuals`），產出可直接貼到後台的 iframe 嵌入碼
  - **16:9 主視覺圖卡**（1920×1080 PNG）：文章封面／社群分享／Notion 封面用，單張靜態圖
- 動手前先判斷產品類型；與 `visual-brief`（主視覺發想）、`bwt-html-table-component`（表格元件）的職責分工於檔內說明

**新增 `bwt-iframe-visual-component.md` 子規範**

- iframe 互動視覺配件的具體實作規範：HTML 結構模板、桌機 + 手機雙軌設計強制規則、Logo 加入規範
- Design tokens（色彩、字體、間距、圓角）一律引用 `bwt-design-standard.md`，本檔不重複定義
- 目前僅 `visual-asset` Skill 套用

**bwt-design-standard 同步更新**

- 子規範索引表新增 `bwt-iframe-visual-component.md` 條目，與既有 `bwt-html-table-component.md` 並列

**README 同步更新**

- Skill 列表新增 `visual-asset.md` 與 `bwt-iframe-visual-component.md` 兩條目（編輯部專用）

---

## v2.7.2 — 2026-05-15

**news-daily / article-checker（官方公告編譯六項查核同步補強）**

- `news-daily` 新增 **Step 2c：官方公告型素材處理流程**——當素材主體是單一公司官方公告（OpenAI／Apple／Google 等官方部落格、release notes、newsroom、產品發表會，及 9to5Mac／TechCrunch／The Verge 等轉述）時，動筆前必須完成六項 pre-check：
  1. publish date 字串提取（不依賴 URL path segment，必須用頁面內可見日期；補台灣時間轉換）
  2. 產品架構 verb 對應檢查（control/access/monitor → 遠端操控／存取／監看，禁用「進駐」「跑在」「整合進」等升級詞）
  3. 平台／版本支援度分階表（GA／preview／coming soon／Enterprise only 必須明寫）
  4. 技術名詞處理規則（secure relay 不擴寫成「加密通道」、sandboxed 不擴寫成「容器化」；白話化是「解釋」不是「擴寫」）
  5. 合規／法律術語語氣保守（HIPAA／GDPR／SOC 2 等必加「符合資格的客戶」「視部署條件而定」等限定詞，禁用「終於可以 X」）
  6. 旁支新聞分流（與原文主標題對照，砍掉非本次公告主體的次要訊息）
- 出稿前自查清單新增第 14 條（六小項自查），順手修正原檔兩個 `11.` 編號重複的 bug
- `article-checker` 新增 **規則 D：官方公告型稿件六項強制查核**（D-1 至 D-6），與 news-daily 的 Step 2c 形成兩道閘門：
  - D-1：日期必對應官方頁面 publish date 字串
  - D-2：產品架構 verb 升級檢查（擴寫黑名單）
  - D-3：平台／版本支援度逐項對應
  - D-4：技術名詞擴寫黑名單（secure relay／sandboxed／TLS／in-memory cache／zero-trust）
  - D-5：合規語氣保守度檢查
  - D-6：旁支新聞混入檢查
- 觸發背景：2026-05-15 OpenAI Codex 手機版公告稿件，七個失準點（日期 5/7 vs 5/14、「Codex 進駐手機版」、Windows 漏寫 coming soon、secure relay 擴寫成加密通道、HIPAA 寫太滿、結尾混入 Pro 方案 100 美元與 GPT-5.5）。原本的 article-checker 攔不到任何一項，因為這些不屬於既有查核維度（人名／公司名／財務數字／引言／技術術語）。產稿端、查核端兩邊都需要規則，單改一邊只攔一道閘門
- 同步新增 memory：`feedback_official_announcement_translation.md`（高優先級）

**bwt-style-guide（教學文／方法論稿開頭與結尾規則）**

- 新增第 6 條規則：開頭從「讀者利益」切入，避免「某公司宣布／某 CEO 主張」的權威切入；結尾用「先 X，再 Y；先 X，再 Y」遞進句式收束，比文學比喻更有記憶點
- 觸發背景：codex polish 教學文修潤學到的兩個動作（原 memory `feedback_codex_polish_lessons.md` 四動作中的前兩條升為共用規範）

---

## v2.7.1 — 2026-05-06

**notion-orchestrator（安全與寫回格式修正）**

- Step 1 token 處理改用 inline 環境變數注入模式（`NOTION_TOKEN=$(jq ...) curl ...` 同行寫法），不再 export 到 shell session。明確列出禁止事項：`export`、`echo`、寫入暫存檔、`set -x`、字面值貼 prompt
- Step 2 起強制走 MCP（`mcp__claude_ai_Notion__*`）：fetch、update-page、create-pages 不再呼叫 curl + Notion API。MCP 自動處理 markdown↔block 雙向轉換，避免手動建 block JSON 踩坑
- 新增頁面內文精確 block 規格：3 個 paragraph 行內粗體（重點一／二／三）→ divider → paragraph 行內粗體（摘要）→ divider → 正文。明確禁止 `bulleted_list_item` 與 `heading_2` 小標
- 教學文例外：頂部不放重點 paragraph，章節結構即為重點脈絡
- 觸發背景：5/6 子代理為了 fetch Notion 從 settings 讀 NOTION_TOKEN 並 export 到 shell，且寫回時用 heading + bullet 而非 paragraph 行內粗體

---

## v2.7.0 — 2026-05-04

**新增 Skill**

- `visual-brief.md`：主視覺發想 Skill，將稿件翻譯為結構化視覺 brief，作為 Claude Design 等設計工具的輸入。編輯不需直接下設計 prompt，就能產出風格一致的主視覺

**bwt-style-guide（風格規範大幅擴充）**

- 適用範圍明確擴大：除產稿 Skill 外，凡是 Claude 產出且寫進 vault / memory 的 Markdown 檔案（research、projects、archive、daily-notes、memory、Notion 寫回等），一律適用所有風格規則
- 新增 **SI 單位前綴譯法**：tera-（太瓦 TW）、giga-（吉瓦 GW）、mega-（百萬瓦 MW）；優先保留英文縮寫，避免「兆瓦」歧義
- 新增 **日期格式規範**：月日寫成「4 月 27 日」不用「4/27」斜線；ISO 格式僅限 frontmatter / URL 等機器讀取欄位
- 新增 **技術性稿件白話化原則（五條）**：技術名詞第一次出現加中文白話、小標問句化、技術細節壓縮為對讀者影響、抽象百分比改具體例子、官方語直譯改白話翻譯。AI／程式／晶片／API 主題預設啟用，字數彈性允許超出原規範 30–60%

**article-checker（強制驗證規則）**

- 新增「商業行為動詞」與「技術術語」精準度檢查（收購 vs 授權、zero-day exploit vs security vulnerability 不可語意升級）
- 新增 **規則 A：時效性財務數字必須 web_search 抓最新值**（ARR、估值、融資、用戶數、市值），警戒「二手引述舊里程碑」陷阱
- 新增 **規則 B：一手素材覆蓋檢查**（官方 X、newsroom、SEC filing、財報），單靠 Bloomberg／NYT 等二手報導常裁掉關鍵規格
- 新增 **規則 C：未成交事項語氣控制**（target / expected / reportedly 不可譯為確定式；IPO 估值以區間呈現；併購未交割不可寫「買下」）

**全產稿 Skill：URL → inbox/ frontmatter source 匹配**

- `news-daily`、`deep-analysis`、`tutorial-article`、`social-post`、`etf-explainer`、`notion-orchestrator` 收到 URL 時，先掃 `~/Claude Project/inbox/` 所有 md 檔案的 frontmatter `source` 欄位（嚴格相等優先；其次比對 hostname + path，忽略 query string／fragment／尾端斜線），命中即 Read 本機全文，未命中才走 `web_fetch`
- 為什麼：Web Clipper 抓下的素材 frontmatter `source` 即原始網址，自動對照本機檔案；本機讀取無 fetch 失敗、無付費牆、避開反爬限制

**deep-analysis（自檢清單擴充）**

- 新增第 14 條：事實必須能回溯至資料來源區，禁止「來源未列、正文有寫」幽靈段落
- 新增第 15 條：商業動詞（收購／授權／投資）與技術術語必須與來源原文精準對應

**news-daily**

- 摘要規則：明訂下限 50 全形字元、目標 60–75，避免事件交代不足；字元數計算規則明確化（中文／全形標點 = 1，英文／數字／空格 = 0.5），寫完標註實際字元數供驗證
- 新增禁用詞：「一句話來說」「先說結論」等公式化引導句一律 0 次。即時新聞的「結論先行」靠倒金字塔導言完成，不靠引導語

**lint-vault（同步 research 新格式）**

- 內文區塊檢查改為 `## 為什麼現在重要` / `## 發生了什麼` / `## 我怎麼看` / `## 延伸` 四區塊（舊 Summary／Key Points 格式視為歷史遺留，提示但不報錯）
- 新增 H1 主張句檢查：H1 應為完整主張（至少 10 字），不是單純主題標籤

**README / bn-claude-code-init**

- Vault 整合段落：Wiki Ingest 改為「每日收工批次處理」（Step 2.5），不再每次產稿後即時跑——降低中斷摩擦、避免一次性主題堆積成 stub
- bn-claude-code-init：內容讀取優先順序加入 inbox frontmatter source 匹配；風格規範段落補上技術白話化原則

---

## v2.6.1 — 2026-04-15

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

## v2.6.0 — 2026-04-15

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

## v2.5.0 — 2026-04-08

**新增**
- `lint-vault.md`：Vault 健康檢查 Skill，掃描孤兒筆記、缺連結、過時內容、格式問題
- `bn-claude-code-init.md`：新同事初始化指南，丟給 Claude Code 即可自動設定完整工作環境

**README 更新**
- Skills 一覽表新增 lint-vault
- Vault 資料夾結構補上 draft/ 和 personal/
- 新增「快速上手」段落，指向初始化指南

---

## v2.4.1 — 2026-04-07

**README**
- 新增「進階：Obsidian Vault 整合」段落，說明如何搭配 Karpathy wiki 架構讓產稿知識自動累積

---

## v2.4.0 — 2026-04-02

**新增 Skill**

- `retrospective`：新增工作覆盤 Skill，session 結束後掃描對話，整理值得保留的偏好與教訓，產生記憶或 Skill 更新建議

**修改 Skills**

- `deep-analysis`、`news-daily`：更新內容邏輯
- `notion-orchestrator`：精簡執行步驟

---

## v2.3.3 — 2026-03-26

**draft-polish、social-post**
- 新增風格規範讀取步驟，開始前先讀取 `bwt-style-guide.md`
- `social-post` 說明限縮為「人名、公司名與兩岸用語」，避免過度套用不適用的寫作規則

---

## v2.3.2 — 2026-03-26

**notion-orchestrator**
- Bloomberg 讀取改為條件式：腳本存在才執行，不存在自動 fallback 到 Playwright，其他使用者不受影響
- 硬寫的 `/Users/terrylee` 路徑改為 `$HOME`，跨機器相容

---

## v2.3.1 — 2026-03-26

**修正**

- `news-daily`、`deep-analysis`、`tutorial-article`：`bwt-style-guide.md` 路徑從硬寫的 `/Users/terrylee/...` 改為 `~/.claude/agents/bwt-style-guide.md`，其他使用者安裝後可正常讀取
- `bwt-style-guide.md` 加入 repo，安裝後即可使用，不需額外手動複製

---

## v2.3.0 — 2026-03-25

**新增 Skill**

- `headline-generator`：新增標題生成 Skill，支援《數位時代》與《商周》兩種風格
  - 固定 5 種角度（企業轉型、人物決策、產業衝擊、數字驅動、工具教學）+ 動態補充角度
  - 工具教學型標題有獨立寫作規則（字數、感嘆號、利益承諾）
  - 輸出含首選/備選推薦理由，支援互動迭代

---

## v2.2.0 — 2026-03-24

**notion-orchestrator**

- 新增「建議標題」欄位支援（子代理動筆前優先採用編輯建議標題）
- 新增「社群貼文」、「字數」屬性欄位寫回
- 新增「負責人」欄位說明（支援「跑先泰的稿件」觸發模式）
- 稿件全文寫回策略改為 `replace_content`，解決含 code block 長文的 JSON 解析錯誤
- 新增 Bloomberg 付費內文特殊讀取路徑（使用 `bloomberg-fetch.js`），含 session 過期的處理說明
- 摘要欄位字數上限從「重點摘要」三合一格式拆分為獨立的「摘要」（75字）＋「重點一/二/三」（各50字）

---

## v2.1.0 — 2026-03-20

**notion-orchestrator**
- Step 1 改用 Notion API `curl` 查詢，取代原本無法列出條目的 `notion-fetch`
- 新增 `$NOTION_TOKEN` 環境變數支援
- 新增「負責人」篩選能力（支援「跑先泰的稿件」類觸發方式）
- 新增情境 A（指定負責人）/ 情境 B（全部待辦）兩種查詢模式

**環境設定**
- Playwright MCP 改用 persistent profile（`--user-data-dir`），保留登入 Cookie，解決 WSJ / Bloomberg 付費牆問題
- `settings.local.json` 新增常用工具 allow 清單，減少手動批准頻率

---

## v2.0.0 — 2026-03 以前

初始版本，包含 7 個 Skills 基礎架構。
