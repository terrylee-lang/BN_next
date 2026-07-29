# 《數位時代》美術製作標準總綱

此文件是《數位時代》產稿 Skill 與配件元件的**共通美術製作標準**，所有「會產出視覺配件」的 Skill 都應遵守。涵蓋色彩、字體、間距、圓角、設計原則等 design tokens 與規範。

設計依據：**Claude Design 匯出的「Business Next Design System」bundle**（BN Orange + Warm Ivory + Anthropic-style 排版）。原始 bundle 完整存於 `~/Claude Project/projects/bn-design-system/`。

---

## 適用範圍

**所有產出視覺配件的 Skill 都應遵守本標準**（包括 etf-explainer、未來的 deep-analysis 配件、tutorial-article 配件等）。

**子規範索引**（依配件類型分檔，引用本標準的 tokens）：

| 子規範 | 涵蓋元件 | 套用 Skill |
|---|---|---|
| [`bwt-html-table-component.md`](bwt-html-table-component.md) | HTML 表格元件 | 目前僅 `etf-explainer`；未來評估擴散 |
| [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md) | iframe 嵌入式互動視覺配件（tracker、時間軸、橫條排行等） | `visual-asset` |
| [`bwt-visual-checklist.md`](bwt-visual-checklist.md) | 視覺配件工作流與交付 Checklist（產品類型判斷、16:9 SOP、iframe spot-check、禁用破折號、本地優先 iterate） | `visual-asset`、跨 skill 配件情境 |
| （未來）`bwt-key-numbers.md` | 關鍵數字呈現元件 | 未建立 |
| （未來）`bwt-feature-card.md` | 文章 feature card | 未建立 |
| （未來）`bwt-callout-box.md` | 編按 / 引用框 | 未建立 |

新增子規範時，**必須**：
1. 引用本標準的 design tokens（不自行另立色號 / 字體）
2. 遵守「核心設計原則」段所列規則
3. 在本檔的子規範索引表新增一行

---

## Design Tokens

### 1. 色彩

#### Brand primary

| Token | Hex | 用途 |
|---|---|---|
| BN Orange | `#FF6B1A` | 品牌主色、CTA、key numbers、強調 |
| BN Orange Deep | `#E55A0F` | hover / pressed 狀態 |
| BN Orange Soft | `#FFE7D6` | 淡橘背景（罕用） |
| BN Navy | `#0F1A2E` | 深色 editorial 卡片底 |
| BN Navy 2 | `#1B2A45` | navy 卡片進階層 |
| BN Black | `#1A1A1A` | 內文主色（**所有元件文字色強制套用此值**，避免後臺白底吃字） |

#### Warm parchment surfaces（核心識別）

| Token | Hex | 用途 |
|---|---|---|
| Paper | `#FAF7F1` | 頁面底色、卡片底（warm ivory，**絕不用純白 `#FFFFFF`**） |
| Paper 2 | `#F5F3EF` | nav / 軟卡片、表格斑馬紋偶數列 |
| Paper 3 | `#ECE8DF` | 標題列底、表頭底、強調區塊底 |
| Paper 4 | `#E3DECF` | 最深 warm-gray 卡片 |

#### Neutrals

| Token | Hex | 用途 |
|---|---|---|
| Warm 300 | `#D4D0C8` | 卡片邊框、表格邊框 |
| Cool 600 | `#5C6470` | 註腳、metadata、caption |
| Cool 400 | `#8A93A0` | muted 文字 |
| Slate 700 | `#2A3142` | dark text on light surfaces |
| Hairline | `rgba(15, 26, 46, 0.12)` | 極細分隔線 |

#### Semantic accents（限制使用）

| Token | Hex | 用途 |
|---|---|---|
| Signal Yellow | `#F5C518` | opportunity / 上漲 |
| Alert Red | `#E63946` | risk / 下跌 |
| Tech Cyan | `#3DB5C6` | 第三色，僅 data-viz 用 |

#### 顏色使用原則

- **內文主色強制**：所有可讀文字一律 `#1A1A1A`，**不可省略此設定**（避免網站樣式讓文字變白色看不見）
- **底色用 warm ivory**：所有卡片 / 容器底色用 `#FAF7F1`，**絕不用純白 `#FFFFFF` 或純黑 `#000000`**
- **品牌橘節制使用**：`#FF6B1A` 是「moments of intentional warmth」，每個視覺區塊最多 1-2 處強調，過度使用會稀釋品牌色重量
- **無 box-shadow**：BN 規範深度只靠表面對比 + 1px border，**不可加 drop-shadow / box-shadow**
- **無 gradient**：顏色轉換用 hard-edged surface bands，**不可加漸層**

---

### 2. 字體

| Token | Stack | 用途 |
|---|---|---|
| `--font-sans-tc`（顯示字體） | `"Noto Sans TC", "PingFang TC", -apple-system, "Microsoft JhengHei", sans-serif` | 中文為主的標題、卡片文字 |
| `--font-sans-en`（基礎字體） | `"Inter", "Noto Sans TC", -apple-system, BlinkMacSystemFont, sans-serif` | UI 主字體、英文混排、body |
| `--font-mono`（等寬字體） | `"JetBrains Mono", ui-monospace, SFMono-Regular, Menlo, monospace` | metadata、code、技術標籤 |
| `--font-serif-tc`（編輯襯線） | `"Noto Serif TC", "PingFang TC", "Songti TC", serif` | 暗色 editorial 卡片標題（Anthropic Serif 的 BN 對應） |

#### 數字字體規範（重要）

**所有表格、卡片內的數字一律用 Inter + tnum 等寬數字**：

```
font-family: Inter, 'Noto Sans TC', sans-serif;
font-feature-settings: 'tnum' 1;
```

對齊好看、視覺一致。**重點 key numbers 可染品牌橘色 `#FF6B1A` 凸顯**（節制使用）。

---

### 3. 字級與行高

| Token | 字級 | 行高 | 用途 |
|---|---|---|---|
| caption | 13px | 1.5 | 註腳、超小字 |
| meta | 14px | 1.5 | metadata、tag |
| body-sm | 15px | 1.7 | 表格內文、小型卡片 body |
| body | 17px | 1.8 | 一般 body（中文閱讀重，需 1.8） |
| body-lg | 18px | 1.8 | 加大 body |
| h3 | 22px | 1.45 | 第三層標題 |
| h2 | 28px | 1.35 | 第二層標題 |
| h1 | 44px | 1.2 | 主標題 |
| display | 72px | 1.1 | display 級 |
| headline-xl | 96px | 1.05 | 大畫面 hero headline |

#### 中文字距規範

- **中文** letter-spacing：`0.02em`（中文閱讀略疏） — 標題與大字 size 適用
- **拉丁字符 display** letter-spacing：`-0.02em`（display 級別字緊收）
- **等寬字符**：不設 letter-spacing

#### 經核可的產品級字級 tokens（授權子規範自持）

以下兩組字級不在上表，屬**產品級 tokens**，經核可由對應子規範單一自持、不回填本表：

- **16:9 主視覺圖卡字體表**（1920×1080 viewport 專用）→ 住 [`bwt-visual-checklist.md`](bwt-visual-checklist.md) §三
- **表格標題字級 19px** → 住 [`bwt-html-table-component.md`](bwt-html-table-component.md)

除此之外，子規範不得另立字級 / 色號；新增產品級 token 需 Terry 核可並在此登記。

---

### 4. 間距（4px 基準）

| Token | 值 |
|---|---|
| s-1 | 4px |
| s-2 | 8px |
| s-3 | 12px |
| s-4 | 16px |
| s-5 | 20px |
| s-6 | 24px |
| s-8 | 32px |
| s-10 | 40px |
| s-12 | 48px |
| s-16 | 64px |
| s-20 | 80px |
| s-24 | 96px |

#### Layout 規範

| 項目 | 值 |
|---|---|
| 頁面 max-width | 1240px |
| Section gap | 80px |
| Card padding | 32px |
| Gutter | 24px |

#### 配件內留白建議

| 配件元件 | 內部 padding |
|---|---|
| 卡片標題列 | 14px 20px |
| 表格 cell | 14px 18px |
| 註腳 | 8px 16px |
| 一般卡片 | 32px |

---

### 5. 圓角

| Token | 值 | 用途 |
|---|---|---|
| r-none | 0px | 按鈕、tag、badge（直角是 BN signature） |
| r-card | 8px | 一般卡片、表格元件 |
| r-panel | 16px | 大型 panel |
| r-feature | 24px | 暗色 editorial feature 卡片 |
| r-cta | `0 0 8px 8px` | 主 CTA 按鈕的不對稱圓角（簽名式設計） |
| r-pill | 999px | pill 標籤（罕用） |

#### 圓角原則

- 一般按鈕用 `0px`（直角），**不用 4px / 6px 之類的「半圓角」**
- 卡片 / 表格元件用 `8px`
- 大型 feature 卡片用 `24px`

---

### 6. 強調系統

BN Design System 採用「**underline as emphasis**」+「**brand orange highlights**」雙軌強調機制：

#### Mechanism 1：底線強調（標題用）

關鍵字加粗底線：

```
text-decoration: underline;
text-decoration-thickness: 4px;
text-underline-offset: 6px;
text-decoration-color: #FF6B1A;
```

用於：display 級標題的關鍵詞（如「研究」「產品」）強調。**不可用顏色 / bold 變化代替底線**。

#### Mechanism 2：品牌橘強調（key numbers 用）

```
color: #FF6B1A;
font-weight: 700;
```

用於：表格 / 卡片內**最重要的 1-2 處數字或關鍵事件**。

#### Mechanism 3：左側橘豎條（區塊錨點用）

```
border-left: 4px solid #FF6B1A;
```

用於：表格內強調列的第一個 cell、callout box 的左側邊條等視覺錨點。

**節制原則**：每個視覺區塊最多 1-2 處用品牌橘色，過度使用會稀釋重量。

---

### 7. Motion（過渡動畫）

| Token | 值 | 用途 |
|---|---|---|
| ease-standard | `cubic-bezier(.2,.7,.2,1)` | 一般 transition |
| ease-out | `cubic-bezier(0,.7,.3,1)` | 進入動畫 |
| dur-fast | 120ms | 快速反應（hover） |
| dur-base | 200ms | 一般 transition |
| dur-slow | 320ms | 較慢的場景切換 |

---

## 核心設計原則

依 Claude Design 的 BN Design System 與 Anthropic 風格參考，本標準的核心原則：

### 1. Warm parchment surfaces

所有卡片底色必須在 BN ivory 色域內：`#FAF7F1` / `#F5F3EF` / `#ECE8DF` / `#E3DECF`。**絕不用純白 `#FFFFFF`**。

### 2. Achromatic-dominant + chromatic accent

整體色域以無彩度的 warm gray / ivory 為主，色彩預算全部花在 BN Orange 上（罕用、節制）。**不可同時使用多個彩色 accent**。

### 3. Flat surfaces + 1px borders

深度只靠表面對比 + 1px border 達成，**不加 box-shadow / drop-shadow**。Border 用 `#D4D0C8`（warm 300）或 hairline `rgba(15, 26, 46, 0.12)`。

### 4. Direct typographic hierarchy

層次靠字體粗細、字級、size 對比達成，**不靠 ornament（裝飾、背景紋路）**。

### 5. Hard-edged transitions

色帶 / surface 之間用 hard-edged（直角過渡），**不加 gradient fade**。

### 6. Brand orange as moments

`#FF6B1A` 是「intentional warmth moments」，每個視覺區塊 1-2 處強調，**絕不大範圍鋪色**。

### 7. Underline-as-emphasis（非顏色）

標題內關鍵字用 4px 底線強調，**不改顏色 / 加粗**作為標題內強調機制。

### 8. CJK reading 行高 1.7-1.8

中文閱讀比英文重，body 行高 1.7-1.8（不是英文的 1.4-1.6）。

---

## Webfont 載入規範

若元件部署在獨立網頁（如預覽檔、外部嵌入），建議載入 Google Fonts CDN 確保字體可用：

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700;900&family=Noto+Serif+TC:wght@500;700;900&family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap">
```

若元件嵌入《數位時代》網站（網站已載字體），inline style 直接套用 font-family stack 即可，**不重複載入 webfont**。

---

## Do's and Don'ts

### Do

- ✅ 用 `#FAF7F1` ivory 作為底色，**絕不用純白**
- ✅ 文字色強制 `#1A1A1A`（避免後臺吃字）
- ✅ 圓角用 `0px`（按鈕）/ `8px`（卡片）/ `24px`（feature card），**不用 4px / 6px**
- ✅ 標題關鍵字用底線強調（4px 厚）
- ✅ 重點數字用 BN Orange `#FF6B1A`，節制使用
- ✅ 中文行高 1.7-1.8
- ✅ Border 用 `#D4D0C8`（warm 300）
- ✅ 數字字體用 Inter + tnum 等寬

### Don't

- ❌ 用純白 `#FFFFFF` 或純黑 `#000000` 當底色 / 文字色
- ❌ 加 box-shadow / drop-shadow
- ❌ 加 gradient
- ❌ 在標題內用顏色變化代替底線強調
- ❌ 大範圍鋪 brand orange
- ❌ 用 4px / 6px 之類的「半圓角」
- ❌ 同一區塊使用多個 chromatic accent
- ❌ 用 inline style 加 `<strong>` / `<em>` 標籤同時控制粗細（用 inline style 的 font-weight 即可）

---

## 寫作風格與美術製作的職責分工

| 規範 | 負責層面 | 檔案 |
|---|---|---|
| **寫作風格** | 兩岸用語、人名、日期、禁用詞彙、技術白話化、署名 | [`bwt-style-guide.md`](bwt-style-guide.md) |
| **美術製作** | Design tokens、設計原則、配件結構 | 本檔（`bwt-design-standard.md`） + 各子規範 |
| **個人聲音指紋** | Terry 個人語氣偏好 | `bwt-voice-reference.md`（Terry 個人，不進共享 repo） |

三者並行：寫作風格控制「該怎麼寫」、美術製作控制「該怎麼長」、個人聲音指紋控制「該帶什麼個性」。

---

## 設計依據與原始檔

本標準的 design tokens 抽取自 Claude Design 匯出的 **Business Next Design System** bundle，完整 bundle 保留於：

`~/Claude Project/projects/bn-design-system/`

bundle 內容含：
- `README.md`（bundle 說明）
- `project/colors_and_type.css`（色彩與字體 CSS variables）
- `project/uploads/Anthropic風格DESIGN.md`（Anthropic 風格參考）
- `project/preview/*.html`（21 個元件 / token 預覽）
- `project/ui_kits/website/*.html`（網站文章樣本）
- `project/assets/businessnext-logo.jpg`（BN logo）

**bundle 不進共享 repo**（檔案大、含 logo 圖片），僅在 Terry 個人 vault 保留。若其他編輯部成員需要對標 BN 視覺設計，透過 Terry 取得 bundle。

---

## 版本紀錄

| 日期 | 變更 |
|---|---|
| 2026-05-13 | 初版建立。從 Claude Design 匯出的 Business Next Design System bundle 抽取 tokens、整理為共通美術製作標準。當時僅 `bwt-html-table-component.md` 為子規範。 |
| 2026-07-28 | 視覺規範重整：子規範已達 3 檔（table / iframe / checklist），索引表以本檔為唯一目錄；rubric 歸 `bwt-visual-checklist.md`、工作流歸 `visual-asset.md`、tokens 唯一居所在本檔；新增「經核可的產品級字級 tokens」段。 |
