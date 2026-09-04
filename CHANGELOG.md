# CHANGELOG

《數位時代》Skills 庫的完整版本紀錄。

> **更新流程**：每次更新 Skills 後，在此檔案最上方補上版本號、日期、異動說明，再 commit & push。
> 版本號規則：新增 Skill → 次版號 +1（v3.x.0）；修改既有 Skill 或交接文件 → 修訂號 +1（v3.x.x）。

---

## v3.2.5 — 2026-09-04

**素材只給題目、沒給路徑時，會漏看 inbox 直接 web_search：新增「先掃 inbox 再對外找」前置步驟**

源頭：Terry 指出，請 longform 先出大綱時，Claude 常常自己跑去 web_search 找資料，浪費時間不說，等讀到 inbox 裡早就備好的指定素材後，大綱還要重工一次。

根因：`bwt-style-guide.md`「來源讀取優先序」與 `longform.md` Step 1a 的 inbox 檢查，原本只在使用者已經給出具體路徑／URL／Notion 來源欄位時，才會去比對 inbox frontmatter 的 `source`。但 Terry 的日常習慣是素材先丟進 `inbox/`、再口頭下產稿指令，訊息裡不會每次重複點名「inbox 那篇」。使用者只給題目、沒有具體路徑或連結的情境下，舊規則完全沒有觸發「先掃 inbox」的動作，導致直接跳去對外找資料，用到立場或細節不同的替代素材。

### bwt-style-guide.md：「來源讀取優先序」章開頭新增前置段落

- 進入既有的讀取流程前，先確認「有沒有素材需要主動找」：沒有具體路徑／連結時，先用題目關鍵字掃一輪 `inbox/`（`ls`／`grep`，不只是比對 frontmatter），命中就當作指定素材優先讀取；掃過確認沒有才進入 web_fetch／web_search
- 落點選共用章而非只修 longform，理由是這個「先查本地、再對外找」的邏輯不限長文，news-daily、deep-analysis 等其他 Skill 都經由指標引用同一節，一次修好全部受惠

### longform.md：Step 1a「指定素材的來源」清單補第 4 項

- 加入「`inbox/` 掃描命中的檔案」為指定素材來源之一，並註明不准跳過這一步直接對外找資料，與共用章節新前置段落對齊

### CLAUDE-template.md：交接摘要同步

- 「重點只記三件事」擴充為四件事，新增「先掃 inbox 再 web_search」的觸發情境，避免同步中新同事只看到舊摘要而漏掉這條

---

## v3.2.4 — 2026-08-04

**媒體是查核依據，不是正文出處：新增「正文的來源歸屬分界」**

源頭：亞馬遜 3 兆美元市值稿（news-daily）正文寫「據《Bloomberg》報導，當天盤中一度大漲 5.3%，《CNBC》報導終場收漲 4%」，Terry 指出股價是任何人查得到的客觀數據，「媒體報導可以當作查核時候的依據，但實際在寫新聞稿的時候，為什麼需要在股價上引用其他媒體？這根本毫無邏輯」。同稿另有兩處同型錯誤：亞馬遜自家財報公告的營收與 EPS 掛《CNBC》、微軟與 Alphabet 自家財報公布的雲端成長率也掛《CNBC》。

根因是把媒體報導的兩個身分混用：**查核階段的交叉驗證依據**被寫成**正文裡的出處歸屬**。副作用不只囉唆，掛名會暗示這個數字是「某家媒體的說法」，讀者誤以為它要靠該媒體的信用才成立；反向失準同樣存在（媒體專有彙整被寫成無出處的直述事實）。

### bwt-style-guide.md：新增「正文的來源歸屬分界（跨 Skill 共用）」章

- 兩張清單分界：**直接敘述**（股價與市值、公司自己公告的財報數字、政府與交易所數據、法院文書與 SEC filing 明載事實）vs **必須掛出處**（媒體的詮釋歸因、專有彙整與自訂指標、分析師預估值、獨家未確認消息、引言）
- 判準一句話：這項資訊被質疑時，靠得住的是原始事實本身，還是那家媒體的信用？
- 落點選跨 Skill 共用章而非塞進 news-daily 引述規範，理由是編譯外電的 Skill 不只一支（longform、draft-polish 同樣適用），規則住共用章才不會只修一支

### news-daily.md：三處對齊

- **導言規則改寫**：原本無條件要求「帶出原始來源媒體名稱」，正是這次失誤的直接誘因。改為導言含詮釋、專有彙整或引言時才順勢帶出；只有客觀數據時不掛媒體，名稱後移到正文首次出現詮釋處
- 引述規範補「引言以外的歸屬分界」指標，指向 style guide 新章、不複製規則內容
- 出稿前自查清單第 4 項補「股價、財報數字等客觀數據是否誤掛媒體出處」

### article-checker.md：Step 4「斷言過度／來源歸屬不當」改為雙向檢查

- 既有落點加掛，不新增查核維度：客觀數據被掛媒體出處、媒體專有彙整被寫成直述事實，兩個方向都要抓

### 未改動（刻意）

- **deep-analysis.md:117**（前兩段帶出原始來源名稱）：該條要求交代的是素材本身（Podcast、報告、專訪）與受訪者 credibility，不是客觀數據的歸屬，與新章不衝突
- **亞馬遜 3 兆美元那篇稿件**：Terry 裁定「只立規範，這篇不動」，已推送的內容池 id 198 保持原樣

---

## v3.2.3 — 2026-08-03

**素材優先於結構：規範裡的「必答」槽位不再被讀成生產指令**

源頭：Whatnot 產品長 PM 稿（longform，分析型）為了滿足品質 rubric 的「台灣關聯」必答項，生出「台灣科技業職級與薪酬區間普遍壓縮、資深產品人才供給更薄」這種零查證支撐的句子，Terry 要求整節刪除。他指出的根因不是「台灣關聯這一節不好」，而是**規範裡任何「必須有 X 段」的槽位，都會逼模型生產素材裡沒有的內容**，代價有二：查核時找不到出處只能標「無法核實」，讀感也因為岔開主軸而散掉。原則是「所有 Skill 都應以素材本身出發，不先射箭再畫靶」。

修法採全域規則加單點改寫，不逐條補 rubric（逐條補會變打地鼠：本次僅用五個關鍵字掃描就找到三個同型槽位，且 Skill 會持續新增）。

### bwt-style-guide.md：鐵律 2「無幽靈段落」增列子條

- 明訂各 Skill 的「必答」「必須有這一節」「全文至少一個 X」與**稿單備註列的項目**，一律是檢查提示不是生產指令；素材撐不住時的正解是在編輯附註寫明「素材無此面向，略過」，略過不算缺陷
- 落點選在鐵律區而非新增獨立章節，理由有二：鐵律明訂「所有產稿 Skill 在產稿階段就要遵守、article-checker 查核時逐項對照」，規則住這裡會被查核掃到而閉環；掛在既有鐵律 2 底下不動編號，不打斷下游對「鐵律 4」「鐵律 13」「鐵律四」的引用（依 maintenance-protocol「預設修剪、不增建」）

### longform.md：品質 rubric「擴散與台灣關聯」該列倒轉預設值

- 逃生口從句末提到句首，語氣對調：先判素材撐不撐得住，撐不住直接略過；刪去「台灣關聯是本刊讀者買單的理由，不是加分項」這組會蓋過全域規則的措辭
- 新增反例「憑印象斷言『台灣普遍是 X』是製造查核破口」，並指定要跟讀者對話時改用條件句、不下事實斷言

### 未改動（刻意）

- **rubric「洞察深度」「產業戰役定位」**：同屬會誘發生產的槽位，但既有逃生口寫得夠清楚（「找不到時明說…不硬掰」），由新增的全域鐵律覆蓋即可
- **反共識自檢、誠實揭露限制**（`longform.md`、`deep-analysis.md`、`tutorial-article.md`）：這類「必須有」是要求你去看反方、去揭露侷限，不會產生假事實，砍掉反而弱化品質
- **deep-analysis.md、tutorial-article.md**：過渡期退路、預計廢除，靠全域鐵律覆蓋

---

## v3.2.2 — 2026-08-03

**對齊 Pluto 後台推送規格：資料來源改為結構化資料，新增文章元件語意標記**

源頭：IT 工程同事提供 `pluto-markdown-plugin-format` 規格書，定義 Pluto CMS 的文章格式（正文 Markdown ＋ 富媒體包成 JSON 塞進 `<data type="application/json">`，共 9 個 plugin）。比對 `content-pool-add` 的 MCP schema 後確認兩件事：

1. **文末資料來源那一行早已是結構化資料**。`reference` 參數要的是 `[{"title":"","url":""}]`，而既有格式「`資料來源：[《TechCrunch》](URL)、[Anthropic 官方公告](URL)`」正好是 title + url 成對，不需改寫作方式，只需在規範裡確立它的身分。這讓 2026-07-01 先泰三篇的漂移問題（資料來源被改成條列、署名整行遺失）從審美要求升級為功能要求：格式跑掉不是不好看，是解析失敗。
2. **稿件層不能寫 plugin JSON**。三個理由：品管流程要貼 Codex 審稿，JSON 區塊無法審；`article-checker` 的文字掃描會失準；修訂輪要求 `old_string` 從 Read 原樣複製，在單行轉義 JSON 裡改中文是該紀律最容易崩的地方（中文稿常有引號，一個引號跑掉整塊失效）。因此採「語意標記在上游、JSON 在最後一哩」三層架構，本次只做前兩層。

### bwt-style-guide.md：「署名格式」章補結構化聲明

- 明寫解析規則（取 `資料來源：` 後整行、以「、」切開、`[名稱](URL)` → `title`／`url`），以及違反後果
- 明寫 `reference` **只取文末那一行、不取 Notion「來源 1／2／3」**：那三欄是 AI 讀的素材入口，文末是給讀者的引用清單，兩者不等價（`web_search` 補的來源在文末不在素材欄，讀了沒引用的則相反）。此區別經 Terry 確認

### bwt-style-guide.md：新增「文章元件語意標記（推送 Pluto 用）」章

- 定義四個元件標記：抽言（essence）、延伸閱讀（inlinelinks）、重點框（textblock）、影音（video）
- 沿用「行首粗體 ＋ 全形冒號」形狀而非自創語法，因既有的「**摘要**：」「**重點一**：」已實證能通過 Notion MCP 寫回不變形（對照 `notion-orchestrator.md` 手動建 block JSON 的踩坑紀錄）
- **只定格式、不要求 AI 主動產出**（Terry 決定）：抽言該挑哪一句是編輯判斷，不由 AI 決定。元件皆為選用，但一旦要寫就必須照表，推送層不做猜測
- 圖片與圖說刻意未納入：`picture` plugin 需 xs／s／m／l／o 五種尺寸網址，取得機制未向後台確認。現在定形狀，答覆回來很可能要重定，反而製造遷移成本

### notion-orchestrator.md：欄位表新增三個選填欄位

- SEO 標題（`page_title`）、permlink、標籤（`tag`），全部選填，未建立不影響既有產稿流程（同事各自持有自己的產稿資料庫）
- 新增「對照 Pluto `content-pool-add` 參數」小節，並明寫本流程**不自動填寫這三欄、不自動推送**，維持既有紀律（內容池寫的是正式後台，Notion 為 single source of truth、內容池是單向下游）

### article-checker.md：格式守護擴及元件標記（grep 下游同步）

改完 grep 掃描下游，發現 `article-checker` 是唯一真正的漂移風險點：元件標記由 Terry 手動加入，加入後稿件會進 Codex 多輪修改，而 checker 每輪都會「輸出修訂後全文」。若它不認識 `**抽言**：` 是必須原樣保留的標記，就會像過去打散文末兩行一樣把它改成 blockquote。

- Step 3 機械格式必查新增一條：元件標記原樣保留，不得改成 blockquote／條列／一般段落
- 既有的文末格式那條補一句說明「這一行推送時會解析成 `reference`，格式跑掉等於解析失敗」，把審美理由換成功能理由
- 「格式守護是每輪固定動作」與「第二輪查核範圍」兩處一併納入元件標記

其餘下游確認**不需改動**：`draft-polish`、`news-daily`、`longform`、`tutorial-article`、`deep-analysis`、`notion-orchestrator`、`SMOKE-TEST` 對「署名格式」皆為指路型引用、不複寫規則內容，新增段落自動繼承；`bn-claude-code-init.md` 建庫清單只列「最少要有」的必填欄位，新增三欄為選填故不動。全庫僅 `bwt-style-guide.md` 一份持有資料來源格式規則，無平行副本（符合 maintenance-protocol「預設修剪、不增建」）。

### 規範對齊實際資料庫（同批發現的既有漂移）

建三個新欄位時抓 Notion schema 對照欄位表，發現規範與實際資料庫早已不一致，且會造成靜默失敗：

- **負責人**：規範列 8 人（先泰、柔瑋、美欣、若彤、建鈞、祈安、宗穎、莞淇），資料庫實際 6 人（先泰、美欣、若彤、建鈞、祈安、**育萱**）。規範有而資料庫沒有的三人已離職或不再使用表單（Terry 確認），育萱則從未寫進規範。後果具體：說「跑柔瑋的稿件」時 API filter 送出不存在的 select 值，**靜默回傳 0 筆而非報錯**，看起來像「今天沒稿子」
- **稿件類型**：規範列 6 種，資料庫只有 3 種（即時新聞／深度分析／教學文）。缺的潤稿、社群貼文、文章查核經 Terry 確認**不走 Notion 流程**，一律在對話中直接處理
- 處置（Terry 裁決）：資料庫負責人選項保留 6 人不動（多留選項無害，日後有人加入不必重建），規範改為與資料庫逐字一致；稿件類型規範砍成 3 種

改動位置：`notion-orchestrator.md` 欄位表（稿件類型、負責人兩列）、Step 1 篩選模式範例（「跑柔瑋的待辦」→「跑育萱的待辦」）、負責人選項清單、類型→子代理對照（移除 draft-polish／social-post／article-checker 三個映射）；`~/.claude/CLAUDE.md` 的 Skill 觸發語表。欄位表新增一句「本表須與資料庫 select 選項逐字一致」並說明靜默失敗的機制，避免同一漂移再發生。

### ETF懶人包納入 Notion 流程；「社群貼文」欄位停用

grep 掃描時發現 `etf-explainer.md` 的 Notion 寫回段要求填「社群貼文」欄位，與「社群貼文不走 Notion」的裁決衝突。追下去發現更根本的問題：**ETF懶人包從來不在稿件類型選項裡**，所以那整段寫回路徑是斷的。Terry 裁決：ETF 補進類型選項、社群貼文欄位停用（實務上貼文內容不在此工作流處理）。

- Notion 資料庫：`稿件類型` 新增 `ETF懶人包`（green），既有三個選項與現有資料完整保留
- `notion-orchestrator.md`：欄位表與類型→子代理對照新增 `ETF懶人包`→`etf-explainer`；「社群貼文」欄位標為**目前未使用**、欄位保留但不填；原「例外（社群貼文 social-post）」段改為「例外（ETF懶人包）」，說明 ETF 內文結構不套三大重點格式
- `etf-explainer.md`：Notion 寫回段的「社群貼文欄位填寫步驟型重點貼文」改為**明確禁止填寫**，需要貼文改走 `social-post`
- 署名全名對應表補「育萱 → 林育萱」，並加一句：其餘負責人的署名全名未登記，遇到時向 Terry 確認、**不自行推測姓氏**（人名錯誤會直接見報）

### IT 答覆到位：新增 bwt-pluto-push-spec.md 記錄裁決

同日取得 IT 工程同事對 7 個問題的答覆（Q6 textblock 樣式未獲回答）。**關鍵發現：現行規格書是對方以 AI 產出的，已確認含實質錯誤，對方正在重產。** 因此不照現版全面實作，只把人親口確認的裁決寫進新檔 `bwt-pluto-push-spec.md`（推送層規格的落點，與稿件層的 style-guide 元件標記章分工），細節等新版規格書補完。

已確認裁決：

- **內容池會原樣保留 plugin block**：`content-pool-add` 的 `content` 可直接帶 `<data type="application/json">`，階段 3 的轉換層因此可行
- **根 class 一律 `.mcp-ai-content`**：規格書寫的 `.wrapper-html` 是對方 AI 寫錯，非兩套並存
- **JSON 一定要單行**：規格書「Available Plugins」段的 pretty-print 範例只能看欄位結構，不可照抄。轉義層數對方未逐字回答，結合唯一 Correct 範例判定為單層正常 JSON 轉義、不做雙重轉義，此推論已在新檔標記，首次推送失敗時先回查此點
- **`picture` 的 `src` 取自 `media-upload` 回傳的 object**（內含 URL）
- **後台沒有表格 plugin**，表格一律自刻 HTML 走 `iframe` plugin，適用 `.mcp-ai-content` 隔離規則
- **有 stage 環境**（接法待問）

仍待補、明令不得憑猜實作：《數位時代》的 `media-upload` site 值（現行 enum 無 `bn`，對方稱要用 bn／mt／sd 但 bn 與 sd 不在 enum 內）、stage 接法、`textblock` 的 type1 到 type6 視覺、新版規格書。

`bwt-style-guide.md` 元件標記章的圖片註記同步更新（取得機制已確認，卡點改為 site 值未定），並加一行指向 `bwt-pluto-push-spec.md`。

`bwt-html-table-component.md` 仍未改寫：現行規範是為「直接貼後台」寫的，未套 `.mcp-ai-content` 根容器與前綴 class 要求，等新版規格書到手後一次改，避免照錯誤版本改兩遍。

### 原始 7 個問題（存證）

送出給 IT 同事的問題如下，答覆見上節：

1. 內容池（`content-pool-*`）是否解析 `<data type="application/json">` plugin block，或 plugin 格式僅適用 Pluto 編輯器內編輯
2. **根 class 兩份規格不一致**：規格書寫 `.wrapper-html`，`content-pool-add` schema 寫 `.mcp-ai-content`。掛錯會讓「只在框內生效」的保護失效、樣式外溢到整站
3. JSON 轉義層數：規格書文字說「escaped JSON serialized as a single-line string」像是要兩層，唯一的 Correct 範例卻只有一層（正常 JSON 的 `\n`／`\"`）。多做一層讀者看到反斜線，少做一層遇引號即壞
4. `picture` 的五尺寸網址從哪來（`media-upload` 似乎只回單一 URL）；另 `media-upload` 的 `site` enum 無《數位時代》選項，只能選 `other`
5. 表格無專用 plugin，是否一律走 iframe 自刻（影響 `bwt-html-table-component.md` 是否要整套改寫）
6. `textblock` 的 type1 到 type6 各自視覺
7. 有無 staging 環境（上述每題的驗證方式都是「推一次看看」，但內容池是正式後台）

另：規格書檔案結尾被截斷（第 524 行的 ```html 未關閉，Incorrect 範例整段缺失），已請對方補完整版。

因此 `bwt-html-table-component.md`、`bwt-iframe-visual-component.md`、`visual-asset.md` 本次一律不動；轉換程式與推送流程亦未實作。

---

## v3.2.1 — 2026-07-30

**事實查核鐵律補一條：摘要不算「讀到」**

源頭：Boris Cherny 長文產稿時，Bun 改寫案例的數字錯了三輪。第一輪照 `web_search` 的搜尋摘要寫，第二輪察覺要查一手、卻又照 `web_fetch` 的頁面摘要「修正」，把原本正確的「約 50 個工作流」改成錯的「4 個」（那是尖峰同時併發數）；同篇另把約 16,000 個編譯錯誤寫成 1,600 個，並虛構「12 小時內清完」。一手來源（Jarred Sumner 的技術回顧）全程公開可讀，卻到 Terry 把檔案丟進 inbox 才真正讀完。

值得記錄的是**這條知識前一天就存在**：v3.2.0 已寫下「用網頁抓取工具讀 GitHub repo 頁面拿到的是摘要改寫版」，但它住在交接包安裝器的 README 與本 CHANGELOG，產稿流程載入不到。符合 `ops/maintenance-protocol.md` 第四節訊號 1：同一類錯誤重複出現，多半是規則住錯地方。

### bwt-style-guide.md：「事實查核鐵律」→「一、來源紀律」規則 1 增設子條

- 新增「**『讀到』的定義：摘要不算**」：`web_search` 摘要與 `web_fetch` 的小模型頁面摘要只能用來判斷來源值不值得讀，不得作為引用依據；要進稿的數字、引言、時序一律回原文核對，長文用 `Grep` 或章節標題定位再讀該段
- 明寫互補關係：規則 3（一手素材覆蓋）要求去找一手，本條定義找到之後怎樣才算讀了
- 採子條而非新編號，避免規則 4 之後全部重新編號（既有 memory 與 article-checker 以編號互相指涉）
- 未在 `article-checker.md` 另立平行規則：鐵律開頭已載明「article-checker 查核時逐項對照」，依 maintenance-protocol「預設修剪、不增建」不做副本

---

## v3.2.0 — 2026-07-29

**交接包自助化：貼一個 repo 網址，同事的 Claude Code 就能自己把產稿環境裝起來**

源頭：交接包（`bn-claude-code-init.md` ＋ `CLAUDE-template.md` ＋ `SMOKE-TEST.md`）v3.0.0 已就位，但入口在 README 第 144 行，第一屏還是給人看的介紹。實測發現一個決定架構的限制：**用網頁抓取工具讀 GitHub repo 頁面拿到的是摘要改寫版**（要求逐字引用 README 前 30 行，回來的是摘要），clone 指令、檢查點、參數小抄都可能被壓縮掉。所以網址只能承載「一句開機指令」，安裝細節必須 clone 後用 Read 讀本機檔案。

### README：新增「⚡ 開機指令」為第一屏

- 人類端只有一句話：在 Claude Code 說「照 <repo 網址> 的開機指令幫我設定」
- AI 端壓到三步（確認有 Bash 與家目錄寫入權限 → clone → Read `bn-claude-code-init.md` 執行到底），並明寫「不要憑這一頁推導安裝步驟，本頁經網頁抓取會失真」
- 非編輯部同事分流到 `ADAPTATION.md`，不要整套照裝
- 新增「需要本人動手的四件事」表（Notion 連接器授權、自建資料庫＋token、建 `bnext-visuals` repo 開 Pages、Pluto OAuth），各標所在步驟與所需時間
- 新增「這個 repo 交接什麼、不交接什麼」表：明列 5 項刻意不隨附的個人層檔案（`bwt-voice-reference.md`、每日收工 SOP、`ops/` 四件套、memory 政策與筆記格式範本、`bwt-lint.sh`）、缺了會怎樣、要的話怎麼拿。AI 不再去找不存在的檔案
- 補 `git`／`jq`／`poppler`／連接器到前置需求表；`pdftotext` 與 `jq` 的 symlink 要求寫進表格（Claude Code 的 Bash PATH 不含 `/opt/homebrew/bin`）

### 安裝器：人工事項前置化，修掉會讓 AI 中途卡死的反向依賴

- **Step 1 開場先攤開四件人工事項**，並把「建 Notion 資料庫＋integration」升為第 7 題（原本埋在 Step 5b）。修掉原本的順序 bug：Step 5b 要求「把資料庫 URL 記下來，**Step 3** 要寫進 CLAUDE.md」，而 Step 3 早已跑完，AI 照做會卡住。現在 URL 在 Step 1 就到手，Step 5b 只處理 token
- Step 5b 保留一條退路：URL 一時拿不到就先寫佔位符，5b 結束前回頭補並重跑 Step 3 檢查點
- **Notion「各自建置」口徑改乾淨**：Step 5a 檢查點改用使用者自己的資料庫 URL，失敗排查從「請他找 Terry 分享」改為「integration 沒加進 Connections 或 URL 貼錯」；Step 8 移除「把 `notion-orchestrator.md` 內的預設 URL 換成自己的」（與 Step 6c「不要動 repo 內檔案」相矛盾），改走 CLAUDE.md 覆寫規則
- 執行原則新增兩條：開場先講「會寫入 `~/.claude/`，權限詢問請按允許」與「Skills 清單要重開 session 才載入」；以及「不要為了讓檢查點通過而改規範檔」
- Step 2 檔案清單補上 `longform.md`，檔案數檢查點 `≥ 24` 改 `≥ 25`（與 SMOKE-TEST T1 對齊）
- Step 2b 明寫「其餘 6 支刻意不建 stub、只走 Agent 工具」，並說明想要 `/名稱` 就自己補 stub
- Step 4 補建 `.trash/`（範本附錄與收工流程都依賴它，原本沒建）
- Step 5d Playwright 指令補 `--`（`claude mcp add playwright -- npx @playwright/mcp@latest`），與現行 `claude mcp add --help` 對 stdio server 的寫法一致
- Step 7 明寫「先請使用者重開 session 再跑驗證」，避免 T2、T3 誤判成安裝失敗
- Step 8 改列「兩個容易被誤認成故障的預期行為」＋「真正需要找 Terry 的三類事」（Pluto 授權、`bwt-lint.sh`、個人層制度檔範例）

### 個人 CLAUDE.md 範本：補上 longform

- 產稿 Skills 表新增 `longform`（標為 1,500 字以上長稿主線），`deep-analysis`／`tutorial-article` 降為「過渡期退路，明確說『用舊的』才走」。原本範本完全沒有 longform，新同事照範本生成的 CLAUDE.md 等於裝了卻永遠不會觸發
- 「不確定稿件類型時（深度分析 vs 教學文）主動詢問」改為 longform 口徑：長稿不事前選稿種，要問的只有真正影響路由的分界
- stub 名單改為描述現況：只有 `notion-orchestrator` 有帶 `disable-model-invocation: true` 的 stub，其餘 Agent-only 的沒有 stub（原本寫成 7 支都應有，與安裝器的 7 個 stub、SMOKE-TEST T2 的預期三方打架）
- Notion 段新增覆寫規則（本檔的資料庫 URL 優先於 `notion-orchestrator.md` 的預設值）；附錄補 `pic/`、補 longform 產出的檔名代碼沿用規則
- 「12 支以上 Skill 寫死 `~/Claude Project`」更正為 6 個檔案並列出檔名（實查結果）

### 其他

- `SMOKE-TEST.md`：T1 的 frontmatter 清單改為 longform 主線口徑、T2 補「只有 7 支有 stub」說明、T6／T7 去除「請 Terry 分享／加 Connections」的共用庫殘留、開頭與結尾補「先重開 session」與「只有兩類事要找 Terry」
- `ADAPTATION.md`：`mv *.md ~/.claude/agents/` 改為逐檔 `curl -o`（原寫法會誤搬同目錄其他 `.md`）、補 clone 前的既有目錄警語、補「這三支怎麼觸發（Agent 工具，要斜線指令得自建 stub）」、Q3 skill 清單改為 longform 口徑、`notion-orchestrator` 改造建議改走 CLAUDE.md 覆寫而非改 Skill 檔
- `notion-orchestrator.md`：「資料庫資訊」加註「個人 CLAUDE.md 優先、不要改本檔」
- `bwt-style-guide.md`：「正文字數（全域規則）」正式登記 ETF 懶人包 3,500–4,500 字為唯一例外（原本例外只寫在 `etf-explainer.md`，而本節自稱「唯一硬上限就是本條」，形成單點化原則的破口）
- 版本號規則的示例從 `v2.x` 更新為 `v3.x`（README、CHANGELOG）

---

## v3.1.0 — 2026-07-29

**新增 `longform.md`（長文 Skill，⚠️ 實驗中，不是預設路徑）**

源頭：舊架構按「文章類型」選 Skill，但 deep-analysis 與 tutorial-article 的界線在實務上經常模糊，因為兩者的差異不在格式而在「結構是論證型還是步驟型」，而這件事在大綱長出來之前無法確定。事前選當然會模糊。新設計把 Skill 邊界改按「格式契約」劃：格式在素材進來那一刻就定了的稿種（外電即時新聞、ETF 懶人包、社群貼文）維持獨立 Skill；需要先定切角才能動筆的長稿合併為一支，**由大綱決定套哪一套結構**。

### 這支 Skill 的核心：grill 前置

動筆前先把切角逼問到共識，取消「事前選稿種」這個動作。流程是：

1. **讀完全部指定素材**（硬約束，素材沒讀完不得進入提問階段）。素材通常是多視角蒐集的，可能含當事者自述、受害者、第三方評論、調查報導
2. **來源立場盤點**：每份素材是什麼角色、主張什麼、彼此哪裡衝突。衝突點往往是最有新聞價值的地方
3. **判力度**（依素材形態不依主題）：單一來源且方法論已成形 → 中力度（一次列 3 到 5 個判斷點附建議答案，單輪往返，一階段）；多來源或素材本身沒有結論 → 高力度（逐題問、一次一題，兩階段：先 grill 到共識再生成大綱）
4. **兩題必問**：讀者是誰與這件事對其工作或決策的具體影響（影響間接也是有效答案）；核心主張一句話與有沒有反例
5. **反共識自檢**（大綱定案前強制）：主流敘事是什麼、誰從中獲益、最強反方論點是什麼
6. **退出條件**：同一題與使用者實際往返兩輪仍無共識，由模型定案、標記為假設並註明依據，繼續往下。grill 是為了讓稿子更準，不是為了取得授權

設計原則：grill **提供判斷資訊，不做風險控管**。選題與投入規模的決定權在編輯，模型問一次就好、不反覆確認；明文禁止把「會不會被轉發」寫進提問（那是傳播指標不是新聞判斷，當成前提會系統性偏向獵奇題目，擠掉重要但枯燥的題目）。

### 目前的驗證狀態（給接手同事看的）

- **已驗證**：論證型高力度整條路徑（2026-07-29 用一篇三來源的解釋型長稿實跑）。其中反共識自檢最有效，它找出的反向框架直接成為文章核心
- **未驗證**：步驟型結構（原 tutorial 那半，含多步驟 prompt 合成 one-shot master prompt 的邏輯）、中力度單輪往返、混合體處理
- **建議**：可以試用，但**別當主力**。`deep-analysis` 與 `tutorial-article` 仍是預設路徑，要走新的必須明確說「用 longform」或「幫我寫長文」

### 過渡期安排

舊的 `deep-analysis`、`tutorial-article`、`draft-polish` **全部保留**，作為退路。本 Skill 驗證穩定後才會廢除舊兩支（`draft-polish` 另有規劃，將改為「讀感分析」品質關卡並移入查核鏈）。`CLAUDE-template.md` **刻意不加**長文觸發語，因此照範本產生個人 CLAUDE.md 的人，預設流程不變、不會誤觸實驗中的 Skill。

同步更新 `SMOKE-TEST.md` 的檔案數期望值（≥25、frontmatter 14 支）與 `README.md` 的 Skills 一覽。

---

## v3.0.2 — 2026-07-29

**兩處編輯口徑定案（Terry 裁決）**

- `news-daily.md`：三大重點字數「60 字以內」改為 **50 字以內**，與 `deep-analysis.md:202` 出稿前自查、`notion-orchestrator.md:37` 的 Notion 欄位定義統一。原先三處各寫一個數字，同一篇稿在產稿與寫回階段會被要求不同長度
- `etf-explainer.md`：3,500-4,500 字作為全域 3,500 字上限的明定例外，**確認保留**（SEO 流量文），註記補上裁決日期
- 摘要下限「50 至 75 全形字元」已於 v3.0.0 統一，本次確認無誤

---

## v3.0.1 — 2026-07-29

**Notion 產稿資料庫改各自建置，不共用稿單與 token**

源頭：Terry 裁決——實務上不需要共用稿單，新同事自建一份即可。稿單是個人工作佇列，共用會混淆負責人歸屬與狀態；各自持有 integration token 也避免金鑰在人與人之間傳遞。連帶消除交接包原本的兩個阻塞（NOTION_TOKEN 傳遞管道、「負責人」下拉需請人代加選項）。

- `bn-claude-code-init.md`：Step 1 問答第 4 題與 Step 5b 改為自建流程（[notion.so/my-integrations](https://www.notion.so/my-integrations) 建 internal integration、自建資料庫與必要欄位、資料庫頁面 Connections 授權這一步漏做會回 404）；Step 8 移除「向 Terry 索取資料庫權限與負責人選項」
- `CLAUDE-template.md`：核心資料庫改為「你自建的產稿資料庫」；「負責人」選項改為自行設定；補述 `~/.claude/skills/` stub 帶 `disable-model-invocation: true` 的機制（**stub 存在 ≠ 可用 Skill 工具呼叫**，避免安裝驗證時誤判為規範矛盾）
- `SMOKE-TEST.md`：T7 前置條件改為「確認 `notion-orchestrator.md` 內的資料庫 URL 已換成自己的」
- `README.md`：`NOTION_TOKEN` 說明改為自建 integration secret，移除「公司內部金鑰可全體同仁共用」
- `ADAPTATION.md`：「CLAUDE.md 非公開、找 Terry 索取範例」過期指引改指 `CLAUDE-template.md`

**補 v3.0.0 漏改**：`CLAUDE-template.md` 的 deep-analysis「最多 2,500 字」、draft-polish「2000-2500 字」改為「目標」（全域 3,500 字唯一硬上限的裁決，該檔原漏同步）。

> **個人層同步提醒（不在本 repo）**：`~/.local/bin/bwt-lint.sh`（article-checker Step 0 呼叫的機械檢查腳本）的規則來源是 `bwt-style-guide.md` 與 `article-checker.md`，v3.0.0 改動後已同步四處：「值得注意的是」從禁詞改為每篇上限 1 次的計數檢查、白話轉場詞從「總次數上限 2」改為「同一轉場詞不重複」、破折號字元集補上 U+FF0D、字數表改「目標」並新增全域 3,500 字硬上限檢查（etf 除外）。**自行維護此腳本的人，改規範後記得同步腳本。**

---

## v3.0.0 — 2026-07-28

**全系統總體檢：活矛盾修復、約束單點化、交接包改造**

源頭：Terry 指出兩件事——(1) 舊 Skills 隱含「高效流水線」目的已過時，當前重心是獨創性內容、與 Codex 配合的多輪查核、配件發想；(2) 既有 Skills 以舊式 prompt（逐步腳本＋防呆條款堆疊）寫成，對 Claude 5 家族模型已不合用。經 5 份平行 audit 逐行盤點（證據存 Terry 個人 vault `projects/skills-renewal-2026h2/`），共 16 檔規範改寫，淨減 576 行。**硬約束（事實查核鐵律、design tokens、Notion 參數小抄、兩岸用語、SEO 骨架）全部保留**，砍除對象為同一約束多處複寫、對當前模型多餘的手把手腳本、歷史補丁堆疊。

### 一、活矛盾修復（規範互相打架，照哪份做都可能錯）

- **「值得注意的是」口徑**：`bwt-style-guide` 原列禁用詞、個人風格指紋檔卻列為建議用詞 → 統一為**每篇最多 1 次**。`etf-explainer` 同步（原列為「空洞開場白」禁用）；`article-checker` 該詞保留於旁支訊息偵測清單，但加註「此處是偵測訊號、非禁詞」
- **轉場詞規則**：原「≤2 次」vs「至少 2-3 次」數字互斥 → 改為**同一轉場詞不重複使用**，不設總次數
- **iframe 高度策略**：`bwt-iframe-visual-component` 決策表寫「postMessage 動態同步」、同檔高度策略章寫「禁 postMessage 純寫死」、`visual-asset` 又稱「配件端已內建發送機制」並引用不存在的段落 → 統一為**純寫死＋100px buffer，禁 postMessage**（2026-05-18 實測教訓保留為理由：CMS Alpine widget 有 listener 但接收後高度異常、點擊後 footer 出現空白）
- **RWD 寫法**：`visual-asset` 強制 `@media` vs iframe 子規範強制 `@container` 禁 `@media` → 統一為 **`@container` 主軌、`@media` 僅作 fallback**（inline 表格元件在文章欄約 600px 寬時，`@media` 會誤判為大螢幕而排錯版）
- **量測參數漂移**：buffer 91/100px、等待 1200/1500ms 兩處不一致 → 統一 1280×800 → `fonts.ready` → 等 1500ms → 量 `scrollHeight` → +100px
- **logo 尺寸**：規範寫 24px，實際部署配件無一使用 → 依部署實務定為源圖 `sz=64`、顯示 32px、密集清單 20-22px
- **inbox 去留**：`notion-orchestrator` 要求「請使用者一次確認」，與專案端 2026-07-09 零決策原則矛盾 → 整段改為一行指針
- **破折號**：三處口徑（2,000 字 1 個／完全禁用／節制）→ 統一**全面禁用**，自查 pattern 合一為 `grep -cE "[—－]"`

### 二、約束單點化（載體合併，約束本身不變）

- **`bwt-style-guide` 升為跨 Skill 共用段落的唯一權威**（21,484 → 約 28,000 bytes）：吸收來源讀取優先序（含付費牆政策、PDF 處理、Playwright 禁令）、AI 味句式總表與替代表、文末資料來源＋署名格式、Notion 寫回格式、首段獨立閱讀原則、他國金額取捨。各 Skill 原處改為一行指針
- **全稿種正文上限統一 3,500 字**，預估超過須在大綱規劃階段警示；`etf-explainer` 3,500-4,500 為明定例外（SEO 流量文）；roundup 2,000 字上限併入 style-guide
- **標題字數以 `headline-generator` 為唯一權威**（25-35 字），`tutorial-article` 原「≤30 字」改為指向
- **官方公告六項 pre-check 三份併一份**（原存在於 news-daily 兩處＋style-guide 一處）
- `etf-explainer`：7 大硬約束原散見角色原則／章節內文／checklist 各 2-3 次 → 統一收進「出稿驗收 Checklist」唯一載體；檔內兩份重複 checklist 合併；硬編的槓桿 ETF 持股數字改為「產稿當下查官方值」
- 視覺六檔職責單一化：rubric 歸 `bwt-visual-checklist`、workflow 歸 `visual-asset`、design tokens 目錄唯一歸 `bwt-design-standard`（iframe 子規範內的色票字體「節錄」刪除改引用）
- `bwt-html-table-component`：逐字重述自家範本的元素 inline style 清單改為變體差異表

### 三、article-checker 三軸改造

- **規則 A-F、矛盾協議、Step 0 機械檢查原樣保留**（全部 load-bearing），實戰案例壓縮至每規則 ≤2
- Step 4 重寫為**論點挑戰軸**：不只挑錯字與事實，要挑戰論證——主張是否有證據支撐、反例是否被忽略、因果是否過度推論；每項輸出附嚴重度＋建議處理
- **新增 Codex 協作協議**（原版 0 字提及 Codex，實際品管流程僅存在於個人層設定）：意見三分類處理（事實類衝突先 `web_search` 再決定，不盲從也不硬頂）、每輪修改集中執行、文末資料來源兩行改完必回查（多輪修改最易跑掉處）、逐輪強度遞減定義（第一輪全查 → 第二輪只查上輪改動處 → 第三輪起僅格式與抽查）
- 砍除：貨幣搜尋對照表（改一行「匯率一律即時 `web_search`」）、emoji 報告模板、重複注意事項

### 四、其他修正

- `deep-analysis`：「四個具體動作」實列五項的計數 bug
- `visual-asset`：2 處死引用（不存在的段落錨點、不存在的範例檔）
- `bwt-iframe-visual-component`：logo 登錄表補齊落後的 3 檔（spacex／starlink／xai），並改為以 `ls _assets/logos/` 實際目錄為準
- `visual-brief`：補上與 `bwt-visual-checklist`／`bwt-design-standard` 的連結（原為引用鏈孤島）
- 各 Skill 對個人風格指紋檔的引用改為條件句（「若存在則讀」），使用共用 repo 但無此個人檔的同事不受影響

### 五、交接包（新同事一次安裝）

- `bn-claude-code-init.md` 升級為**互動式安裝主體**（由 Claude Code 讀取後引導安裝，跨 CLI／VSCode）
- 新增 `CLAUDE-template.md`：全域 CLAUDE.md 可改寫範本，含 Skill 工具 vs Agent 工具兩張名單對照、Notion MCP 參數小抄、產稿 Skill 觸發語對照、付費牆政策
- 新增 `SMOKE-TEST.md`：功能性安裝驗證（每項含指令→預期結果→失敗怎麼修），取代原本僅有的靜態檔案檢查
- 補 `bnext-service` MCP 說明（原全 repo 0 提及）
- 配件托管改為**各自託管**：新同事在自己 GitHub 帳號建 bnext-visuals 並開 Pages（iframe 網址永久嵌在已發布文章，不宜綁他人帳號）
- `lint-vault.md`：個人專屬路徑參數化

---

## v2.9.9 — 2026-07-15

**article-checker：新增 Step 0 機械檢查（bwt-lint 腳本掛鉤，環境有才跑）**

源頭：Claude Code 官方〈Getting started with loops〉一文「確定性工作用腳本、不用模型推理」原則。查核流程中一批純字串比對項目（相對時間詞、禁止詞彙、破折號、文末署名格式）過去靠模型逐字掃描，有機率漏抓且每輪修訂重複燒 token；2026-06 相對時間詞單月被外部糾正 6 次即此類漏失。

- `article-checker.md`：前置作業新增 **Step 0 機械檢查**——若環境有 `~/.local/bin/bwt-lint.sh` 則先跑，取得含行號的確定性候選清單（相對時間詞／兩岸用語／禁止詞彙／文學成語／破折號／SI 前綴／新台幣寫法／斜線日期／半形標點／轉場詞密度／文末兩行格式／字數區間）；模型只補語境判斷、不重掃全文；輸出修訂後全文前需對修訂稿重跑一次確認無新增命中。腳本不存在時照原流程執行，同事環境不受影響（腳本本體屬個人層 `~/.local/bin`，不在本 repo）

---

## v2.9.8 — 2026-07-07

**etf-explainer：輸出結構調整——移除「三大重點」區塊，改由新聞時事切入 intro 打頭**

源頭：先泰指示 ETF 懶人包不再於正文開頭寫「三大重點／三大亮點」，改以扣合當前新聞時事的 intro 直接切入，接目錄與正文（呼應 00685L 以「1拆24 恢復交易」時事鉤子打頭）。

- `etf-explainer.md`：輸出順序改為「標題 → 新聞時事切入 intro → 目錄 → 九個 H2 → Q&A → 資料來源」，移除「三大重點」區塊；摘要（≤75 全形字元）改為只填 Notion「摘要」屬性欄作 SEO meta、不放正文開頭；「### 2. 開頭」改寫為「新聞時事切入 intro」，後續小節編號、出稿前自查清單、寫回 Notion 順序同步更新

---

## v2.9.7 — 2026-07-07

**etf-explainer：新增「槓桿型／反向型 ETF 成分股特殊處理」規則（結構因標的與操作而異，禁套單一假設）**

源頭：先泰的 00685L 群益臺灣加權正2 懶人包。初稿依基金月報「主要投資標的：台指期 199.53%」單一欄位，誤判為「純期貨、無成分股」；先泰指出應有成分股清單。查證後確認 00685L 官方揭露 43～78 檔持股（台指期約 200% ＋中央公債／公司債，無權值股現股），且比對元大 00631L（現股台積電約 38.7% ＋臺股期貨約 160%）發現同為正2、結構卻因追蹤標的不同而異（臺灣50 用現股複製、臺灣加權指數近千檔改用純期貨）。

- `etf-explainer.md`：角色定位原則由 6 條增為 7 條（新增原則 7 指向 H2 三細則）；Step 3 › H2 三成分股段新增「槓桿型／反向型 ETF 的成分股特殊處理」區塊——兩種型態判別（(a) 現股＋期貨型如 00631L／(b) 純衍生品型如 00685L）＋純衍生品型的三層呈現原則（只列可驗證期貨部位、債券文字帶過、間接曝險帶到指數權值股），附兩檔真實案例與 2026/7 查證日期

---

## v2.9.6 — 2026-07-07

**依 75 個 session 的 transcript 實證診斷，補三個高頻失準點的結構化防線**

源頭：掃描 2026-06-07~07-07 全部 75 個 session（含 33 筆使用者真實糾正、47 次可預測工具錯誤）後的診斷結論。最大宗糾正（14 筆）是「證據級別與語氣錯配」——未確定事項寫成已確定（美超微 IPO 條件）與已確定事實被過度 hedge（Ternus 接班人）雙向震盪；次大宗（6 筆）是相對時間詞見報即失效；Notion 參數同形錯誤一週內跨 session 重犯 26 次。

- `article-checker.md`：強制驗證規則新增**規則 E（確定性口徑五級對照）**——L1 已完成～L5 推論的證據級別 → 語氣對照表，雙向查核（寫太滿與 hedge 過頭都列事實錯誤），法律指控需一手文件；**規則 F（相對時間詞掃描）**——「明日／目前／即將」等機械式掃描改絕對日期、兩階段事件動詞對應、事件歸因時間錨定
- `notion-orchestrator.md`：Step 1 MCP 工具區新增**參數小抄**——notion-fetch 參數名只有 `id`（值可放 URL）、replace_content 用 `new_str`、狀態欄合法值「完成」、date 展開格式、archived 頁面檢查
- `bwt-visual-checklist.md`：修正「禁用破折號」節殘留的舊版內文規範描述（≤1 次／2,000 字已於 7/2 廢止，統一為全文 0 次），消除與 bwt-style-guide 的規範矛盾

---

## v2.9.5 — 2026-07-02（補記錄）

**全 Skills model／effort 分層 + 事實查核鐵律章節（7/2 盤整 session 完成、未即時記錄，本次補上）**

源頭：Terry 反映頻繁撞到 5 小時 token 上限，盤整發現全系統一律以最高規格模型執行所有任務（含機械式掃描），且 15 條查核類 memory 靠 recall 召回不可靠、反覆再犯。

- 13 個 Skill frontmatter 新增 `model:` + `effort:` 分層：深度層保 Opus + high（deep-analysis、draft-polish、etf-explainer、notion-orchestrator）；輕量層降 Sonnet（news-daily、social-post、headline-generator、visual-brief=medium、lint-vault=low、retrospective=medium、tutorial-article、visual-asset、article-checker=high）
- `bwt-style-guide.md`：新增「事實查核鐵律」章節——15 條查核類 memory（來源可回溯、動詞精準、欄位標題逐字核對、累積占比、SEC 文件分類、官方公告六模式等）濃縮為 18 條要點，血淚案例錨點全保留
- `lint-vault.md`：排除清單補 `.claude-refs/` 與 `_archived-*` 檔案

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
