# BN Claude Code 初始化指南

請依照以下步驟，幫我建立《數位時代》編輯工作環境。

---

## Step 1：確認基本資訊

請先問我以下問題，再開始設定：
1. 你的名字（中文，用於署名與 CLAUDE.md）
2. 你希望工作資料夾建在哪裡？（預設：~/Claude Project/）
3. 你有沒有在用 Obsidian？（有 → 建 vault 結構；沒有 → 只建基本資料夾）

---

## Step 2：安裝 Skills

從 GitHub 下載《數位時代》共用的產稿 Skills。

如果 `~/.claude/agents/` 資料夾不存在，直接 clone：

```bash
git clone https://github.com/terrylee-lang/BN_next.git ~/.claude/agents
```

如果 `~/.claude/agents/` 已存在（裡面有使用者自己的檔案），改用複製方式：

```bash
git clone https://github.com/terrylee-lang/BN_next.git /tmp/bn-skills
cp /tmp/bn-skills/*.md ~/.claude/agents/
rm -rf /tmp/bn-skills
```

安裝完成後，確認 `~/.claude/agents/` 內有以下檔案：
- `bwt-style-guide.md`（共用風格規範）
- `news-daily.md`（即時新聞編譯）
- `deep-analysis.md`（深度分析長文）
- `draft-polish.md`（潤稿去 AI 味）
- `tutorial-article.md`（教學型文章）
- `social-post.md`（社群貼文生成）
- `article-checker.md`（文章查核）
- `headline-generator.md`（標題生成）
- `notion-orchestrator.md`（Notion 產稿主腦）
- `retrospective.md`（工作覆盤）
- `lint-vault.md`（Vault 健康檢查）

列出實際安裝的檔案讓我確認。

---

## Step 3：建立資料夾結構

在工作資料夾內建立以下子資料夾：

```
inbox/        ← Web Clipper 或手動貼入的原始素材
research/     ← 累積的知識庫（公司、產業、技術）
draft/        ← 產稿過程中的草稿與中間版本
daily-notes/  ← 每日工作日誌
archive/      ← 發布後值得收藏的文章
people/       ← 重要人物資料卡
projects/     ← 中長期專案文件
```

同時建立兩個自動維護的索引檔：
- `research/_index.md`：知識庫入口索引
- `draft/_register.md`：草稿狀態追蹤表

如果使用者有 Obsidian，提醒他們將工作資料夾開啟為 vault。

---

## Step 4：生成 CLAUDE.md

在工作資料夾根目錄建立 `CLAUDE.md`，內容需包含以下區塊（請根據 Step 1 的回答填入個人化資訊）：

### 4-1. 使用者背景
```markdown
## 使用者背景
[名字] 是《數位時代》(Business Next) 的科技媒體編輯。主要工作是 AI 輔助產稿，包含即時新聞編譯、深度分析長文、教學文、社群貼文生成與稿件查核。
```

### 4-2. 檔案儲存位置
```markdown
## 檔案儲存位置
所有在本機產生的檔案，預設儲存至 [工作資料夾路徑]。
```

### 4-3. 資料夾結構
將 Step 3 的資料夾結構寫入，說明每個資料夾的用途。

### 4-4. Skills 列表
列出已安裝的 Skills 與觸發方式，參考格式：

```markdown
## 可用 Skills

| Skill | 觸發方式 |
| --- | --- |
| news-daily | 貼外文新聞連結，說「幫我編譯」 |
| deep-analysis | 提供長文或 PDF，說「幫我寫深度分析」 |
| draft-polish | 貼草稿，說「幫我潤稿」 |
| tutorial-article | 貼方法論素材，說「幫我寫教學文」 |
| social-post | 貼文章連結，說「幫我寫社群貼文」 |
| article-checker | 說「幫我查核」、「幫我 fact check」 |
| headline-generator | 說「幫我想標題」、「幫我下標」 |
```

### 4-5. 內容讀取規則
```markdown
## 內容讀取優先順序
1. web_fetch 直接讀取
2. 失敗 → 停止並向使用者索取原文（可用 Dia 瀏覽器萃取 Markdown 貼入）
3. X / Twitter 連結 → 直接告知無法讀取，請使用者貼入文字
4. 不使用 Playwright 作為內容讀取備援
```

### 4-6. 寫作風格規範
```markdown
## 寫作風格規範
所有產稿 Skill 啟動前會讀取 `~/.claude/agents/bwt-style-guide.md`，內含兩岸用語轉換表、人名公司名規範、禁止詞彙表、署名格式。
```

### 4-7. 注意事項
```markdown
## 注意事項
- 匯率換算必須即時搜尋，不使用靜態匯率
- 人名若無法查核中文譯名，保留英文原名並標記「人名待確認」
- 來源無法讀取時，立即回報，不要自行替換素材繼續產稿
```

### 4-8. Obsidian 格式規範（如果使用者有 Obsidian）
```markdown
## Obsidian 格式規範
1. 內部連結一律使用 `[[Note Title]]`
2. 標題層級：H1 標題、H2 小節、H3 以下細項
3. 有關聯的筆記在底部加 Related 區塊
4. 專案名、人名、公司名若有對應筆記，自動加 `[[]]` 連結
```

---

## Step 5：驗證

設定完成後，執行以下驗證：
1. 列出 `~/.claude/agents/` 所有 .md 檔案
2. 列出工作資料夾的完整目錄結構
3. 顯示 CLAUDE.md 的完整內容

讓使用者確認一切正確。

---

## 完成後提示

告訴使用者：
- 現在可以試試貼一個英文新聞連結，說「幫我編譯」
- Skills 會由團隊統一更新，如果是用 git clone 安裝的，執行 `git -C ~/.claude/agents pull` 即可更新
- 如果想讓 Claude Code 記住個人偏好，直接告訴它「記住這個」，它會自動建立記憶
- 進階設定（research 筆記格式、稿件潛力評分、自動交叉檢查、Wiki Ingest 規則等）可參考 Terry 的 CLAUDE.md：`~/Claude Project/CLAUDE.md`
- 有問題找 Terry
