# 子規範｜HTML 表格元件

此檔為《數位時代》[美術製作標準總綱](bwt-design-standard.md) 的**子規範**，規範 HTML 表格元件的具體實作（結構、inline style、精緻化細節）。

**Design tokens（色彩、字體、間距、圓角等）一律以 [`bwt-design-standard.md`](bwt-design-standard.md) 為準**，本檔不重複定義，僅補充表格元件特有的 layout 與細節規則。

---

## 適用範圍

**目前僅 `etf-explainer` Skill 套用本規範**。其他產稿 Skill（deep-analysis、tutorial-article、news-daily、draft-polish）暫不適用，仍維持原 Markdown 表格輸出。待 etf-explainer 試行穩定後，再評估擴散至其他 Skill。

---

## 觸發條件

凡 etf-explainer 輸出**任何要以表格形式呈現的資料**，一律走本規範的 HTML 元件，不留例外。即便是 2 欄 4 列的小型對照表也適用。

不適用情境：
- 條列清單（目錄、適合誰買的條件列）—— 維持 Markdown 條列
- 行內並列（「分為三類：A、B、C」）—— 維持散文

---

## 關鍵設計決策

| 決策 | 原因 |
|---|---|
| **每張表自帶 inline style，不依賴外部 CSS** | 《數位時代》網站後臺把 HTML 嵌入區塊分開渲染，文章開頭的 `<style>` 不保證影響後面的表格 |
| **不在文章開頭放獨立 `<style>` 區塊** | 獨立 CSS 嵌入區塊在前臺會留下空白 |
| **文字色強制 `color:#1A1A1A;`** | 避免後臺白底主題吃字 |
| **卡片底用 `#FAF7F1` warm ivory（依 BN standard）** | 跟 BN 文章內文 grid 一致 |
| **重點數字用品牌橘 `#FF6B1A` 凸顯** | 每張表至多 1-2 列 / cell，呼應 BN brand voice（節制使用） |

---

## 表格元件特有的 design tokens

以下 tokens 是表格元件特有的 layout 參數（其餘色彩、字體、字級請參考 `bwt-design-standard.md`）：

| 項目 | 值 | 說明 |
|---|---|---|
| Cell padding | `14px 18px` | 一般 cell 留白 |
| 標題列 padding | `14px 20px` | 卡片標題列留白 |
| 註腳 padding | `8px 16px` | 註腳留白（窄） |
| 表格標題字級 | 19px | 標題列字級（加大、易讀） |
| 標題 letter-spacing | `0.02em` | 中文標題字距 |
| 表格內文字級 | 15px | 一般 cell 字級（`--text-body-sm`） |
| 表格內行高 | 1.7 | CJK 表格行高（`--leading-body-sm`） |
| 註腳字級 | 13px | 註腳字級 |
| 註腳行高 | 1.5 | 註腳行高 |

斑馬紋與強調列的具體色值不在本表重列，以下方 HTML 範本為準（hex 一律對齊 `bwt-design-standard.md` tokens）。表格標題字級 19px 為**經核可的產品級 token**（登記於 design-standard「產品級字級 tokens」段）。

---

## 表格內的文字寫作風格

表格內所有可讀文字（表格標題、欄位名、列項用語、註腳）的寫作風格，一律遵循 [`bwt-style-guide.md`](bwt-style-guide.md)：

- 兩岸用語轉換
- 日期格式：「2026 年 5 月 12 日」（不用 5/12 斜線）
- 中英數混排留半形空格
- 禁用空洞開場白、AI 式總結句、行銷形容詞
- 技術術語第一次出現時加中文白話解釋

---

## Notion 輸出結構

每個表格在 Notion 內依下列順序輸出三段內容：

1. **元件標記行**（一行文字，加粗）：

   ```
   **後臺元件｜表格名稱**
   ```

   例：`**後臺元件｜00981A 前 10 大持股**`

2. **Notion code block，語言指定 `html`**（內含完整的自帶 inline style 的 `<div>` 整段 HTML）

3. **接下一段正文敘述**

⚠️ **務必保留為 code block**，不要讓 HTML 被 Notion 轉成視覺表格或直接混入正文段落。編輯後續會手動把 code block 內的 HTML 複製到網站後臺。

**⚠️ 不要在文章開頭、或第一個表格元件之前生成獨立 `<style>...</style>` 區塊**。每張表元件都是自包含的 inline style 結構。

---

## HTML 範本

### 基本骨架（2 欄對照表，含斑馬紋與強調列）

```html
<div style="margin:24px 0;border:1px solid #D4D0C8;border-radius:8px;overflow:hidden;background:#FAF7F1;color:#1A1A1A;font-family:'Noto Sans TC','PingFang TC',-apple-system,'Microsoft JhengHei',sans-serif;">
  <div style="padding:14px 20px;font-weight:700;background:#ECE8DF;border-bottom:1px solid #D4D0C8;color:#1A1A1A;font-size:19px;line-height:1.5;letter-spacing:0.02em;">表格名稱</div>
  <div style="overflow-x:auto;-webkit-overflow-scrolling:touch;">
    <table style="width:100%;min-width:480px;border-collapse:collapse;font-size:15px;line-height:1.7;color:#1A1A1A;">
      <thead>
        <tr>
          <th style="padding:14px 18px;border-bottom:1px solid #D4D0C8;background:#ECE8DF;text-align:left;white-space:nowrap;color:#1A1A1A;">欄位 A</th>
          <th style="padding:14px 18px;border-bottom:1px solid #D4D0C8;background:#ECE8DF;text-align:left;white-space:nowrap;color:#1A1A1A;">欄位 B</th>
        </tr>
      </thead>
      <tbody>
        <tr><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;font-weight:700;white-space:nowrap;color:#1A1A1A;">一般列項</td><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;color:#1A1A1A;">內容</td></tr>
        <tr style="background:#F5F3EF;"><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;font-weight:700;white-space:nowrap;color:#1A1A1A;">斑馬紋列</td><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;color:#1A1A1A;">偶數列加 paper-2 淺底</td></tr>
        <tr><td style="padding:14px 18px;border-left:4px solid #FF6B1A;border-bottom:1px solid #D4D0C8;font-weight:700;white-space:nowrap;color:#FF6B1A;">強調列</td><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;font-weight:700;color:#1A1A1A;">第一 cell 加左側橘條 + 染橘色；其他 cell 僅加粗</td></tr>
      </tbody>
    </table>
  </div>
  <p style="padding:8px 16px;margin:0;color:#5C6470;font-size:13px;line-height:1.5;background:#FAF7F1;">手機版可左右滑動查看完整欄位。</p>
</div>
```

### 含數字欄（金額、百分比、權重）

數字 cell 使用 Inter + tnum 等寬數字：

```html
<td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;text-align:right;vertical-align:top;color:#1A1A1A;font-family:Inter,'Noto Sans TC',sans-serif;font-feature-settings:'tnum' 1;white-space:nowrap;">2,616.85 億元</td>
```

強調列的數字 cell：

```html
<td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;text-align:right;vertical-align:top;color:#1A1A1A;font-family:Inter,'Noto Sans TC',sans-serif;font-feature-settings:'tnum' 1;font-weight:700;white-space:nowrap;">95.88%</td>
```

### 含排序欄（前 N 大持股）

排序 cell 置中：

```html
<th style="padding:14px 18px;border-bottom:1px solid #D4D0C8;background:#ECE8DF;text-align:center;white-space:nowrap;color:#1A1A1A;">排序</th>
<td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;text-align:center;vertical-align:top;color:#1A1A1A;font-family:Inter,'Noto Sans TC',sans-serif;font-feature-settings:'tnum' 1;">1</td>
```

第一名（強調列）排序 cell 加左側橘條：

```html
<td style="padding:14px 18px;border-left:4px solid #FF6B1A;border-bottom:1px solid #D4D0C8;text-align:center;vertical-align:top;color:#FF6B1A;font-family:Inter,'Noto Sans TC',sans-serif;font-feature-settings:'tnum' 1;font-weight:700;">1</td>
```

### 含時間軸（大事紀）

時間 cell 加粗、不換行；事件描述 cell 不加粗：

```html
<tr><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;font-weight:700;white-space:nowrap;color:#1A1A1A;">2025 年 5 月 27 日</td><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;color:#1A1A1A;">基金成立</td></tr>
```

強調列（如「掛牌上市」這列）：

```html
<tr><td style="padding:14px 18px;border-left:4px solid #FF6B1A;border-bottom:1px solid #D4D0C8;font-weight:700;white-space:nowrap;color:#FF6B1A;">2025 年 5 月 27 日</td><td style="padding:14px 18px;border-bottom:1px solid #D4D0C8;font-weight:700;color:#1A1A1A;">掛牌上市，發行價 10 元</td></tr>
```

---

## 範本變體差異表

所有元素的完整 inline style **以上方「基本骨架」與各變體範本為 canonical 版本**，不逐元素重抄；照範本複製後只改差異：

| 變體 | 相對一般 cell 的差異 |
|---|---|
| 數字欄 `<td>` | `text-align:right;` ＋ `font-family:Inter,'Noto Sans TC',sans-serif;font-feature-settings:'tnum' 1;` ＋ `white-space:nowrap;` |
| 排序欄 `<th>` / `<td>` | `text-align:center;`（數字字體規則同上） |
| 列項首欄（label）`<td>` | `font-weight:700;white-space:nowrap;` |
| 強調列：第一個 cell | 加 `border-left:4px solid #FF6B1A;` ＋ `color:#FF6B1A;` ＋ `font-weight:700;` |
| 強調列：其他 cells | 僅 `font-weight:700;`（文字色維持 `#1A1A1A` 不染橘，避免整列搶眼） |
| 偶數列斑馬紋 `<tr>` | `<tr style="background:#F5F3EF;">`；⚠️ **強調列不套斑馬紋**（避免雙重背景） |

> **min-width 建議值**：2 欄 480px、3 欄 560px、4 欄以上 640px / 720px。確保手機版有充足橫向滑動空間。

---

## 細節規則

### 表格標題（標題列文字）

- 與「**後臺元件｜XXX**」標記行的「XXX」一致
- 簡潔，5-15 字
- 不含日期（日期放在註腳）

例：
- ✅ `00981A 前 10 大持股`
- ❌ `00981A 前 10 大持股（資料日期 2026 年 5 月 12 日）`

### 表格註腳

固定句型：

| 情境 | 寫法 |
|---|---|
| 有資料日期 | `資料日期：2026 年 5 月 12 日；手機版可左右滑動查看完整欄位。` |
| 有資料日期＋來源 | `資料來源：統一投信；手機版可左右滑動查看完整欄位。` |
| 純比較表、無時點 | `手機版可左右滑動查看完整欄位。` |

格式要點：
- 資料日期用中文日期（不用斜線）
- 分號分隔資料日期與手機提示
- 句尾保留「。」

### 斑馬紋規則

- **偶數列**（第 2、4、6...列）加 `background:#F5F3EF`
- **奇數列**（第 1、3、5...列）不設背景（透明、顯示卡片底 `#FAF7F1`）
- **強調列例外**：強調列**不套斑馬紋**（避免雙重背景），無論該列是第幾列

### 強調的視覺權重

| 強調強度 | 做法 | 適用 |
|---|---|---|
| 弱 | `font-weight:700;`（保持 `#1A1A1A` 色） | 列項首欄（label）、表頭、列名 |
| 中 | `font-weight:700;` + 斑馬紋 | 一般加粗 |
| 強 | 左側橘條 + label 染橘 + 其他 cell 加粗 | 每張表 1-2 列最重要的數字 / 事件 |

**避免**：整欄都染品牌橘、`<strong>` 標籤散亂使用、強調過度造成視覺疲勞。

### 金額單位寫法

- 單位寫在 `<td>` 內，**不寫在 `<th>` 表頭**
- 統一用「億元」「萬元」「%」等中文 / 半形單位
- 含千分位逗號（如 `2,616.85`）

例：
- ✅ `<th>金額</th>` + `<td>2,616.85 億元</td>`
- ❌ `<th>金額（億元）</th>` + `<td>2,616.85</td>`

### 中文 vs 英數字混排

- 數字、英文代號、百分比與中文之間留半形空格（依 `bwt-style-guide.md` 風格規範）
- 例：`股票 2,616.85 億元`，不寫 `股票2,616.85億元`

### 連結（如有）

- 表格內若需放連結（如導流至同站文章），用：

```html
<a href="URL" style="color:#1A1A1A;text-decoration:underline;text-decoration-thickness:2px;text-underline-offset:3px;">文字</a>
```

- 連結 hover 由網站全站樣式控制，inline 不需指定
- 不在表格內用 Markdown `[文字](URL)`

### `<strong>` 與 `<em>` 標籤

- **不使用 `<strong>` / `<em>`**：強調權重已透過 inline style 的 `color` + `font-weight` 控制，避免雙重強調
- 若需要在敘述句內強調某幾個字，用 `<span style="color:#FF6B1A;font-weight:700;">文字</span>`

---

## 出稿前自查

1. 每個表格是否都有「**後臺元件｜...**」標記行？
2. 是否**沒有**在文章開頭、或第一個表格元件之前生成獨立 `<style>...</style>` 區塊？
3. HTML 是否全在 Notion code block 內（語言 `html`）？沒有 HTML 流到正文段落？
4. 每個元素是否**都有 inline style**？沒有任何元素只靠 class 或外部 CSS 渲染？
5. 文字色是否**強制 `color:#1A1A1A;`**？避免後臺白底吃字？
6. 卡片背景是否用 `#FAF7F1`（warm ivory），不用純白？
7. 數字欄是否都用 `font-family:Inter,'Noto Sans TC',sans-serif; font-feature-settings:'tnum' 1;` 等寬對齊？
8. 強調列（左側橘條 + label 染橘）是否節制使用（每張表至多 1-2 列）？
9. 斑馬紋是否套在偶數列？強調列是否**未**套斑馬紋？
10. 註腳是否包含「手機版可左右滑動查看完整欄位。」？
11. min-width 是否設定（依欄位數調整 480 / 560 / 640 / 720px）？

---

## 注意事項

- **不可改 inline style 的色號**：色號對應 BN Design System，自行更動會破壞品牌一致性
- **不可省略 inline style 的任何屬性**：每個元素都要完整 inline style，不依賴外部 CSS
- **不可只用 class**：class 可選用作語義標記，但不可省略 inline style
- **不可在文章開頭放獨立 `<style>` 區塊**：會留下空白、且後臺嵌入區塊分開渲染不保證生效
- **不可用 Notion 視覺表格代替**：必須是 code block 形式，編輯才能複製 HTML 到網站後臺
- **不可加 box-shadow / drop-shadow**：BN Design System 規範深度只靠表面對比 + 1px border 達成
- **不可用純白 `#FFFFFF` 或純黑 `#000000` 當底**：底色必須在 BN ivory / slate 色域

---

## 設計依據

本子規範遵循 [`bwt-design-standard.md`](bwt-design-standard.md) 的 BN Design System tokens，原始 design bundle 完整保留於 `~/Claude Project/projects/bn-design-system/`。

精緻化改造方向（斑馬紋、左側橘條、加大 padding、字級拉大）於 2026-05-13 確認套用於 etf-explainer 的 6 個配件範本，視覺驗證通過後納入本規範。
