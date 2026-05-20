---
name: visual-asset
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

## 設計優先序（最高原則）

設計判斷照以下優先序：

1. **服務讀者閱讀體驗**（辨識度、戲劇張力、reading flow）— 最高原則
2. **BN Design System**（[`bwt-design-standard.md`](bwt-design-standard.md)）— default 起點
3. **個案戲劇化元素** — 若服務讀者可從寬納入

Design System 是 default，不是鐵則。當戲劇化／辨識度元素確實服務讀者（譬如法庭 Q&A 律師雙色用 BN brand tokens 做語意對映、簡訊 iMessage 直覺氣泡、證物編號 footer 增加真實感），可以靈活納入——**不要為了忠於規範犧牲閱讀體驗**。

### 判斷光譜

| 設計選擇 | 偏向 default | 偏向從寬 |
|---|---|---|
| 律師雙色用 BN tokens 語意對映 | — | ✅ 服務辨識度 |
| 簡訊 iMessage 風氣泡 | — | ✅ 服務直覺 |
| 證物編號 footer | — | ✅ 服務戲劇真實感 |
| 章節 H2 用 BN Orange 純文字 | ✅ 對齊 BN | — |
| 加 box-shadow / drop-shadow | ✅ 絕對禁止 | — |
| 加 gradient 漸層 | ✅ 絕對禁止 | — |
| 純白底色 | ✅ 絕對禁止 | — |

**節制原則仍適用**：BN Orange「節制」不是「不用」，speaker label 級別的小範圍染色不算違反「大範圍鋪色」禁令。

詳細哲學紀錄於 memory：`feedback-reader-experience-trumps-style`。

---

## 動手前自檢（避免色票漂移）

⚠️ **絕不憑外部觀察自建色票系統**——禁止用 Playwright 抓 bnext.com.tw computed styles、其他媒體參考、品牌印象等來源建立「自己的」design tokens。所有 design tokens 一律以 [`bwt-design-standard.md`](bwt-design-standard.md) 為**唯一 source of truth**。

實測值（官網 computed styles）反映「臨時實作」，不是品牌目標規範。Design System bundle（`~/Claude Project/projects/bn-design-system/`）才是 Terry 授權的品牌正規範。兩者不一致時，**以 Design System 為準**。

歷史教訓：2026-05-19 曾用 Playwright 實測 BN 官網建立 `#FF5500` 純白底的衝突 spec，差點覆寫既有 Design System（`#FF6B1A` + warm ivory）。發現後拍板路徑 A：以 Design System 為準。

## 必讀規範（啟動前依序讀取）

1. `~/.claude/agents/bwt-design-standard.md` — 共通美術規範（色彩、字體、字級、設計原則）
2. `~/.claude/agents/bwt-iframe-visual-component.md` — iframe 配件子規範（HTML 結構、雙軌設計、相容性）
3. `~/.claude/agents/bwt-style-guide.md` — 寫作風格（兩岸用語、人名、禁用詞）

⚠️ **配色強制**：所有配件必須用 BN Orange `#FF6B1A` + Paper `#FAF7F1` warm ivory + BN Black `#1A1A1A`。**禁止自創品牌色**（如 `#c0392b` 深紅、純白 `#fff`、深藍非 BN Navy 等）。

---

## 工作流

### Step 0：判斷產品類型（最重要，避免整套白工）

動手前先分辨兩種產品：

| 訊號 | iframe 嵌入配件 | 16:9 主視覺圖卡 |
|---|---|---|
| 使用者怎麼說 | 「做配件」「做個 tracker」「嵌入內文」「互動圖表」 | 「做主視覺」「做 cover」「文章封面」「社群分享圖」「Notion 封面」「16:9 圖卡」 |
| 輸出 | HTML + iframe 嵌入碼，托管 GitHub Pages | 1920×1080 PNG |
| 互動 | 雙軌（桌機 + 手機）有 hover、預覽工具列 | 無互動，純靜態圖 |
| 字體尺寸 | 對齊內文閱讀（多偏小） | 視覺衝擊（要大） |

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

依 [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md) 的 HTML 結構模板撰寫。**強制雙軌**：包含 `@media (max-width: 640px)` 區塊。

存到 `draft/YYYYMMDD-news-主題視覺配件.html`，讓 Terry 預覽。

**Logo 加入策略**：
- 若資料涉及具名公司，預設用 Google s2 Favicon API：`<img src="https://www.google.com/s2/favicons?sz=64&domain=<domain>.com" width="24" height="24" onerror="this.style.visibility='hidden'">`
- 失敗自動隱藏（onerror），避免破圖
- ⚠️ **不要用 Clearbit**（2024 年被 HubSpot 收購後 API 已停用）
- 未來如需更高解析度 logo，批次下載到 `~/Claude Project/projects/bnext-visuals/_assets/logos/<company-slug>.png`，把 src 換成 `../../_assets/logos/<company>.png`

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
        width="100%" height="<桌機實際高度>"
        style="border:0; max-width:880px; display:block; margin:24px auto;"
        loading="lazy"
        title="<配件標題>"></iframe>
```

| 配件類型 | 建議 height（桌機優先） |
|---|---|
| 短型統計卡（單一圖表） | 600–800 |
| 中型（4 卡陣列、橫排比較） | 1,200–1,400（Anthropic playbook 桌機實測 1,109） |
| 長型 tracker（編年清單） | 3,800–4,000（裁員 tracker 桌機實測 3,815） |

⚠️ **每個新配件產出後必須跑 Playwright 量精準桌機 height，不靠估算**——歷史上估算誤差曾達 1,000+ px。流程：
```
resize 1280x800 → navigate 配件 URL → await document.fonts.ready → 等 1500ms → 量 document.documentElement.scrollHeight → +91px buffer = iframe height
```

**取捨**：桌機讀者完美貼合；手機讀者會在 iframe 內部 scroll（手指 swipe 在 iframe 內 scroll、到邊界自動切回頁面 scroll，現代瀏覽器處理 OK）。

**真正解（長期）**：請 IT 在 Alpine.js iframe widget 加 postMessage 監聽，所有 iframe 自動 auto-resize。配件端已內建發送機制，IT 加接收端後現存配件無需更新嵌入碼即可受惠。詳見 [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md) 的「iframe 高度自動同步」段。

需要強制 CDN 刷新時，URL 後加 `?v=YYYYMMDD`。

---

## 注意事項

- **Public Repo**：所有上傳到 `bnext-visuals` 的檔案公開可見。**禁止放未發布稿件、API key、客戶資料、財報內部數據**。每次上傳前自查。
- **GitHub Pages + Jekyll 陷阱**：repo root 必須有 `.nojekyll` 空檔，否則 Jekyll 會自動忽略 `_` 開頭目錄（如 `_assets/`），導致 self-hosted logo / CSS 全部 404。新建 repo 時第一個 commit 就要把 `.nojekyll` 加進去。
- **CDN 快取**：改完 HTML 後 1–5 分鐘才刷新。
- **Logo 來源穩定性**：Google s2 Favicon API 為 Google 維護的公開服務，穩定可用。若特定公司 favicon 設計過於低解析度或不美觀，可手動下載高品質版本到 `_assets/logos/` 自托管。
- **共用 CSS**：累積到 3–4 個配件後，把 BN 配色變數、字體 stack 抽到 `_assets/base.css`，新配件 `<link>` import。

---

## 16:9 主視覺圖卡子規範

當 Step 0 判斷為主視覺圖卡（非 iframe 配件）時，套用以下規格：

### 預設規格
- **尺寸**：1920×1080（16:9）PNG
- **輸出**：HTML 設計 → Chrome headless 截圖
  ```bash
  "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
    --headless=new --disable-gpu --hide-scrollbars \
    --force-device-scale-factor=1 --window-size=1920,1080 \
    --virtual-time-budget=8000 \
    --screenshot=output.png file:///path/to/html
  ```
- **存放**：`~/Claude Project/draft/YYYYMMDD-<類型>-<主題>-keyvisual.html` + `.png` + 圖檔素材同目錄
- **配色**：BN tokens（同 iframe 配件）
- **禁止破折號**：見 [`memory/feedback_no_dash_in_visuals.md`]

### 字體預設（給 1920×1080 viewport，**起步要大**）

| 元素 | 預設 | 不要低於 |
|------|------|---------|
| h1 主標 | 60–72px | 56px |
| 副標 / deck | 30–36px | 28px |
| Kicker | 22–26px | 20px |
| 卡片產品名（item-name） | 28–32px | 26px |
| 卡片描述（item-desc） | 22–26px | 20px |
| 卡片序號（item-num） | 22–26px | 20px |
| Footer | 18–22px | 16px |

**經驗**（2026-05-20）：1920×1080 視覺空間很大，字體不要保守起步。不確定時往上選；下端空白多 = 字還能放大。

### Layout 預算（1920×1080）

| 區塊 | 高度 |
|------|------|
| Banner（素材圖 cover） | 300–340px |
| h1 + 副標 + meta | 180–240px |
| 卡片區（5 卡橫排） | 350–400px |
| Footer | 70–90px |
| Padding 上下 | 80–90px |

### Banner 素材圖置中 SOP

不要憑視覺猜 `object-position`，用 PIL 量原圖 logo 垂直位置：

```python
from PIL import Image
img = Image.open('banner.webp')
gray = img.convert('L')
# 掃描 dark rows（logo 通常為深色文字）
row_dark = [(y, sum(1 for x in range(img.width) if gray.getpixel((x,y))<100))
            for y in range(img.height)]
row_dark.sort(key=lambda t: -t[1])
logo_y = sorted([t[0] for t in row_dark[:30]])[15]
print(f"Logo vertical: {logo_y/img.height:.1%}")
```

logo 在原圖 50% → `object-position: center center`
logo 在原圖 60% → `object-position: center 60%`

### 渲染後 spot-check checklist

每次截圖後立刻檢查：
1. Banner logo 真正上下／左右置中（看圖確認，不是「我以為」）
2. 副標單行收完，無「孤行」尾字
3. 5 個卡片小標單行不折行、不孤詞
4. 卡片內 desc 起始位置切齊（用 `item-name { min-height }` 預留兩行高度）
5. 圖卡下端是否還有大塊空白 → 有就放大字體或加內容，不要浪費

### HTML 結構模板

```html
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<title>主標 ｜ 數位時代</title>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&family=Inter:wght@400;500;600;700;900&display=swap">
<style>
  /* BN tokens：對齊 bwt-design-standard.md */
  /* 注意：html, body 固定 1920×1080，overflow hidden */
  html, body { width: 1920px; height: 1080px; overflow: hidden; }
</style>
</head>
<body>
<div class="key-visual">
  <div class="banner">...</div>
  <div class="content">
    <header>...</header>
    <div class="cards-row">5 張卡片</div>
    <footer>...</footer>
  </div>
</div>
</body>
</html>
```

完整實作範例：`~/Claude Project/draft/20260520-news-google-io-keyvisual.html`

---

## 版本紀錄

| 日期 | 變更 |
|---|---|
| 2026-05-18 | 初版建立。從 `bnext-visuals/2026/05-layoffs-tracker` 案例抽取工作流。 |
| 2026-05-20 | 新增「16:9 主視覺圖卡」產品類型與 Step 0 類型判斷。預設字體尺寸表、Banner logo 置中 SOP、spot-check checklist。從 Google I/O 懶人包案例抽取。 |
