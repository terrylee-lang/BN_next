# BN Claude Code 安裝指南（互動式安裝器）

**這份檔案是給 Claude Code 讀的指令，不是給人看的說明書。** 新同事的用法只有一句話：把這個檔案的路徑或內容丟給 Claude Code，說「照這個幫我設定」。

---

## 給 Claude 的執行原則（動手前先讀完）

1. **依序執行 Step 0 → Step 8，不跳步、不平行跑。** 每步結尾輸出一行 `✅ Step N 完成：<檢查點的實際輸出>`；檢查點沒過就停在那一步修，不要帶著錯往下走。
2. **需要人工才能完成的事，明確停下等使用者回覆**：claude.ai 連接器 OAuth、GitHub 網頁操作、向 Terry 索取 token 或權限。不要自己想辦法繞過，也不要假裝完成。
3. **不要求使用者把任何 token 貼進對話**。token 一律由使用者自己用編輯器填進設定檔，你只負責驗證格式與連線。
4. **破壞性動作前先備份並口頭確認**：覆蓋既有 `~/.claude/CLAUDE.md`、動既有 `~/.claude/agents/` 目錄、刪任何檔案。
5. 全程繁體中文回報。安裝過程中若使用者的環境與本檔描述不符（版本、路徑、已裝過一半），先回報差異再提方案，不要靜默套用預設值。
6. 本檔提到的 `<vault>` 一律代換為 Step 1 問到的工作資料夾路徑；`<ACC>` 代換為使用者的 GitHub 帳號。

---

## Step 0：環境需求檢查

| 項目 | 必要性 | 說明 |
|------|--------|------|
| macOS | 必要 | 本指南以 macOS 為準 |
| Claude Code（CLI 或 VSCode extension） | 必要 | 兩者共用 `~/.claude/`，安裝流程完全相同 |
| 已登入 claude.ai 帳號 | 必要 | MCP 連接器要靠它 |
| `git` | 必要 | 安裝與更新共享 repo |
| `jq` | Notion 流程必要 | 讀取 `NOTION_TOKEN` |
| `gh`（GitHub CLI） | 選配 | Step 6 建配件 repo 用；沒有就走網頁 |
| `poppler`（pdftotext） | 選配、建議 | PDF 素材處理 |

**動作**

```bash
sw_vers -productVersion
git --version
command -v jq || echo "MISSING jq"
command -v pdftotext || echo "MISSING pdftotext"
command -v gh || echo "MISSING gh"
```

**缺 `jq` / `poppler` 時**（`gh` 缺可略）：

```bash
brew install jq poppler
mkdir -p ~/.local/bin
for b in jq pdftotext pdftoppm pdfinfo; do ln -sf "$(brew --prefix)/bin/$b" ~/.local/bin/$b; done
```

⚠️ **symlink 不是多餘步驟**：Claude Code 的 Bash 工具不載入 `~/.zprofile`／`~/.zshenv`，PATH 不含 `/opt/homebrew/bin`。**日後用 brew 裝的任何 CLI（ffmpeg、node…）都要在 `~/.local/bin` 建 symlink** 才叫得到。

**檢查點**：`git --version` 有輸出；`jq --version` 與 `pdftotext -v` 有輸出（或使用者明確表示這輪不需要）。

---

## Step 1：互動問答（一次問完，等使用者回覆再動手）

一次列出以下六題，等回答齊全再進 Step 2：

1. **你的中文名字**（用於文末署名與 CLAUDE.md）
2. **工作資料夾（vault）路徑**？直接按 Enter 用預設 `~/Claude Project/`
   → **強烈建議用預設**：共享 repo 內 12 支以上 Skill 內文寫死這個路徑（inbox 素材匹配、bnext-visuals 本地位置）。選別的名字不會報錯，會**靜默失效**。若使用者堅持，Step 3 必須在 CLAUDE.md 寫入「路徑代換規則」那一行。
3. **你的 GitHub 帳號**？（Step 6 建視覺配件托管 repo 用；還沒有帳號就先跳過，之後補做）
4. **要跑 Notion 產稿流程嗎？**（要 → Step 5 需要向 Terry 索取 `NOTION_TOKEN`，並確認你的 Notion 帳號有「AI產稿中心 v2」權限）
5. **有在用 Obsidian 嗎？**（有 → Step 4 額外寫入專案層 CLAUDE.md 並提醒開啟為 vault）
6. **要接 bnext-service（Pluto 後台）嗎？**（用途：撈各刊文章與 pageview、寫入內容池。不確定就先選要，只讀不寫沒有風險）

**檢查點**：把六個答案整理成一張表覆述，請使用者確認「OK」才進 Step 2。

---

## Step 2：安裝共享 repo（Skills ＋ bwt-* 規範）

### 2a. clone 或更新 `~/.claude/agents/`

先判斷現況：

```bash
ls -d ~/.claude/agents 2>/dev/null && git -C ~/.claude/agents remote -v 2>/dev/null
```

依三種情況處理：

- **目錄不存在** → 直接 clone
  ```bash
  git clone https://github.com/terrylee-lang/BN_next.git ~/.claude/agents
  ```
- **已是 git repo 且 remote 指向 BN_next** → 更新
  ```bash
  git -C ~/.claude/agents pull
  ```
- **目錄存在但不是 git repo**（裡面有使用者自己的 agent 檔）→ **不要用 `cp` 把檔案倒進去**（會混進 README／CHANGELOG，而且從此無法 `git pull` 更新）。改為：
  ```bash
  mv ~/.claude/agents ~/.claude/agents.backup-$(date +%Y%m%d)
  git clone https://github.com/terrylee-lang/BN_next.git ~/.claude/agents
  ls ~/.claude/agents.backup-$(date +%Y%m%d)
  ```
  列出備份目錄的檔案，請使用者指認哪些是他自己的，再逐一 `cp` 回 `~/.claude/agents/`（不要整包倒回）。

**檢查點**：以下三行都要符合。

```bash
ls ~/.claude/agents/*.md | wc -l            # 應 ≥ 24
ls ~/.claude/agents/bwt-*.md                # 應為 5 份（見下）
git -C ~/.claude/agents status --short      # 應為空（工作區乾淨）
```

安裝後應存在的檔案：

**共用規範（5 份 bwt-*）**
- `bwt-style-guide.md`（寫作風格規範：兩岸用語、禁用詞、**事實查核鐵律**、來源讀取優先序、署名格式）
- `bwt-visual-checklist.md`（視覺任務入口 checklist：設計優先序、產品類型判斷、16:9 SOP、交付 spot-check）
- `bwt-design-standard.md`（美術製作標準總綱：BN Design System 完整 tokens）
- `bwt-iframe-visual-component.md`（iframe 配件子規範）
- `bwt-html-table-component.md`（HTML 表格元件子規範）

> `bwt-voice-reference.md` 是個人風格指紋檔，**刻意不在共享 repo**，缺少是正常的，任何 Skill 都不會因為它缺席而報錯。

**產稿 Skills**：`news-daily.md`、`deep-analysis.md`、`draft-polish.md`、`tutorial-article.md`、`social-post.md`、`etf-explainer.md`、`article-checker.md`、`headline-generator.md`、`notion-orchestrator.md`

**視覺 Skills**：`visual-brief.md`、`visual-asset.md`

**工具型 Skills**：`retrospective.md`、`lint-vault.md`

**交接文件**：`README.md`、`CHANGELOG.md`、`ADAPTATION.md`（跨部門改造指引）、`CLAUDE-template.md`、`SMOKE-TEST.md`、本檔

### 2b. 生成 Skill 工具 stub（`~/.claude/skills/`）

`~/.claude/agents/` 的檔案預設只能用 **Agent 工具（`subagent_type`）** 呼叫。要讓幾支高頻 Skill 也能被 **Skill 工具**直接呼叫（使用者說「幫我編譯」時觸發更順），需要在 `~/.claude/skills/<名稱>/SKILL.md` 放一個薄 stub 指回本體。**兩種機制搞混是歷史上最常見的錯誤（累計 17 次），所以這一步要做，讓環境符合 CLAUDE.md 裡的兩張名單。**

為以下 7 支各建一個 stub：`news-daily`、`deep-analysis`、`draft-polish`、`tutorial-article`、`social-post`、`article-checker`、`notion-orchestrator`。

做法：先 `echo $HOME` 取得絕對家目錄（stub 內要寫絕對路徑，不要寫 `~`），再 `Read` 每支 agent 檔開頭取得 frontmatter 的 `description`，然後 Write 出 stub：

```markdown
---
name: <skill 名>
description: <從 agent 檔 frontmatter 抄來的描述，含觸發語>
version: 1.0.0
---

使用 Read 工具讀取完整 Skill 定義：`<$HOME 的絕對路徑>/.claude/agents/<skill 名>.md`，並嚴格按照其中的執行流程處理。

輸入素材（如有）：$ARGUMENTS
```

`notion-orchestrator` 的 stub **額外加一行** frontmatter `disable-model-invocation: true`（它會批次寫回 Notion，不該被模型自動觸發，只由使用者明確呼叫）。

**檢查點**：`ls ~/.claude/skills/ | wc -l` = 7（若使用者原本已有其他 skills 則為 7 + 原有數量）；且提醒使用者「Skill 清單要重開 session 才會重新載入」。

---

## Step 3：從範本產生個人 `~/.claude/CLAUDE.md`

`~/.claude/CLAUDE.md` 是**個人檔、不在共享 repo**，但沒有它，Claude 不知道兩種呼叫機制、Notion 參數小抄、付費牆政策——這是新同事「能不能正確觸發 Skills」的分水嶺。

**動作**

1. `Read ~/.claude/agents/CLAUDE-template.md`
2. 產生新內容：
   - **刪掉開頭的範本說明區塊**（第一行到 `---` `---` 雙分隔線為止）
   - **刪掉所有以 `> 範本註記`／`> 範本註記：` 開頭的引言行**
   - 代入 Step 1 的答案：`<你的名字>`、`<你的 vault 路徑>`、`<你的 GitHub 帳號>`
   - 使用者**不跑 Notion 流程** → 刪掉「MCP 整合 → Notion」整段與觸發語表的 `notion-orchestrator` 那列
   - 使用者**不接 bnext-service** → 刪掉該段
   - 使用者**沒有個人風格指紋檔** → 刪掉「個人風格指紋（選配）」那一條
   - vault 路徑**不是** `~/Claude Project/` → 保留「路徑代換規則」那一行
   - **附錄（專案層 CLAUDE.md 最小範本）不寫進 `~/.claude/CLAUDE.md`**，留到 Step 4 用
3. 若 `~/.claude/CLAUDE.md` 已存在 → 先 `cp ~/.claude/CLAUDE.md ~/.claude/CLAUDE.md.bak-$(date +%Y%m%d)`，把舊檔中屬於使用者自己的規則整理出來，**展示合併前後對照給使用者確認**，同意後才寫入。
4. Write `~/.claude/CLAUDE.md`

**檢查點**

```bash
test -f ~/.claude/CLAUDE.md && wc -c ~/.claude/CLAUDE.md
rg -n "<你的|範本註記|terrylee-lang" ~/.claude/CLAUDE.md   # 應 0 命中
```

---

## Step 4：建立 vault 資料夾結構

**動作**（`<vault>` = Step 1 的路徑）

```bash
mkdir -p <vault>/{inbox,research,draft,daily-notes,archive,people,projects,personal,pic}
mkdir -p <vault>/projects/bnext-visuals
```

| 資料夾 | 用途 |
|--------|------|
| `inbox/` | Web Clipper 或手動貼入的原始素材 |
| `research/` | 累積的知識庫（公司、產業、技術） |
| `draft/` | 產稿過程中的草稿與中間版本 |
| `daily-notes/` | 每日工作日誌（`YYYY-MM-DD.md`） |
| `archive/` | 發布後值得收藏的文章 |
| `people/` | 重要人物資料卡 |
| `projects/` | 中長期專案文件（含 `bnext-visuals/` 配件本地副本） |
| `personal/` | 私人內容（不參與自動寫回、不被 lint-vault 掃描） |
| `pic/` | 圖片素材 |

再建兩個自動維護的索引檔（不存在才建，已存在不要覆蓋）：

- `<vault>/research/_index.md` — H1 `# 知識庫索引` ＋ 一行說明「Claude 每次新增或更新 research 筆記後自動維護」
- `<vault>/draft/_register.md` — H1 `# Draft 狀態追蹤表` ＋ 表頭 `| 檔名 | 類型 | 狀態 | 備註（≤100 字） |`，並註明「這是追蹤表不是 changelog，全檔目標 ≤ 5,000 bytes」

**使用者有 Obsidian** → 把 `CLAUDE-template.md` 附錄的專案層範本寫入 `<vault>/CLAUDE.md`（代入實際路徑），並提醒他在 Obsidian 用「Open folder as vault」開啟 `<vault>`。

**檢查點**

```bash
ls -a <vault>
test -f <vault>/research/_index.md && test -f <vault>/draft/_register.md && echo "索引檔 OK"
```

---

## Step 5：MCP 連接

> **只用 VSCode extension 的人**：5c、5d 的 `claude mcp add` 與 `/mcp` 需要 Claude Code CLI。先確認 `command -v claude` 有輸出；沒有就請使用者安裝 CLI（`npm i -g @anthropic-ai/claude-code`）後在終端機執行，設定寫進共用的 `~/.claude/`，extension 端重開即生效。5a 的連接器授權在 claude.ai 網頁做，兩種環境相同。

### 5a. Notion 連接器（跑 Notion 流程才需要）

**必須走 claude.ai 連接器，不要用 `claude mcp add` 自建 Notion server。** `notion-orchestrator.md` 內文寫死工具前綴 `mcp__claude_ai_Notion__*`，自建 server 前綴不同會全部呼叫失敗。

請使用者到 claude.ai → Settings → Connectors → 連接 Notion 並授權；**停下等他回報完成**。

**檢查點**：呼叫 `notion-fetch`（參數名只有 `id`，值放 `notion-orchestrator.md`「資料庫資訊」裡的資料庫網址），應回傳含「稿件類型」「負責人」「狀態」的結構。
- 回 404／no access → 使用者的 Notion 帳號沒有這個資料庫權限，**請他找 Terry 分享**，並順便確認「負責人」下拉有他的名字（否則 `跑 [名字] 的稿件` 永遠篩不到）。

### 5b. `NOTION_TOKEN`（批次掃描用）

`notion-orchestrator` Step 1 的批次篩選走 Notion HTTP API（MCP 不支援結構化 filter），需要公司內部整合金鑰。

1. 請使用者**向 Terry 索取 token**（公司內部整合金鑰，可全體同仁共用，禁止外流）。
2. **不要請使用者把 token 貼進對話。** 指示他自己編輯 `~/.claude/settings.local.json`，在 `env` 區塊加入：
   ```json
   {
     "env": {
       "NOTION_TOKEN": "貼在這裡"
     }
   }
   ```
   （檔案已存在就只加這個 key，不要整檔覆蓋。）
3. 驗證，**不要 echo token 本體**：
   ```bash
   python3 -m json.tool ~/.claude/settings.local.json > /dev/null && echo "JSON 語法 OK"
   jq -r '.env.NOTION_TOKEN | length' ~/.claude/settings.local.json   # 印長度即可
   ```

**檢查點**：執行 `SMOKE-TEST.md` 的 T7（照 notion-orchestrator Step 1 範例跑一次「未開始」查詢，**inline command substitution**，禁止 `export`／`echo $NOTION_TOKEN`／寫暫存檔），回 200 且有 `results` 陣列。

### 5c. bnext-service（Pluto 後台 CMS）

```bash
claude mcp add --transport http bnext-service https://mcp-service.bnextmedia.com.tw/mcp -s user
```

OAuth 走集團 Google auth gateway，需在**互動式** session 執行 `/mcp` 完成授權（用公司 Google 帳號登入）。非互動環境無法授權，要請使用者自己做。

⚠️ 告知使用者：`content-pool-add`／`-update` 寫的是**正式後台**。只在稿件定稿、且明確下令時才推；絕不自動推、不塞進 notion-orchestrator 自動流程。

### 5d. Playwright（選配）

```bash
claude mcp add playwright npx @playwright/mcp@latest
```

僅用於需互動操作的頁面與量 iframe 配件高度，**不作為內容讀取備援**。

**Step 5 檢查點**

```bash
claude mcp list
```

需要的 server 都顯示 `✓ Connected`；顯示 `Needs authentication` 的，回報給使用者並停下等他在互動 session 授權。

---

## Step 6：建立自己的視覺配件托管 repo

**為什麼各自託管**：iframe 網址會**永久嵌在已發布文章裡**，不該綁在別人的個人 GitHub 帳號下（對方帳號改名、repo 刪除、權限調整，線上文章就開天窗）。所以每個人建自己的 `bnext-visuals`，不共用 Terry 的。

### 6a. 建本地骨架

```bash
cd <vault>/projects/bnext-visuals
mkdir -p 2026 _assets/logos _assets/heroes
touch .nojekyll _assets/logos/.keep _assets/heroes/.keep
```

- `.nojekyll`（**必須，第一個 commit 就要有**）：沒有它，GitHub Pages 的 Jekyll 會忽略所有 `_` 開頭目錄，`_assets/` 內的自托管 logo 與 CSS 全部 404
- `2026/`：**年份資料夾**，配件檔名 `<MM-主題-kebab-case>.html`（例 `2026/07-tsmc-fab-map.html`；英文 kebab-case，避免中文 URL 編碼問題）
- `_assets/logos/`：Google s2 Favicon API 取不到或解析度太低時的自托管 SVG
- `_assets/heroes/`：主視覺／封面用圖檔素材

再建兩個檔：
- `index.html`：配件總覽頁，一個 `.grid` 容器 + 每個配件一張 `.card`（連到該配件 html）。樣式依 `bwt-design-standard.md` 的 tokens，不要自創色票。
- `README.md`：配件清單表格（`| 日期 | 配件 | 對應文章 | 網址 |`），每次上線新配件加一行。

### 6b. 建遠端 repo 並開 GitHub Pages

repo 名建議就叫 **`bnext-visuals`**（與規範檔內的路徑一致，之後對照文件不用轉換）。**必須是 public**，Pages 才能公開存取。

有 `gh`：

```bash
cd <vault>/projects/bnext-visuals
git init && git add -A && git commit -m "init: bnext-visuals 骨架（.nojekyll、2026/、_assets/）"
git branch -M main
gh repo create <ACC>/bnext-visuals --public --source=. --push
gh api -X POST repos/<ACC>/bnext-visuals/pages -f "source[branch]=main" -f "source[path]=/" 2>/dev/null || echo "改用網頁開 Pages"
```

沒有 `gh`：請使用者到 github.com 新建 public repo `bnext-visuals`（不要加 README，避免衝突），然後：

```bash
cd <vault>/projects/bnext-visuals
git init && git add -A && git commit -m "init: bnext-visuals 骨架"
git branch -M main
git remote add origin https://github.com/<ACC>/bnext-visuals.git
git push -u origin main
```

再到 repo → Settings → Pages → Source: **Deploy from a branch** → Branch: **main** / **/ (root)** → Save。

### 6c. 把帳號換成自己的

`visual-asset.md` 與 `bwt-iframe-visual-component.md` 內寫的是 `terrylee-lang/bnext-visuals`。**首選做法：不要動這兩個檔**（本機改了以後每次 `git pull` 都會衝突），改為靠 Step 3 已寫進 `~/.claude/CLAUDE.md`「視覺配件托管」的覆寫規則生效。

確認那一節已寫明：你的 repo 是 `<ACC>/bnext-visuals`、Pages 網址 `https://<ACC>.github.io/bnext-visuals/`、本機副本在 `<vault>/projects/bnext-visuals/`，且「規範檔內出現的 `terrylee-lang` 一律代換為自己的帳號」。

若使用者仍偏好直接改檔（例如常常忘記），告知代價：`git pull` 會衝突、要自己 resolve；且**不可** push 這個改動回 BN_next（那是全編輯部共用的）。

**檢查點**（Pages 首次 build 要等 1-5 分鐘）

```bash
curl -s -o /dev/null -w "%{http_code}\n" "https://<ACC>.github.io/bnext-visuals/"   # 應為 200
rg -n "terrylee-lang" ~/.claude/CLAUDE.md                                            # 應 0 命中
```

⚠️ 提醒使用者：這是 public repo，**禁止放未發布稿件、API key、客戶資料、內部財報數據**，每次上傳前自查。

---

## Step 7：跑安裝驗證

`Read ~/.claude/agents/SMOKE-TEST.md`，依序執行 T1–T11，輸出該檔末尾的回報表。

- T1–T5 **必須全過**（環境基本盤）。任一項失敗 → 回到對應 Step 修，不要跳過。
- T6、T7 依 Step 1 第 4 題；T8 依第 6 題；T9 依是否已完成 Step 6。
- 因權限／授權卡住的項目（Notion 資料庫分享、負責人選項、Pluto 授權），列進「需要別人處理的事項」，不算安裝失敗。

**檢查點**：回報表存在，且 T1–T5 全為 ✅。

---

## Step 8：完成回報與後續維護

輸出給使用者：

1. **裝了什麼**：`~/.claude/agents/`（Skills ＋ 規範，共享）、`~/.claude/skills/`（Skill 工具 stub）、`~/.claude/CLAUDE.md`（個人設定）、`<vault>/`（工作資料夾）、`<ACC>/bnext-visuals`（配件托管）
2. **第一件事**：貼一則英文新聞連結說「幫我編譯」，產出後說「幫我查核」
3. **更新 Skills**：`git -C ~/.claude/agents pull`（團隊統一更新；本機若改過 repo 內檔案會衝突，改動請先跟 Terry 討論再推回 repo——那是全編輯部共用的）
4. **個人偏好**：直接跟 Claude 說「記住這個」，它會寫入記憶；你自己的規則寫進 `~/.claude/CLAUDE.md`（不會被 `git pull` 覆蓋）
5. **進階制度檔為個人層、不隨 repo 交接**（派工 playbook、判斷 rubric、每日收工 SOP、memory 政策、research 筆記格式範本）——先把基本流程跑順，需要時再向 Terry 索取
6. **需要找 Terry 的事**（把實際卡住的項目列出來）：
   - Notion「AI產稿中心 v2」資料庫存取權，以及「負責人」下拉加上你的名字
   - `NOTION_TOKEN`（公司內部整合金鑰）
   - bnext-service（Pluto）MCP 的帳號授權
   - `bwt-lint.sh`（選配的機械檢查腳本，`article-checker` Step 0 有它才跑；沒有會自動降級，不影響查核）
