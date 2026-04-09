---
name: lint-vault
description: |
  Obsidian Vault 健康檢查 Skill。掃描 ~/Claude Project/ 所有筆記，找出孤兒筆記、缺連結、過時內容、格式不符等問題，輸出待辦清單供 Terry 決定是否修正。

  請在以下情境下主動使用此 Skill：
  - 使用者說「幫我檢查 vault」、「跑 lint」、「vault 健康檢查」
  - 使用者說「有沒有孤兒筆記」、「哪些筆記過時了」
  - 任何需要對 vault 進行維護掃描的情境
---

# Lint Vault — Vault 健康檢查 Skill

掃描 `~/Claude Project/` 所有 Markdown 筆記及 memory 目錄，輸出八類問題報告。不自動修改任何檔案。

---

## 執行步驟

### 1. 孤兒筆記（Orphan Notes）

找出 `research/`、`people/`、`projects/` 中，沒有被任何其他筆記以 `[[]]` 連結引用的頁面。

輸出格式：
```
【孤兒筆記】
- research/foo.md — 未被任何筆記連結
```

### 2. 缺連結（Missing Backlinks）

掃描所有筆記內文，找出提到已知公司名、人名、專案名但沒有加 `[[]]` 的地方。比對基準：現有筆記的檔名。

輸出格式：
```
【缺連結】
- research/bar.md 第 12 行：提到「OpenAI」但未加 [[openai-strategy]]
```

### 3. 過時筆記（Stale Notes）

兩種判定方式並行：

**A. 檔案層級**：找出 `research/` 中超過 6 個月未更新、且內容涉及快速變動領域（AI、科技公司、產業政策）的筆記，標記為「建議複查」。

**B. 事實層級**：掃描所有 `<!-- valid: YYYY-MM -->` 時效標記，若標記時間距今超過 6 個月，單獨列出該條事實。

輸出格式：
```
【過時筆記】
- research/baz.md — 最後更新 2025-09，涉及 AI 產業，建議複查
- research/qux.md 第 8 行：「OpenAI 估值 800 億美元 <!-- valid: 2025-06 -->」— 時效標記已過期，建議更新或移除
```

### 4. 格式不符（Format Issues）

檢查 `research/` 所有筆記是否符合標準格式。分兩層檢查：

**A. Frontmatter**：必須包含 `title`、`source`、`created` 三個欄位（`author` 為選填，不檢查）。缺少任一必填欄位則標記。

**B. 內文區塊**：必須包含 Summary、Key Points、Quotes / Evidence、My Commentary（含「稿件潛力」子區塊）、Related。缺少任一區塊則標記。

輸出格式：
```
【格式不符】
- research/qux.md — 缺少 frontmatter: source
- research/baz.md — 缺少 My Commentary > 稿件潛力
```

### 5. 矛盾偵測（Contradiction Detection）

掃描 `research/` 和 `people/` 中涉及同一主題（同一公司、人物、技術）的不同筆記，比對數字、事實、時間線是否一致。重點檢查：

- 同一指標在不同筆記中出現不同數值（營收、估值、用戶數等）
- 同一人物的職稱或所屬公司在不同筆記中不一致
- 時間線矛盾（事件先後順序衝突）

若數值不同但各自標有 `<!-- valid: YYYY-MM -->` 時效標記，視為「不同時間點的數據」，不算矛盾，但建議確認是否需要淘汰舊數據。

輸出格式：
```
【矛盾偵測】
- research/aaa.md 說「OpenAI 估值 800 億美元」
  research/bbb.md 說「OpenAI 估值 3,000 億美元」
  → 可能是不同時間點，建議加上時效標記或更新舊筆記
```

### 6. 筆記量門檻提醒（Scale Check）

統計 `research/` 中的筆記數量（不含 `_index.md`）。若超過 30 篇，在報告末尾加上提醒：

```
⚠️ research/ 目前有 N 篇筆記，已超過 30 篇門檻。建議評估分層載入機制（在 _index.md 加入優先級欄位，區分常用 / 備查筆記）。
```

### 7. Draft 清理提醒（Draft Cleanup）

掃描 `draft/` 中所有檔案（不含 `_register.md`），若檔案建立日期距今超過 30 天，列出提醒。同時檢查 `draft/_register.md` 中狀態為 `done` 的檔案，建議刪除或搬至 archive/。

輸出格式：
```
【Draft 清理提醒】
- draft/draft-foo.md — 建立超過 30 天，建議確認是否仍需要
- draft/draft-bar.md — register 狀態為 done，建議刪除或搬至 archive/
```

### 8. Memory 健康檢查（Memory Lint）

掃描 `~/.claude/projects/-Users-terrylee-Claude-Project/memory/` 下所有記憶檔案（不含 `MEMORY.md` 索引本身），檢查三類問題：

**A. 過時**：記憶內容涉及已變更的事實（例如提到的檔案路徑已不存在、流程已改、工具已換）。

**B. 矛盾**：兩條記憶之間的建議或事實互相衝突。

**C. 可合併**：多條記憶涵蓋同一主題，可以整合成一條更精簡的記憶。

輸出格式：
```
【Memory 健康檢查】
- feedback_xxx.md — 提到的路徑 ~/old/path 已不存在，建議更新或移除
- feedback_aaa.md 與 feedback_bbb.md 對同一主題給出不同建議，建議合併
- project_xxx.md 與 project_yyy.md 內容高度重疊，建議整合為一條
```

---

## 輸出結構

```
# Vault Lint 報告｜{日期}

## 孤兒筆記（N 筆）
...

## 缺連結（N 筆）
...

## 過時筆記（N 筆）
...

## 格式不符（N 筆）
...

## 矛盾偵測（N 筆）
...

## Draft 清理提醒（N 筆）
...

## Memory 健康檢查（N 筆）
...

---
以上問題不會自動修正，請告知哪些需要處理。
```

若 research/ 超過 30 篇，在分隔線前加上筆記量門檻提醒。

---

## 注意事項

- `_index.md`、`_register.md`、`.gitkeep`、`daily-notes/` 不列入一般掃描範圍
- `archive/` 存的是發布稿件，格式規範不同，不檢查 research/ 標準欄位
- `personal/` 完全排除，不掃描任何項目
- `draft/` 僅參與第 7 類清理提醒，不參與格式、連結等檢查
- 每次執行後不寫回任何檔案，純回報
