---
name: bwt-pluto-push-spec
description: Pluto 後台（content-pool）推送規格的裁決紀錄。要把稿件推進內容池、或要產出文章內嵌 HTML（表格、配件）時載入。
---

# Pluto 推送規格（裁決紀錄）

稿件層的語意標記規範在 `bwt-style-guide.md`「文章元件語意標記」章，本檔只管**推送層**：標記如何轉成 plugin、HTML 如何隔離。

⚠️ **本檔是 IT 工程同事口頭裁決的紀錄，優先於任何規格書文件。** 現行的 `pluto-markdown-plugin-format` 規格書由對方以 AI 產出，已確認含實質錯誤（見下方「規格書已知錯誤」），對方正在重產。新版到手後補完本檔細節，但下列裁決不因新版而推翻，除非對方明確更正。

---

## 已確認裁決（2026-08-03，IT 工程同事）

| 項目 | 裁決 |
|---|---|
| 內容池是否解析 plugin | **會原樣保留**。`content-pool-add` 的 `content` 可直接帶 `<data type="application/json">` 區塊 |
| 根 class | **一律 `.mcp-ai-content`**。規格書寫的 `.wrapper-html` 是錯的 |
| JSON 格式 | **一定要單行**（compact）。規格書中間那些 pretty-print 多行範例不可照抄 |
| 圖片來源 | `media-upload` 回傳**一整個 object、內含 URL**，`picture` plugin 的 `src` 從該 object 取 |
| 表格 | **後台沒有表格 plugin**，一律自刻 HTML 走 `iframe` plugin |
| 測試環境 | **有 stage**（接法待問） |

**JSON 轉義層數**：對方只確認「一定要單行」，未逐字回答是否要在 JSON 之外再轉義一層。結合規格書唯一標示 Correct 的範例（單行 compact JSON，字串值內為正常的 `\n`／`\"`），判定為**單層正常 JSON 轉義，不做雙重轉義**。此為推論，首次實際推送若失敗先回頭驗證這一點。

---

## HTML 隔離規則（表格與內嵌配件）

`iframe` plugin 的 `data.html` 直接插進文章 DOM，不是 sandbox。與 GitHub Pages 託管的配件（`bwt-iframe-visual-component.md`）是**兩條不同的發布管道**，該檔規則不適用於此。

- 單一根容器 `<div class="mcp-ai-content">`，只能一個根元素
- CSS 一律寫成 `.mcp-ai-content .前綴-類名` 後代選擇器
- 禁用 `body`／`html`／`main`／`article` 選擇器、禁用全域 reset
- 禁用通用 class 名（`.container`／`.wrapper`／`.card`／`.title`／`.button`／`.header`／`.content`），一律加專案前綴
- JS 的 DOM query 鎖在根容器內，不用全域變數與全域監聽

---

## 推送參數映射（2026-09-04 首次實推裁決，`content-pool-add`）

Notion 欄位→參數的基本對應在 `notion-orchestrator.md`「對照 Pluto `content-pool-add` 參數」；本節補該處沒寫、實推時才發現必須決定的四項：

| 參數 | 規則 | 依據 |
|---|---|---|
| `writer` | 填 Pluto 的**作者註冊名**：用 `authorinfo`（`site=bnext`）以中文姓名查，取回傳的 `name`（如「Terry Lee」）。不是文末署名行的中文名；署名行留在 `content` 裡不動 | 首推 id 349／350 回傳 `creator` 由 API 帳號自動帶「李先泰」、`writer` 為所填值，兩者用途不同 |
| `permlink` | **必填**（schema required），後台不會自動生成。由推送者依標題產英文 kebab-case slug：≤100 字元、只用小寫字母、數字與連字號、不含中文；取「主體-事件」二到五個詞（例：`xbox-cloud-gaming-monthly-hour-limit`、`oura-ipo-s1-filing-revenue-loss`）。Notion「permlink」欄有值就照用 | 舊欄位表寫「留空則自動生成」為誤，2026-09-04 改正 |
| `page_title` | 回退鏈：Notion「SEO 標題」→「建議標題」→「標題」，取第一個非空者 | 首推時前兩欄皆空 |
| `content` ＋ `reference` | `content` 帶**完整頁面內文**（含頂部三大重點與摘要區塊、文末資料來源與署名兩行），`content_type` 填 `markdown`；`reference` 另傳文末那一行解析出的 JSON 陣列（規則見 `bwt-style-guide.md`「署名格式」） | 一比一映射，推送層不改稿 |

**待 Terry 確認**：資料來源同時存在於 `content` 文末與 `reference` 參數，後台是否顯示兩次——開 id 349／350 檢視。若重複，改為推送時從 `content` 剔除文末資料來源行（署名行是否保留一併裁決）；未確認前維持一比一映射。

## 待補（不要憑猜實作）

1. **《數位時代》的 `media-upload` site 值**：現行 enum（mc／mt／mschool／people／eightlife／fc／expo／meet-en／sushi／quickread／other）**沒有 bn**。對方表示要用的是 bn／mt／sd，但 bn 與 sd 不在 enum 內。未取得正式值之前不要用 `other` 硬推
2. **stage 環境接法**：URL、endpoint 或參數不明
3. **`textblock` 的 style type1 到 type6 各自視覺**：此題未獲答覆，暫時一律 `style:"default"`
4. **新版規格書**：對方重產中

## 規格書已知錯誤（拿到新版前的閱讀警示）

- 全文的 `.wrapper-html` 應讀作 `.mcp-ai-content`
- 檔案結尾被截斷：第 524 行的程式碼區塊未關閉，Incorrect 範例整段缺失
- 「Available Plugins」段的 pretty-print JSON 範例只能看欄位結構，實際輸出必須壓成單行

## Related

- `bwt-style-guide.md`「文章元件語意標記」：稿件層標記的唯一所在地
- `bwt-html-table-component.md`：表格元件（走本檔隔離規則時需改寫，見 CHANGELOG v3.2.2）
- `bwt-iframe-visual-component.md`：GitHub Pages 託管配件，**另一條管道**，隔離規則不同
- `notion-orchestrator.md`「對照 Pluto `content-pool-add` 參數」：Notion 欄位與推送參數的映射
