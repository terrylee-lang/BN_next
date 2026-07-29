---
name: visual-asset
model: sonnet
effort: high
description: |
  《數位時代》互動視覺配件 Skill。將文章資料、清單、CSV 轉化為符合 BN 視覺規範的 HTML 配件，桌機 + 手機雙軌設計，自動托管至 GitHub Pages（terrylee-lang/bnext-visuals），產出可直接貼到後台的 iframe 嵌入碼。

  請在以下情境下主動使用此 Skill：
  - 使用者說「幫我做配件」、「做個視覺化」、「做個 tracker」、「做張圖表」
  - 使用者提供清單或數據（裁員、新創 funding、營收結構、產品比較），說「做成視覺化」、「做張圖」
  - 使用者貼文章後說「這篇要配什麼互動配件」、「這數據可以視覺化嗎」
  - 任何「文章 + 數據 → iframe 嵌入式配件」的情境

  ⚠️ 與其他 Skill 的區別：
  - 主視覺發想（單張封面圖 / 插畫）→ 使用 visual-brief
  - 文章內表格元件 → 使用 bwt-html-table-component（目前僅 etf-explainer 套用）
  - 本 Skill：兩種產品類型
    - **iframe 嵌入式互動配件**（時間軸、氣泡圖、橫條排行、地圖等）：嵌入文章內、桌機 + 手機雙軌、托管在 bnext-visuals
    - **16:9 主視覺圖卡**（1920×1080 PNG）：文章封面／社群分享／Notion 封面用，單張靜態圖
---

# Visual Asset — 互動視覺配件 Skill

把文章的關鍵資料、清單、時序事件轉成 iframe 嵌入式 HTML 配件，托管在 GitHub Pages，讀者在文章內互動瀏覽。

---

## 設計判斷與動手前自檢

rubric 全數住 [`bwt-visual-checklist.md`](bwt-visual-checklist.md)（唯一居所），啟動時必讀：

- **設計優先序與判斷光譜**（服務讀者 > Design System default > 戲劇化從寬）→ checklist §一
- **掃既有規範、禁止憑外部觀察自建色票**（2026-05-19 `#FF5500` 教訓）→ checklist §二

---

## 必讀規範（啟動前依序讀取）

1. `~/.claude/agents/bwt-design-standard.md` — 共通美術規範（色彩、字體、字級、設計原則）
2. `~/.claude/agents/bwt-iframe-visual-component.md` — iframe 配件子規範（HTML 結構、雙軌設計、相容性）
3. `~/.claude/agents/bwt-visual-checklist.md` — 視覺配件工作流與交付 Checklist（產品類型判斷、16:9 SOP、iframe spot-check、禁用破折號、本地優先 iterate）
4. `~/.claude/agents/bwt-style-guide.md` — 寫作風格（兩岸用語、人名、禁用詞）

⚠️ **配色強制**：所有配件必須用 BN Orange `#FF6B1A` + Paper `#FAF7F1` warm ivory + BN Black `#1A1A1A`。**禁止自創品牌色**（如 `#c0392b` 深紅、純白 `#fff`、深藍非 BN Navy 等）。

---

## 工作流

### Step 0：判斷產品類型（最重要，避免整套白工）

動手前先分辨兩種產品：iframe 嵌入配件 vs 16:9 主視覺圖卡。**判斷訊號表見 [`bwt-visual-checklist.md`](bwt-visual-checklist.md) §二**（唯一居所）。

不確定時主動問：「這要嵌入內文（iframe 互動配件）還是當文章封面／社群分享（1920×1080 PNG 圖卡）？」

### Step 1：確認素材與視覺目標

讀完素材後，與 Terry 確認三件事：

1. **這次要視覺化什麼資料**？（清單、時序、比較、分布、分群）
2. **目標讀者要從這張圖獲得什麼**？（看排名、看趨勢、看分群、看單筆細節）
3. **配件搭配哪篇文章**？（決定檔名年月，套用文章主標的語意）
4. **「N 大發布／重點」的 N 必須跟稿件章節對齊**：逐條核對名稱、順序、用詞，不要自編。多塞一個（如把配套平台當成獨立發布）= 標題與內文不符

### Step 2：選擇視覺類型（依資料形態）

| 資料形態 | 桌機視覺 | 手機替代 |
|---|---|---|
| 時序事件 + 規模 | 時間軸氣泡圖（SVG） | Top N 規模排行榜（橫條） |
| 純比較 / 排名 | 橫條圖 | 橫條圖（精簡 Top 數量） |
| 地理分布 | 簡化地圖（SVG）+ 標籤 | 縣市卡片列表 |
| 多維分類 | 卡片陣列 + 篩選器 | 分群卡片直向排列 |
| 流程 / 步驟 | 階段卡片橫向排列 | 階段卡片直向排列 |
| 對照 / 表格資料 | 直接呼叫 bwt-html-table-component 規範 | 同左 |

### Step 3：產出 HTML 到 `draft/`

依 [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md) 的 HTML 結構模板撰寫。**強制雙軌**：以 `@container (max-width: 640px)` 為主軌（`@media` 僅作漸進增強 fallback，規則見該檔「雙軌設計強制規則」）。

存到 `draft/YYYYMMDD-news-主題視覺配件.html`，讓 Terry 預覽。

**Logo 加入策略**：Google s2 Favicon API、自托管判斷與 SOP、顯示尺寸，一律依 [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md)「Logo 加入規範」（唯一居所，含 Clearbit 禁令）。

### Step 4：上線 Repo（Terry 確認 OK 後）

執行以下檔案動作：

1. 複製 HTML 到 `~/Claude Project/projects/bnext-visuals/<年份>/<MM-主題-kebab-case>.html`
2. 編輯 `~/Claude Project/projects/bnext-visuals/index.html`：在 `.grid` 區塊新增 `.card` 區塊
3. 編輯 `~/Claude Project/projects/bnext-visuals/README.md`：在配件清單表格加新行

產出三段給 Terry：

- **新增 / 修改的檔案清單**（路徑明細）
- **GitHub 上傳 SOP**（網頁拖檔 or git push 指令）
- **iframe 嵌入碼**（含 src、height、title 已填好）+ **預覽 URL**（src 後加 `?preview=1`，可現場切換桌機／手機 layout 驗收）

### Step 5：上線後驗收提醒

- 等 1–5 分鐘 GitHub Pages CDN 更新
- 桌機開 `https://terrylee-lang.github.io/bnext-visuals/` 確認新卡片出現
- Chrome DevTools 切手機尺寸（iPhone 14 / Pixel 7）驗一輪
- 後台貼 iframe 預覽文章

---

## 檔案命名規範

`<年份>/<MM-主題-kebab-case>.html`

- 英文 kebab-case，避免中文 URL 編碼問題
- 範例：`2026/05-layoffs-tracker.html`、`2026/06-openai-revenue-breakdown.html`、`2026/07-tsmc-fab-map.html`

---

## iframe 嵌入碼模板

⚠️ **《數位時代》後台已確認會 strip 掉 `<script>` 與 iframe `id`**（2026-05-18 實測，Alpine.js iframe widget 自動過濾）。所以**預設給「桌機優先」純 iframe 嵌入碼**，不附 script。

**預設嵌入碼（桌機優先）**

```html
<iframe src="https://terrylee-lang.github.io/bnext-visuals/<年份>/<檔名>.html"
        width="100%" height="<Playwright 實測桌機高度 + 100px buffer>"
        style="border:0; max-width:880px; display:block; margin:24px auto;"
        loading="lazy"
        title="<配件標題>"></iframe>
```

| 配件類型 | 建議 height（桌機優先） |
|---|---|
| 短型統計卡（單一圖表） | 600–800 |
| 中型（4 卡陣列、橫排比較） | 1,200–1,400（Anthropic playbook 桌機實測 1,109） |
| 長型 tracker（編年清單） | 3,800–4,000（裁員 tracker 桌機實測 3,815） |

⚠️ **每個新配件產出後必須跑 Playwright 量精準桌機 height，不靠估算**——歷史上估算誤差曾達 1,000+ px。量測流程與 buffer 參數以 [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md)「iframe 高度策略」為準（本檔不自帶參數）。

**取捨**：桌機讀者完美貼合；手機讀者會在 iframe 內部 scroll（手指 swipe 在 iframe 內 scroll、到邊界自動切回頁面 scroll，現代瀏覽器處理 OK）。

**高度策略＝純寫死，全面禁止 postMessage**（2026-05-18 CMS listener 教訓，見同上章節）。若未來 IT 修復 CMS listener 再重新評估動態同步。

需要強制 CDN 刷新時，URL 後加 `?v=YYYYMMDD`。

---

## 注意事項

- **Public Repo**：所有上傳到 `bnext-visuals` 的檔案公開可見。**禁止放未發布稿件、API key、客戶資料、財報內部數據**。每次上傳前自查。
- **GitHub Pages + Jekyll 陷阱**：repo root 必須有 `.nojekyll` 空檔，否則 Jekyll 會自動忽略 `_` 開頭目錄（如 `_assets/`），導致 self-hosted logo / CSS 全部 404。新建 repo 時第一個 commit 就要把 `.nojekyll` 加進去。
- **CDN 快取**：改完 HTML 後 1–5 分鐘才刷新。
- **Logo 與共用 CSS**：logo 來源與自托管、base.css 抽出門檻，見 [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md)（唯一居所）。

---

## 16:9 主視覺圖卡（工作流）

當 Step 0 判斷為主視覺圖卡（非 iframe 配件）時：

- **SOP 全量（規格、字體預設表、layout 預算、PIL 置中量測、Chrome headless 渲染、spot-check）唯一居所在 [`bwt-visual-checklist.md`](bwt-visual-checklist.md) §三**，參數以該檔為準
- **存放**（本檔負責的工作流部分）：`~/Claude Project/draft/YYYYMMDD-<類型>-<主題>-keyvisual.html` + `.png` + 圖檔素材同目錄
- 交付：渲染後的 PNG 成品可直接附圖（不同於 iframe 配件的「給 URL 不給截圖」原則）

---

## 版本紀錄

| 日期 | 變更 |
|---|---|
| 2026-05-18 | 初版建立。從 `bnext-visuals/2026/05-layoffs-tracker` 案例抽取工作流。 |
| 2026-05-20 | 新增「16:9 主視覺圖卡」產品類型與 Step 0 類型判斷。預設字體尺寸表、Banner logo 置中 SOP、spot-check checklist。從 Google I/O 懶人包案例抽取。 |
| 2026-07-28 | 瘦身為純工作流：rubric（設計優先序、產品類型表、色票教訓）與 16:9 SOP 全量移居 `bwt-visual-checklist.md`；修 @media→@container 主軌；刪 postMessage「配件端已內建發送機制」過期描述與死引用（高度＝純寫死＋100px buffer）；logo 與量測參數指向 `bwt-iframe-visual-component.md` 不再自帶。 |
