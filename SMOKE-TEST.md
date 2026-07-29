# 安裝驗證 SMOKE TEST

安裝完成後跑這一輪，確認「裝完真的能動」，而不是「檔案真的存在」。日後環境出怪事、或 `git pull` 之後懷疑壞了，也可以單獨重跑。

**給 Claude 的執行指示**：這份檔案是給你（Claude Code）讀的。依序執行 T1 到 T11，每項照「動作 → 預期 → 失敗怎麼修」三段處理：動作照做、拿實際輸出比對預期、不符就依「失敗怎麼修」修一次再重測。**不要為了讓測試通過而修改 Skill 或規範檔內容**——規範檔若真有 bug，記錄下來回報，不要就地改。全部跑完輸出最後一節的回報表。

**必過分級**：

- T1-T5 **必過**：這是環境基本盤，任一項失敗就別開始產稿
- T6-T8 依用途：不跑 Notion 產稿流程可跳過 T6、T7；不用後台撈稿可跳過 T8
- T9 只在要做視覺配件時必須過；T10、T11 建議都跑一次

---

## T1. 規範檔與 Skill 檔完整性

**動作**

```bash
ls ~/.claude/agents/*.md | wc -l
ls ~/.claude/agents/bwt-*.md
for f in ~/.claude/agents/*.md; do head -1 "$f" | grep -q '^---$' && basename "$f"; done | wc -l
```

**預期**

- 第一行 ≥ **24**
- `bwt-*` 共 **5 份**：`bwt-style-guide.md`、`bwt-design-standard.md`、`bwt-visual-checklist.md`、`bwt-iframe-visual-component.md`、`bwt-html-table-component.md`
  （常被誤以為有第 6 份：`bwt-voice-reference.md` 是個人風格指紋檔，**不在共享 repo**，缺少是正常的，見 T5）
- 第三行 = **13**（有 frontmatter 的 Skill 檔：news-daily、deep-analysis、draft-polish、tutorial-article、social-post、etf-explainer、article-checker、headline-generator、notion-orchestrator、visual-brief、visual-asset、lint-vault、retrospective；再加上你自建的 agent 會更多）

**失敗怎麼修**

- 數量不足 → `git -C ~/.claude/agents pull`；仍不足表示 clone 沒完成，重跑安裝指南 Step 2
- 目錄不是 git repo（`git -C ~/.claude/agents status` 報錯）→ 曾用 `cp` 手動複製，日後無法更新。把目錄改名備份後重新 clone，再把自己的檔案搬回去

---

## T2. Skill 工具可呼叫（挑 news-daily）

**動作**

```bash
ls ~/.claude/skills/
```

然後在**新開的 session** 中，用 Skill 工具呼叫 `news-daily`，指令寫：「只回報你啟動時會讀哪些規範檔、以及 Step 1 要做什麼，不要產稿。」

**預期**

- `~/.claude/skills/` 下有 7 個目錄（news-daily、deep-analysis、draft-polish、tutorial-article、social-post、article-checker、notion-orchestrator），每個內含 `SKILL.md`
- session 開頭注入的 skill 清單看得到 news-daily 等名稱
- Skill 回報「會讀 `bwt-style-guide.md`」，並成功讀到內容（無 file not found）

**失敗怎麼修**

- Skill 清單沒有這些名稱 → `~/.claude/skills/` 的 stub 沒建，重跑安裝指南 Step 2b
- stub 存在但 Skill 讀不到本體 → 打開 `~/.claude/skills/news-daily/SKILL.md`，確認內文的 Read 路徑指向**你自己的** home 下 `.claude/agents/news-daily.md`（stub 內路徑是絕對路徑，複製別人的檔案會指到別人家）
- 名稱正確但工具說不存在 → 重啟 session 讓清單重新載入；同一個工具不要重試第三次，改用 Agent 工具（`subagent_type: news-daily`）

---

## T3. Agent 工具可呼叫（挑 visual-asset）

**動作**

用 Agent 工具、`subagent_type: visual-asset`，prompt 寫：「只做 Step 0 產品類型判斷：我要一張文章封面用的 16:9 圖卡。說明你會讀哪些規範檔、輸出規格是什麼，然後停，不要動手做。」

**預期**

- 子代理回報：判定為「16:9 主視覺圖卡」，會讀 `bwt-visual-checklist.md`（§三 16:9 SOP）與 `bwt-design-standard.md`，輸出規格 1920×1080 PNG
- 沒有出現 "agent type not found"

**失敗怎麼修**

- agent type not found → 確認 `~/.claude/agents/visual-asset.md` 存在且開頭 frontmatter 有 `name: visual-asset`；重啟 session
- 子代理說找不到 `bwt-visual-checklist.md` → 回頭做 T1
- 子代理直接開始產圖 → 不是環境問題，是 prompt 太鬆，重下一次；但要記下來：這支 Skill 的 Step 0 判斷可能不夠強制

---

## T4. 個人 CLAUDE.md 已生效、無佔位符殘留

**動作**

```bash
test -f ~/.claude/CLAUDE.md && echo OK
rg -n "<你的|terrylee-lang|/Users/" ~/.claude/CLAUDE.md
```

然後在新 session 直接問：「我的 vault 在哪個路徑？產稿完成後一定要做什麼？」

**預期**

- 第一行 `OK`
- 第二個指令**沒有任何命中**（佔位符全部填掉、沒有別人的 GitHub 帳號、沒有寫死別人的家目錄）
- Claude 答得出你的 vault 路徑，以及「產稿完成一律跑一輪 article-checker」

**失敗怎麼修**

- 檔案不存在 → 重跑安裝指南 Step 3（從 `CLAUDE-template.md` 產生）
- 有 `<你的…>` 殘留 → 逐一填掉。留著不會報錯，但 Claude 會把佔位符當真實路徑用
- 出現 `terrylee-lang` → 改成你自己的 GitHub 帳號（見安裝指南 Step 6）
- Claude 答不出來 → 確認檔案在 `~/.claude/CLAUDE.md`，不是 `~/.claude/agents/CLAUDE.md`

---

## T5. 個人風格指紋檔缺席時，產稿 Skill 不報錯

**動作**

```bash
ls ~/.claude/*voice-reference*.md 2>/dev/null || echo "無個人風格檔（正常）"
rg -n "voice-reference" ~/.claude/agents/*.md
```

再跑一次 T2 的 news-daily 起手（只回報要讀什麼、不產稿）。

**預期**

- 第一行印出「無個人風格檔（正常）」
- `rg` 命中的每一處都是**條件句或說明句**（「若存在則必讀」、「不必讀取」、「Terry 個人，不進共享 repo」），沒有任何一處是無條件的「去讀這個檔」
- Skill 照常執行，最多說一句「無個人風格檔，略過」，**不得**因為缺檔中斷

**失敗怎麼修**

- Skill 因缺檔停住 → 用 `rg -n "voice-reference" ~/.claude/agents/<那支 skill>.md` 找到該行，確認它是條件句；若真的寫成硬性依賴，記錄檔名與行號回報 Terry（這是 repo bug，不要自己建一個空的 voice-reference 檔充數）

---

## T6. Notion MCP 連線與資料庫可讀

**動作**

先確認 `notion-orchestrator.md` 內的資料庫 URL 已換成你自己的（`rg -n "資料庫 URL" ~/.claude/agents/notion-orchestrator.md`），再用 Notion MCP 的 `notion-fetch`，**參數名只有 `id`**，值放該網址。

**預期**

- 工具名稱前綴是 `mcp__claude_ai_Notion__`
- 回傳資料庫結構，屬性含「稿件類型」、「負責人」、「狀態」、「來源 1」
- 「負責人」下拉選項裡有你的名字

**失敗怎麼修**

- 報 validation error → 你可能傳了 `url` 或 `page_id`；參數名固定是 `id`
- 報 not connected → 到 claude.ai → Settings → Connectors 重新授權 Notion（非互動 session 無法完成 OAuth，要在瀏覽器做）
- 工具前綴不是 `mcp__claude_ai_Notion__`（例如 `mcp__notion__`）→ 你是用 `claude mcp add` 自建的 server。notion-orchestrator 寫死前綴會呼叫失敗：移除自建 server，改走 claude.ai 連接器
- 回 404／no access → 你的 Notion 帳號沒有這個資料庫的權限，請 Terry 分享給你
- 「負責人」沒有你的名字 → 請 Terry 在資料庫加上該下拉選項，否則 `跑 [你的名字] 的稿件` 篩不到東西

---

## T7. NOTION_TOKEN 有效（批次掃描路徑）

**動作**

照 `notion-orchestrator.md` Step 1 的 curl 範例，跑一次「狀態 = 未開始」查詢。**必須用 inline command substitution 把 token 帶進 `-H`**（`$(jq -r '.env.NOTION_TOKEN' ~/.claude/settings.local.json)`），禁止 `export`、`echo $NOTION_TOKEN`、寫入暫存檔、`bash -x`。

**預期**

- HTTP 200，回傳 JSON 內有 `"results": [...]`（陣列可以是空的，代表目前沒有待辦，不算失敗）

**失敗怎麼修**

- `401 unauthorized` → `~/.claude/settings.local.json` 的 `env.NOTION_TOKEN` 沒填或填錯，重看安裝指南 Step 5
- `object_not_found` → token 有效但 integration 沒被邀請進資料庫，請 Terry 到資料庫 → Connections 加上
- `jq: command not found` → `brew install jq`，再在 `~/.local/bin` 建 symlink（Claude Code 的 Bash PATH 不含 `/opt/homebrew/bin`）
- 401 而你用的是 `NOTION_TOKEN=$(...) curl -H "...$NOTION_TOKEN..."` → 這個寫法在 zsh/bash 永遠展開成空字串（已知坑），改成 inline substitution
- JSON 解析錯誤 → 你可能手改 `settings.local.json` 改壞了，用 `python3 -m json.tool ~/.claude/settings.local.json` 驗語法

---

## T8. bnext-service MCP（Pluto 後台）可用

**動作**

用 `mcp__bnext-service__bnext-article` 查最近 3 天的《數位時代》文章（`published_at_start`／`published_at_end`）。

**⚠️ 這一項只做讀取**。`content-pool-add`／`-update` 會寫進正式後台，不可作為 smoke test。

**預期**

- 回傳文章清單，每筆含標題與 `pv`（pageview）

**失敗怎麼修**

- server 顯示 needs authentication → 在**互動式** session 執行 `/mcp` 完成 OAuth（用公司 Google 帳號登入）；非互動環境無法授權
- server 不存在 → 重跑安裝指南 Step 5 的 `claude mcp add`
- OAuth 通了但工具回權限錯誤 → 請 Terry 確認你的公司帳號已被加入 Pluto MCP 的授權名單

---

## T9. 配件托管 URL 可訪問

**動作**

```bash
ACC=<你的 GitHub 帳號>
curl -s -o /dev/null -w "index:%{http_code}\n" "https://$ACC.github.io/bnext-visuals/"
curl -s -o /dev/null -w "nojekyll-test:%{http_code}\n" "https://$ACC.github.io/bnext-visuals/_assets/logos/.keep"
```

**預期**

- `index:200`
- `_assets/` 下的檔案不是 404（若你放了測試檔就該回 200）——這一項在驗 `.nojekyll` 生效

**失敗怎麼修**

- index 404 → GitHub Pages 沒開（Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`），或還在 build，等 1-5 分鐘重試
- index 200 但 `_assets/` 下全 404 → repo root 缺 `.nojekyll` 空檔，Jekyll 把 `_` 開頭目錄整個忽略了。補上並 push，等 CDN 刷新
- 改了檔案但看到舊內容 → CDN 快取，URL 後加 `?v=YYYYMMDD` 強制刷新

---

## T10. pdftotext 可用

**動作**

```bash
command -v pdftotext && pdftotext -v 2>&1 | head -1
```

**預期**

- 印出路徑（通常是 `~/.local/bin/pdftotext`）與 poppler 版本

**失敗怎麼修**

- 沒有輸出 → `brew install poppler`，然後 `mkdir -p ~/.local/bin && ln -sf "$(brew --prefix)/bin/pdftotext" ~/.local/bin/pdftotext`（`pdftoppm`、`pdfinfo` 同辦）。**光裝 brew 不夠**：Claude Code 的 Bash 不載入 `~/.zprofile`，PATH 不含 `/opt/homebrew/bin`，一定要建 symlink
- 這一項失敗不擋產稿，只會讓 PDF 素材處理退化成「整份塞進對話」，很燒 token，建議修好

---

## T11. 端到端跑一篇（news-daily → article-checker）

**動作**

貼一則可公開讀取的英文新聞連結（避開付費牆），說「幫我編譯」。產出後說「幫我查核」。

**預期**

- 產出 650-1000 字繁體中文即時稿
- 文末**兩行俱全**：單行、頓號分隔、帶超連結的資料來源；以及署名行 `本文初稿為AI編撰，整理．編輯/ <你的名字>`
- article-checker 跑完給出錯字／邏輯／事實／貨幣換算四類結果，且**修訂後全文仍保留上面那兩行**

**失敗怎麼修**

- 來源讀不到、Claude 停下來向你索取原文 → **這是正確行為，不是失敗**（換一個可讀連結重試）
- 文末兩行不見或被改成 `## 資料來源` 小標＋條列 → 這是已知的多輪修改漂移點，要求依 `bwt-style-guide.md`「署名格式」改回
- 字數嚴重超標 → 確認 Skill 讀到了 `bwt-style-guide.md`「正文字數（全域規則）」，回頭做 T1、T2

---

## 回報格式

跑完輸出這張表，未跑的標「跳過（原因）」：

```
# 安裝 SMOKE TEST 結果｜YYYY-MM-DD

| # | 項目 | 結果 | 備註 |
|---|------|------|------|
| T1 | 規範檔與 Skill 檔完整性 | ✅／❌ | |
| T2 | Skill 工具（news-daily） | | |
| T3 | Agent 工具（visual-asset） | | |
| T4 | 個人 CLAUDE.md 生效 | | |
| T5 | 個人風格檔缺席不報錯 | | |
| T6 | Notion MCP 與資料庫 | | |
| T7 | NOTION_TOKEN 批次查詢 | | |
| T8 | bnext-service MCP | | |
| T9 | 配件托管 URL | | |
| T10 | pdftotext | | |
| T11 | 端到端產稿＋查核 | | |

需要別人處理的事項（Notion 權限、Pluto 授權、負責人選項等）：
- ...
```

T1-T5 全過就可以開始產稿。剩下需要別人動手的權限問題，列在表格下方直接找 Terry。
