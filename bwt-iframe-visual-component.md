# 子規範｜iframe 互動視覺配件

此檔為《數位時代》[美術製作標準總綱](bwt-design-standard.md) 的**子規範**，規範 iframe 嵌入式互動視覺配件的具體實作（HTML 結構、雙軌設計、嵌入相容性）。

**Design tokens（色彩、字體、間距、圓角、設計原則）一律以 [`bwt-design-standard.md`](bwt-design-standard.md) 為準**，本檔不重複定義，僅補充 iframe 配件特有的 layout 與細節規則。

---

## 適用範圍

**目前套用 `visual-asset` Skill 產出的所有 iframe 配件**。未來若 deep-analysis、tutorial-article 等 Skill 需要產出 iframe 嵌入式配件，亦遵循本規範。

不適用情境：
- 文章內表格元件 → 用 [`bwt-html-table-component.md`](bwt-html-table-component.md)
- 文章內 inline 圖示／徽章 → 用 inline SVG 或 emoji，不需 iframe
- 主視覺發想（封面圖）→ 用 `visual-brief` Skill

---

## 關鍵設計決策

| 決策 | 原因 |
|---|---|
| **iframe 寬度 100%、max-width 880px** | 對應《數位時代》文章內容欄寬，不被後台容器壓窄 |
| **Inline `<style>` + Google Fonts CDN** | iframe 是獨立文檔，不繼承後台 CSS，必須自包含 |
| **文字色強制 `#1A1A1A`** | 避免後台白底主題吃字（同 bwt-html-table-component 規則） |
| **底色強制 Paper `#FAF7F1`** | 跟 BN 文章視覺一致，絕不用純白 |
| **強制雙軌設計（桌機 + 手機）** | 手機讀者佔比 > 60%，不可犧牲 |
| **iframe 高度＝純寫死＋100px buffer** | 全面禁止 postMessage（見下方「iframe 高度策略」章）。以 Playwright 實測桌機 scrollHeight + 100px buffer 寫死 |

---

## HTML 結構模板

```html
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>配件標題 ｜ 數位時代</title>
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&family=Inter:wght@400;500;600;700&display=swap">
<style>
  /* tokens：一律展開自 bwt-design-standard.md（唯一色票居所），不自行另立色號 */
</style>
</head>
<body>
<div class="wrapper">

  <header>
    <div class="kicker">即時追蹤 · TRACKER</div>
    <h1>配件主標</h1>
    <p class="deck">一句副標說明這份配件解答什麼問題。</p>
  </header>

  <div class="meta-bar">
    <div>資料來源：<strong>來源 1、來源 2</strong>　|　更新：YYYY / MM / DD</div>
    <div>製作：《數位時代》</div>
  </div>

  <!-- 可選：Stats strip 4 個關鍵數字 -->
  <div class="stats">...</div>

  <!-- FIGURE 01 主視覺（桌機 SVG / 手機簡化版） -->
  <div class="section">
    <div class="section-title">FIGURE 01 · 視覺類型</div>
    <h2 class="section-headline">主視覺標題</h2>
    <svg class="chart-desktop">...</svg>
    <div class="mobile-alt">...</div>
  </div>

  <!-- 可選：FIGURE 02 細節清單 -->
  <div class="section">
    <div class="section-title">FIGURE 02 · CHRONOLOGY</div>
    <h2 class="section-headline">編年清單</h2>
  </div>
  <div class="timeline">...</div>

  <footer>
    <strong>《數位時代》</strong>　|　資料整理：作者　|　製圖：YYYY.MM.DD
  </footer>

</div>
</body>
</html>
```

---

## 雙軌設計強制規則

所有 iframe 配件必須採用 **CSS Container Query** 實作雙軌設計，原因見下方「為什麼用 container query」段落。

### 必要設定

`.wrapper` 元素**必須**設定 `container-type: inline-size`：

```css
.wrapper {
  max-width: 880px;
  margin: 0 auto;
  container-type: inline-size;
  transition: max-width 0.25s ease;  /* 配合預覽切換動畫 */
}
```

雙軌規則以 `@container` 為**主軌**；`@media` 僅作漸進增強 fallback，**至多一條**：

```css
@container (max-width: 640px) {
  /* mobile / 窄版 layout 規則 */
}
```

**例外**：`body`、`html` 等不在 wrapper 內的元素，必須用 `@media`，因為 body 不是 container 內部（此類不計入 fallback 額度）。例如：

```css
@media (max-width: 640px) {
  body { padding: 12px 6px; }  /* 跟著真實 viewport 走 */
}
```

### 桌機（wrapper >640px）
- 完整 SVG 互動圖（時間軸氣泡、地圖、流程圖）
- Stats 多欄並列（建議 3–4 欄）
- 細節清單三欄式（日期 | 內容 | 數字）

### 手機（wrapper ≤640px）
- 隱藏複雜 SVG（`svg.chart-desktop { display: none; }`）
- 顯示簡化替代版（`.mobile-alt { display: block; }` 預設 hidden）
- Stats 改 2 欄
- 細節清單改兩排式（上排日期 + 數字 / 下排公司 + 原因）

### 為什麼用 container query

- **正確性**：container query 看 wrapper 寬度，不看 viewport。讀者在桌機讀者（viewport 1200px）寬度大、wrapper 880px → 桌機 layout；讀者在手機 viewport 390px、wrapper ~378px → 手機 layout。行為與 media query 等價。
- **可預覽**：透過 JS 改變 wrapper 寬度（例如 `style.maxWidth = '390px'`），container query 自動觸發 mobile 規則，**不需要重複寫一份 CSS**。這是「預覽工具」能運作的基礎。現代瀏覽器（2023 起）皆支援。

### 替代版設計選擇

| 桌機視覺 | 手機替代 |
|---|---|
| 時間軸氣泡圖 | Top N 規模排行榜（橫條，bar 寬按比例縮放） |
| 地理分布地圖 | 縣市卡片列表 |
| 多維散佈圖 | 分群卡片 |
| 流程圖 | 階段卡片直向排列 |
| 桌機橫條圖 | 同樣是橫條，但精簡 Top 數量 |

---

## 配色與字體

一律以 [`bwt-design-standard.md`](bwt-design-standard.md) 為準（唯一 tokens 居所），本檔不節錄色票表與字體表。iframe 配件特有補充僅兩點：

- BN Orange 在每個配件最多用於 1–2 種元素（如「關鍵數字 + AI 動因標籤」），不可大範圍鋪
- 英文／數字行高 `1.2–1.4`（中文行高 1.7–1.8 依總綱）

---

## Logo 加入規範

配件涉及具名公司時，建議加 logo 提升辨識度。

**預設方案：Google s2 Favicon API**

```html
<img src="https://www.google.com/s2/favicons?sz=64&domain=<domain>.com"
     alt="" width="32" height="32"
     style="border-radius:4px; flex-shrink:0; background:#FAF7F1; border:1px solid #D4D0C8; object-fit:contain;"
     onerror="this.style.visibility='hidden'">
```

- Google s2 是 Google 維護的公開 favicon API，免費、穩定
- 源圖一律 `sz=64`（retina 下清晰）；**顯示尺寸預設 32×32**，密集清單／品牌牆可縮至 20–22px（部署實例：排行列 32px、六月起密集 grid 20–22px）。本檔是 logo 尺寸唯一居所，其他檔不另立值
- `onerror="this.style.visibility='hidden'"` 失敗時隱藏框、但保留 grid 位置（避免版面跳動）
- 公司 domain 不確定或品牌 logo 設計太陽春時，省略 logo 即可（不要硬塞）

⚠️ **歷史警告**：早期版本曾使用 Clearbit Logo API（`logo.clearbit.com`），但 2024 年 Clearbit 被 HubSpot 收購後 API 已停用。**禁止使用 Clearbit**。

**已知 Google s2 抓不到（或不敷使用）的公司**

部分公司的 favicon 沒被 Google 索引，s2 API 會回 301 redirect 到 fallback globe 圖示——讀者看到「地球」當 logo。這類公司**必須**走自托管。

**查詢以實際目錄為準**（登錄表註定追不上）：產配件涉及具名公司時，先 `ls ~/Claude Project/projects/bnext-visuals/_assets/logos/` 看已有哪些自托管檔。截至 2026-07-28 共 7 檔：

| 公司 | 原因 | 自托管路徑 |
|---|---|---|
| Dow（陶氏化學） | dow.com 無公開 favicon，Google s2 回 fallback | `_assets/logos/dow.svg` |
| Lowe's | Google s2 只給 16×16，放大模糊 | `_assets/logos/lowes.svg` |
| eBay | Google s2 只給 16×16，放大模糊 | `_assets/logos/ebay.svg` |
| Cisco（思科） | Google s2 只給 16×16，放大模糊 | `_assets/logos/cisco.svg` |
| SpaceX | 高品質 SVG 已入庫（SpaceX IPO 配件） | `_assets/logos/spacex.svg` |
| Starlink | 高品質 SVG 已入庫（SpaceX IPO 配件） | `_assets/logos/starlink.svg` |
| xAI | 高品質 SVG 已入庫 | `_assets/logos/xai.svg` |

新增自托管檔後**不需**回填此表（表僅為快照）；`ls` 目錄永遠是準的。

**識別問題的方法**：產出配件後用 `curl -sL "https://www.google.com/s2/favicons?sz=64&domain=<domain>" -o /tmp/x.png && file /tmp/x.png` 看實際尺寸。如果回傳是 16×16（非 64×64），代表 Google 沒高解析度版本，應立即走自托管。

**自托管 SOP**

1. 從 Wikipedia 條目抓官方 SVG（多數大公司有）：
   ```bash
   curl -sL "https://en.wikipedia.org/wiki/<Company>" -A "Mozilla/5.0" | grep -oE 'upload\.wikimedia\.org/[^"]+\.svg' | head -3
   ```
2. 下載到自托管目錄：
   ```bash
   curl -L "<SVG_URL>" -A "Mozilla/5.0 bnext-visuals" -o "_assets/logos/<slug>.svg"
   ```
3. 把 HTML 中的 src 改成相對路徑：`../_assets/logos/<slug>.svg`
4. 注意 wordmark 型 logo（如 Dow、Cisco）寬高比非 1:1，box 已有 `object-fit: contain` 自動保持比例，但會偏窄。若視覺不佳，再找方塊版（公司官網品牌資源頁）

**下載來源優先序**：
- Wikipedia 條目（最穩，多有 SVG）
- 公司官網 press kit / brand resources 頁（最權威）
- [simpleicons.org](https://simpleicons.org)（科技公司為主）
- [Brandfetch](https://brandfetch.com)、[Logo Search](https://logosearch.app)（聚合服務）

---

## iframe 高度策略：純寫死（不要加 postMessage）

⚠️ **2026-05-18 重要教訓**：曾嘗試讓配件主動 postMessage 高度給父頁，期望 CMS 的 Alpine `x-data="iframe"` widget 接收後自動 resize。實測結果：

- CMS widget **確實有 message listener**（推測在 widget 內部 JS，HTML 看不到）
- 但接收後行為異常：iframe 點擊後高度被改成不正確的值，footer 後出現大塊空白
- 而且只在點擊 iframe 後才觸發，初載時 OK，造成「本來沒問題、點到才壞」的詭異現象

**結論：禁止在配件加 postMessage 發送高度的 JS**。回到純寫死 iframe height 模式。

### 嵌入碼

嵌入碼模板（純 iframe、無 script）唯一居所在 `visual-asset.md`「iframe 嵌入碼模板」段；height 一律填**量測流程算出的值**。

### 高度量測必須用 Playwright（不靠估算）

估算每個 div 累積高度誤差曾大到 1,000+ px。產配件後必須用 Playwright 量精準 scrollHeight，**統一量測流程**（唯一參數居所，本檔為準）：

**resize 1280×800 → navigate 配件 URL → `await document.fonts.ready` → 等 1500ms → 量 `document.documentElement.scrollHeight` → +100px buffer = iframe height**

```javascript
// Playwright 量測流程
await page.setViewportSize({ width: 1280, height: 800 });
await page.goto('https://terrylee-lang.github.io/bnext-visuals/...');
await page.evaluate(async () => {
  await document.fonts.ready;
  await new Promise(r => setTimeout(r, 1500));
  return document.documentElement.scrollHeight;
});
// 量得值 + 100 = iframe height
```

實測案例（2026-05-18）：裁員追蹤桌機實際 **3,815px**（估算 2,500–2,800，誤差 1,000+）→ iframe height = 3,815 + 100 = **3,915**。

### 取捨

桌機讀者：完美貼合（iframe 容器 ≈ 配件高度）。
手機讀者：iframe 內部會 scroll（因為手機 layout 內容比桌機更長）。手指 swipe 在 iframe 區內 scroll iframe，到邊界自動切回頁面 scroll，現代瀏覽器處理 OK。

### 未來重新評估的條件

若未來 IT 修復 CMS Alpine iframe widget 的 message listener（修掉「點擊 expand」副作用、或正確處理 height），再重新評估動態同步。在那之前，配件端一律不發 postMessage。

## 預覽工具（編輯內部使用，讀者不可見）

所有 iframe 配件**必須**內建預覽工具列，方便編輯／設計師快速切換桌機與手機 layout。

### 設計原則

- **URL 參數啟用**：只有當 URL 帶有 `?preview=1` 時才顯示工具列。讀者透過後台 iframe 嵌入的版本（URL 不帶參數）完全看不到。
- **位置**：`position: fixed; top: 14px; right: 14px; z-index: 9999`
- **視覺**：BN Navy 底色 + Paper 文字，active 鈕用 BN Orange
- **行為**：點擊「桌機」→ `wrapper.style.maxWidth = '880px'`；點擊「手機」→ `wrapper.style.maxWidth = '390px'`。配合 wrapper 的 `transition: max-width 0.25s` 達成平滑切換。

### 實作需求（不附全量模板，抄 repo 現成配件即可）

- 放在 `</div>`（wrapper close）後、`</body>` 前；CSS 加在 `<style>` 內
- JS 開頭檢查 `new URLSearchParams(location.search).has('preview')`，無參數直接 return（工具列連 DOM class 都不加）
- 兩顆鈕「桌機」「手機」：點擊切 `wrapper.style.maxWidth`（880px / 390px），active 鈕互斥、同步 `aria-pressed`
- a11y：`role="toolbar"` + `aria-label="預覽模式切換"`
- 完整實作直接參考 `bnext-visuals/2026/` 任一近期配件（如 `07-*.html`），全部內建同一套工具列

### 使用方式

| 情境 | URL | 工具列 |
|---|---|---|
| 讀者瀏覽（iframe 嵌入文章） | `.../05-layoffs-tracker.html` | 不顯示 |
| 編輯預覽 | `.../05-layoffs-tracker.html?preview=1` | 顯示，可切換桌機／手機 |

### 為什麼不加例外 box-shadow？

雖然 [`bwt-design-standard.md`](bwt-design-standard.md) 明文禁止 box-shadow，但**預覽工具列為編輯內部使用、不會出現在公開頁面**，加上輕微 shadow 純粹是為了在內容上方浮動時不貼平。讀者看不到，不違反「視覺識別給讀者的承諾」。**這是唯一允許的 box-shadow 例外**。

---

## Inline Style 規範

iframe 是獨立文檔，**所有樣式必須 inline**（在 `<style>` 內定義），不依賴外部 CSS（除了 Google Fonts CDN）。

**例外允許**：未來累積到 3+ 配件後，可以 `<link rel="stylesheet" href="../_assets/base.css">` 引用 repo 內共用 base CSS。

---

## 文字斷行規範（避免孤字）

中文／數字混排 + 窄寬度容器很容易出現孤字——常見兩種情境：

| 情境 | 解法 |
|---|---|
| **資訊段內部斷字**（「更新：2026 / 05 / 18」斷在「18」前） | `white-space: nowrap` 鎖整段 |
| **敘事文字最後一行單字**（「⋯⋯三家子公司」斷成「⋯三家子公」+「司」） | `text-wrap: pretty` 讓瀏覽器自動調整 |

### 強制做法 1：資訊段不可斷行

「label + 數值」一律 nowrap：

```html
<span class="meta-chunk">更新：2026 / 05 / 18</span>
```
```css
.meta-chunk { white-space: nowrap; }
```

多段 meta-chunk 用 `|` 連接時，手機隱藏分隔符 + chunk 改 block：

```css
@container (max-width: 640px) {
  .meta-bar .meta-sep { display: none; }
  .meta-bar .meta-chunk { display: block; }
}
```

### 強制做法 2：敘事文字啟用 text-wrap

body 加 `text-wrap: pretty`，瀏覽器自動讓最後一行至少有 2-3 字（CJK 友善）：

```css
body { text-wrap: pretty; }
```

標題類加 `text-wrap: balance`，讓多行標題各行寬度平均、視覺穩定：

```css
h1, h2, .section-headline, .deck { text-wrap: balance; }
```

舊版瀏覽器不識別 `text-wrap` 會自動 ignore，**不會壞，只是沒有優化效果**——graceful degradation，放心用。

### 自查情境

| 情境 | 處理 |
|---|---|
| 含冒號或數字的資訊段（「更新：2026 / 05 / 18」） | `white-space: nowrap` |
| 公司名 + 標籤（「Meta Platforms[AI]」） | `white-space: nowrap` |
| 大段敘事文字（一句話 reason） | body 已有 `text-wrap: pretty` 自動處理 |
| 多行標題（h1、h2、deck） | `text-wrap: balance` 平衡寬度 |

---

## 設計禁止項（重申自 bwt-design-standard）

- ❌ 純白 `#FFFFFF` 或純黑 `#000000` 底色 / 文字色
- ❌ box-shadow / drop-shadow
- ❌ gradient（背景 / 文字 / 邊框皆禁）
- ❌ 大範圍鋪 BN Orange（每個區塊最多 1–2 處強調）
- ❌ 4px / 6px 之類的「半圓角」（應 0px 直角 or 8px / 24px）
- ❌ 標題內用顏色變化代替底線強調

---

## 配件自我檢核 Checklist

產出 HTML 後，逐項確認：

- [ ] 底色 `#FAF7F1`（不是純白）
- [ ] 文字色 `#1A1A1A`（強制套用）
- [ ] BN Orange `#FF6B1A` 用於關鍵數字 / AI 標籤 1–2 處（節制）
- [ ] 字體 stack 含 "Noto Sans TC", "PingFang TC"
- [ ] 數字字體用 Inter + tnum
- [ ] 中文行高 1.7–1.8
- [ ] 圓角用 0px / 8px / 24px（不用 4px / 6px）
- [ ] 無 box-shadow、無 gradient
- [ ] `.wrapper` 設定 `container-type: inline-size` + `transition: max-width 0.25s ease`
- [ ] 雙軌以 `@container (max-width: 640px)` 為主軌（`@media` 僅 body 等 wrapper 外元素＋至多一條漸進增強 fallback）
- [ ] 內建 `?preview=1` 觸發的預覽工具列（桌機／手機切換鈕）
- [ ] iframe 寬度 100%、max-width 880px
- [ ] viewport meta 正確（`width=device-width, initial-scale=1`）
- [ ] Google Fonts CDN 載入（`Noto Sans TC` + `Inter`）
- [ ] Logo 用 Google s2 Favicon API（`sz=64`）+ onerror visibility:hidden fallback；**禁止 Clearbit**

---

## Webfont 載入

iframe 不依賴《數位時代》網站字體，**必須**載入 Google Fonts CDN：

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&family=Inter:wght@400;500;600;700&display=swap">
```

---

## 寫作風格

配件內所有可讀文字（標題、副標、清單描述、註腳）一律遵循 [`bwt-style-guide.md`](bwt-style-guide.md)：

- 兩岸用語轉換
- 日期格式：「2026 年 5 月 12 日」（不用 5/12 斜線）— **配件內因空間限制可用 `5/12`，但 caption 與 footer 用全寫**
- 中英數混排留半形空格
- 禁用空洞開場白、行銷形容詞
- 技術術語第一次出現時加中文白話解釋
- **配件內英文術語強制補中文括號**：配件常是讀者首次接觸該詞、且沒有完整上下文（不像稿件可在段落中補白話）。例如 `IDEA（概念）`、`MVP（最小可行產品）`、`LAUNCH（發布）`、`SCALE（規模化）`、`TAM（市場總額）`、`SOC 2（系統與組織控制標準）`。中文用小一級字體 + Cool 600 顏色，避免搶英文標題的視覺重量

### 破折號（配件一律 0 次）

規則本體見 [`bwt-style-guide.md`](bwt-style-guide.md) 與 [`bwt-visual-checklist.md`](bwt-visual-checklist.md) §五；統一自查指令：`grep -cE "[—－]" <file>`，必須為 0。

---

## 版本紀錄

| 日期 | 變更 |
|---|---|
| 2026-05-18 | 初版建立。從 `bnext-visuals/2026/05-layoffs-tracker` 案例抽取規範。 |
| 2026-07-28 | 規範重整：決策表對齊「純寫死＋100px buffer」結論（全面禁止 postMessage）；量測流程統一（1280×800 / fonts.ready / 1500ms / +100px）；RWD 改「@container 主軌＋至多一條 @media fallback」；刪配色字體節錄（指向 design-standard）；logo 表補齊 7 檔並改以 `ls` 目錄為準、顯示尺寸定案（預設 32px，密集 20–22px）；預覽工具列縮為需求 bullets；刪瀏覽器版本表；破折號改一行引用。 |
