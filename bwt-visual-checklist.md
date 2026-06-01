# 子規範｜視覺配件工作流與交付 Checklist

此檔為《數位時代》[美術製作標準總綱](bwt-design-standard.md) 的**子規範**，規範視覺配件的**工作流紀律與交付前自查清單**。Design tokens 與元件結構不在本檔範圍，請參照：

- [`bwt-design-standard.md`](bwt-design-standard.md)：色彩、字體、字級、間距、設計原則
- [`bwt-iframe-visual-component.md`](bwt-iframe-visual-component.md)：iframe 配件 HTML 結構、雙軌設計
- [`bwt-html-table-component.md`](bwt-html-table-component.md)：文章內表格元件

---

## 適用範圍

所有產出視覺配件的 Skill 啟動時與交付前讀取本檔。涵蓋三種產品類型：

| 產品類型 | 套用 Skill |
|---|---|
| iframe 嵌入式互動配件 | `visual-asset` |
| 16:9 主視覺圖卡（1920×1080 PNG） | `visual-asset` |
| 社群分享圖、文章封面 Banner | `visual-asset`、`social-post` 配件 |

跨 skill 情境（例如 social-post 要產配件）也適用本檔的禁用詞與 spot-check 規則。

---

## 一、設計優先序（最高原則）

設計判斷照此優先序：

1. **服務讀者閱讀體驗**（辨識度、戲劇張力、reading flow）— 最高原則
2. **BN Design System**（[`bwt-design-standard.md`](bwt-design-standard.md)）— default 起點
3. **個案戲劇化元素** — 若服務讀者可從寬納入

Design System 是 default，不是鐵則。當戲劇化／辨識度元素確實服務讀者（譬如法庭 Q&A 律師雙色用 BN brand tokens 做語意對映、簡訊 iMessage 直覺氣泡、證物編號 footer 增加真實感），可以靈活納入——不要為了忠於規範犧牲閱讀體驗。

**節制原則仍適用**：BN Orange「節制」不是「不用」，speaker label 級別的小範圍染色不算違反「大範圍鋪色」禁令。無理由的裝飾（box-shadow、gradient、純白底）仍禁。

**Why**：2026-05-19 教訓——拍板「以 Design System 為準」後曾把所有戲劇化元素拿掉（律師雙色、簡訊氣泡、Email 縮排線），讀者辨識度與 reading flow 被犧牲。Terry 回饋：「設計元件要能服務讀者的閱讀體驗，這是最高原則，至於其他可以從寬處理」。

---

## 二、動手前自檢

### 1. 判斷產品類型（最重要，避免整套白工）

| 訊號 | iframe 嵌入配件 | 16:9 主視覺圖卡 |
|---|---|---|
| 使用者怎麼說 | 「做配件」「做個 tracker」「嵌入內文」「互動圖表」 | 「做主視覺」「做 cover」「文章封面」「社群分享圖」「Notion 封面」「16:9 圖卡」 |
| 互動性 | 有 | 無 |
| 桌機尺寸 | max-width 880px、響應式 | 1920×1080 固定 |
| 輸出 | 托管於 bnext-visuals、iframe 嵌入碼 | 單張 PNG |

**Why**：2026-05-20 做 Google I/O 懶人包時，第一版做成 iframe 配件，Terry 要的其實是主視覺圖卡，整套重做。判斷錯產品類型 = 整套白工。

### 2. 掃既有規範（禁止憑外部觀察自建）

接到「需要依據規範」的任務動手前，先 `ls ~/.claude/agents/` 讀相關規範檔。**禁止憑外部觀察（官網實測、其他媒體參考、品牌印象）自建一套規範系統。**

存疑時優先既有規範；若外部觀察與既有規範不一致，預設「既有規範為準」，並暴露差異讓 Terry 拍板。

**Why**：2026-05-19 曾用 Playwright 實測 bnext.com.tw 的 computed styles（`#FF5500` 橘、純白底）建立衝突 spec，差點覆寫既有 Design System（`#FF6B1A` + warm ivory）。實測值反映「臨時實作」，不是品牌目標規範。

---

## 三、16:9 主視覺圖卡 SOP

### 動手前必做

1. **判斷產品類型**：「配件」「圖卡」「主視覺」等模糊用語先確認桌機尺寸、互動需求
2. **對齊內文章節**：「N 大發布／重點」的 N 必須跟稿件章節對齊；逐條核對名稱、順序、用詞，不自編
3. **量素材圖檔 logo 位置**：用 PIL 算 logo 在原圖垂直 % 位置，決定 `object-position` 值
4. **預估 layout 預算**（1920×1080）：
   - Banner：300–340px（佔 ~30%）
   - h1 + 副標：180–240px
   - 卡片區（5 卡橫排）：350–400px
   - Footer：70–90px
   - Padding 上下：80–90px

### 字體預設（給 1920×1080 viewport，起步就要大）

| 元素 | 預設 | 不要低於 |
|---|---|---|
| h1 主標 | 60–72px | 56px |
| 副標 / deck | 30–36px | 28px |
| Kicker | 22–26px | 20px |
| 卡片產品名（item-name） | 28–32px | 26px |
| 卡片描述（item-desc） | 22–26px | 20px |
| 卡片序號（item-num） | 22–26px | 20px |
| Footer | 18–22px | 16px |

**經驗**：1920×1080 視覺空間很大，字體要敢用大尺寸；不確定時往上選，不要保守起步。

### 渲染後 spot-check

每次截圖後立刻檢查：

1. **Banner logo 是否真正上下／左右置中**（看圖確認，不是「我以為」）
2. **副標是否單行收完**（折行尾字孤行就改文案、加 max-width 或縮句子）
3. **卡片小標單行不折行、不孤詞**（如「Gemini Omni Flash」斷成「Gemini Omni / Flash」是孤詞）
4. **卡片內 desc 起始位置切齊**（用 `item-name { min-height }` 預留兩行高度）
5. **圖卡下端是否還有大塊空白**（有就放大字體或加內容，不要浪費）

### 技術 SOP

- 圖檔 logo 位置量測：`python3 -c "from PIL import Image; ..."` 找 dark rows mean → vertical %
- 渲染：`/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --headless=new --window-size=1920,1080 --screenshot=... <file URL>`
- 確認 PNG 尺寸：`file *.png` 看 `1920 x 1080`

---

## 四、iframe 配件交付 Spot-Check

每次交付前必跑這份 spot-check，避免每個錯誤都讓 Terry 親自抓。每一項都要實際渲染後逐項檢查，不可只靠 sub-agent 自報通過。

### 必查項

1. **敘事邏輯閉環**
   - 多張數字卡是否同口徑（例如 A 卡 9,122 − B 卡 8,870 = C 卡 250，三張要對得起來）
   - 不可一張用 2025/3 期、另一張用 2026/3 期還聲稱有「差距」
   - 中央 annotation 的起終點要與線圖節點精準對應（例如「2013→2018 / 5 年翻倍」要連 2,000 億和 4,000 億兩個節點，不能浮在中間）

2. **時間區間／期別／單位完整**
   - 所有財務數字必須帶會計年度（2025/3 期、2026/8 期），不可用「本期」「去年」等模糊詞
   - 所有非單位純數字要標單位（億日圓 / 店 / %）。曾發生「國內 4,701 + 東亞 2,222」被誤判為店數實為營收億日圓
   - 細項拆解要補齊到總額（4,701 + 2,222 + 923 = 7,846，缺一不可）
   - 不同公司會計年度差異要在 footer 標明，且標對家

3. **術語白話化**
   - 縮寫一律展開：SPA → 製造零售、PB → 自有品牌、IR → 投資人關係
   - 外語公司名要中文化或加註：西友 → 西友量販、Saison → 西武 Saison 集團
   - 不能假設讀者懂專業術語，配件比稿件正文要更白話

4. **節點視覺對應**
   - 每個有 callout 文字的節點都要有 leader line（虛線）連到 callout
   - 不可只有部分節點有虛線
   - leader line 兩端要視覺對應該節點，不能誤導讀者以為標的是別的點

5. **位置／遮蓋檢查**
   - 沒有泡泡擋住折線
   - 多家公司並列時建立明確位置規則（例如 A 公司全部在左、B 全部在右；或上下分布；或位置呼應折線）
   - 規則一旦定下就要全圖一致套用

6. **字體層級**
   - 桌機主標 ≥ 18px、副標 ≥ 14px、年份標籤 ≥ 13px
   - 核心 annotation（戲劇張力數字）≥ 22–26px
   - 1280px viewport 下肉眼舒適閱讀，不用湊近看
   - 手機版同步檢查不可比桌機小

7. **品牌色／規範**
   - 營收主數字用品牌色 + 加粗，副標籤統一深灰
   - 中性指標（如「差距 250 億」）不歸屬任一家，用深灰
   - 嚴守 `bwt-design-standard.md` 色票，禁止自創品牌色

8. **桌機 + 手機雙軌**
   - 桌機 SVG / 手機垂直清單兩處都要套相同規則
   - 手機版要實測 320 / 375 / 430 斷點

---

## 五、文字禁忌

### 禁用破折號

視覺配件（iframe 互動配件、16:9 主視覺圖卡、社群分享圖、文章封面 Banner）的所有文案，**一律禁用**全形破折號「——」與半形 em dash「—」。

這個規範比一般內文更嚴格：內文受 `bwt-style-guide.md` 約束為 ≤1 次／2,000 字，配件則是 0 次。

**Why**：2026-05-20 Terry 處理 Google I/O 懶人包配件時明確要求「配件都不要用」。

**How to apply**：
- 任何 visual-asset、bwt-iframe-visual-component、social-post 配件設計，文案改用其他分隔符（、／／冒號／句號／換行）
- 即使內文還允許節制使用，配件設計時都要主動掃描刪除
- 寫好配件 HTML 後在 finalise 前 grep 一輪「——」「—」確認 0 命中：
  ```bash
  grep -cE "[—－]" *.html  # 應為 0
  ```

### 其他文字規範

`bwt-style-guide.md` 的兩岸用語、人名公司名、禁用詞彙表（空洞開場、AI 式總結、行銷形容詞、翻譯腔、空指稱弱化詞）一律適用於配件文案。

---

## 六、工作流紀律

### 本地優先迭代，禁止散打 push

視覺配件一律先在本地完整迭代到 Terry 認可，再做 commit + push 上線。**不可以「改一點 → push → Terry 看 → 再改 → 再 push」的散打模式**。

**Why**：宜得利 vs 無印良品配件（2026-05-20）從 v1 到 v9 推了 9 次 git push，每次都靠 Terry 在線上 CDN 刷新後檢查，等待成本高、版本汙染嚴重、Terry 親自抓到的錯誤累積一長串。

**How to apply**：
- 收到改稿需求 → 在本地修改 → 本地渲染 spot-check（Playwright 或瀏覽器開檔案）→ 給 Terry 看 → 拿到 OK 後**才** `git commit && git push`
- 一次 push 對應一個 Terry 認可的版本，不對應「我自己改了一次」
- 多項改動可以在本地連續 iterate 多輪，不必每輪都 push
- CDN 等待時間（1–5 分鐘）是 push 的固定成本，能省則省

**例外**：若 Terry 明確說「先 push 上去我直接在線上看」，依指示執行。

### 預覽方式：給本地網址，不給截圖

視覺配件本地 iterate 階段，給 Terry 看時直接提供本地網址（例如 `http://localhost:PORT/path/file.html`、`?preview=1` 參數），**不要用截圖貼縮圖**。

**Why**：截圖縮放在訊息中往往看不清細節，Terry 自己用瀏覽器開 URL 一目了然、可自由縮放與切換桌機／手機。截圖只是浪費 token + iterate 速度。

**How to apply**：
- 本地起 `python3 -m http.server` 後，把網址貼給 Terry（標明桌機 URL + `?preview=1` 預覽工具列 URL）
- 若 Terry 主動要截圖才截
- 例外：渲染後的 16:9 主視覺圖卡（PNG 成品）仍可直接附圖，那是最終產出不是 iterate 預覽

---

## 七、交付前最終 checklist

每次交付（無論本地 spot-check 或正式 push）前自問：

- [ ] 產品類型判斷正確？（iframe 配件 vs 16:9 圖卡 vs 社群圖）
- [ ] 動手前掃過既有 `bwt-*` 規範？
- [ ] 設計優先序：服務讀者 > Design System default > 戲劇化從寬
- [ ] 16:9 圖卡：字體起步夠大、Banner logo 置中、副標單行、無大塊空白
- [ ] iframe 配件：8 項必查項（敘事閉環／單位完整／白話化／節點對應／不遮線／字體層級／品牌色／雙軌）
- [ ] 全文 0 個破折號「——」「—」
- [ ] 兩岸用語、人名規範符合 `bwt-style-guide.md`
- [ ] 本地完整 iterate 過，給 Terry 看用 URL 不用截圖

通過後才動 commit + push。
