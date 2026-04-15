# 跨部門同事使用指引

這個 repo 是《數位時代》編輯部的 Claude Code Skills 實作範例。你不在編輯部？這份文件回答三個問題：

1. [我能直接用哪些 Skill？](#q1-我能直接用哪些-skill)
2. [我想做類似的 Skill 給我部門用，怎麼開始？](#q2-我想做類似的-skill-給我部門用怎麼開始)
3. [我想直接改編輯部的 Skill 給自己用，要改哪？](#q3-我想直接改編輯部的-skill-給自己用要改哪)

有問題找 Terry（內部通訊錄或信件）。

---

## Q1. 我能直接用哪些 Skill？

標註 `[通用]` 的三支可直接下載使用，無特殊環境依賴：

| Skill | 用途 | 使用情境 |
|-------|------|----------|
| `article-checker` | 錯字、邏輯、事實查核 + 貨幣換算 | 任何對外文件（新聞稿、提案、公關稿、白皮書）送出前的最後一關 |
| `headline-generator` | 多角度標題／文案生成，含 SEO 推薦 | 社群貼文、EDM、落地頁、活動名稱 |
| `retrospective` | 工作覆盤：掃描對話整理偏好與教訓 | 專案結束、完成一份大案後，把學到的東西存下來 |

**安裝**：只下載這三支檔案放到 `~/.claude/agents/`，或整個 clone 後忽略編輯部的 Skills。

```bash
# 方法一：只取這三支
curl -O https://raw.githubusercontent.com/terrylee-lang/BN_next/main/article-checker.md
curl -O https://raw.githubusercontent.com/terrylee-lang/BN_next/main/headline-generator.md
curl -O https://raw.githubusercontent.com/terrylee-lang/BN_next/main/retrospective.md
mv *.md ~/.claude/agents/

# 方法二：全部 clone，不影響——Claude Code 只在被觸發時才讀對應 skill
git clone https://github.com/terrylee-lang/BN_next.git ~/.claude/agents
```

**注意**：`article-checker` 與 `headline-generator` 預設會讀 `bwt-style-guide.md`（《數位時代》風格規範）。你有兩個選擇：

1. **直接用**：編輯部的規範包含基本錯字、用語、標點，對其他部門大致也適用
2. **換成你部門的規範**：替換 `bwt-style-guide.md` 內容為你部門的用語表與禁用詞彙

---

## Q2. 我想做類似的 Skill 給我部門用，怎麼開始？

Skill 的本質是「把你腦中的 checklist 寫成 Claude 可以執行的指示」。參考編輯部的流程：

### 起手流程

1. **找一個你每週重複做 3 次以上的任務**
   - 不是做一次就丟的專案，是高頻率、有固定規則的工作
   - 範例：業務的「每週競品動態整理」、行銷的「活動文案檢查」、公關的「新聞稿 Q&A 準備」

2. **寫下現在怎麼做的 6-10 個步驟**
   - 用口語寫就好：「我會先看 A、確認 B、如果是 C 就做 D，否則做 E」
   - 包括「什麼情況下要停下來問人」「什麼情況下有例外」

3. **抄一支最像的 Skill 當模板**
   - 流程類：參考 `article-checker.md`（單一檔案輸入 → 多層檢查 → 輸出報告）
   - 生成類：參考 `headline-generator.md`（給主題 → 生成多個選項 → 推薦）
   - 多階段類：參考 `news-daily.md`（讀取 → 查核 → 格式化 → 輸出）

4. **替換成你部門的內容**
   - 角色定位：你是 XX 部門的 XX 專員
   - 風格規範：連結到你部門自己的 style guide
   - Input / Output 格式：換成你部門的表單、範本、報告格式
   - 禁止詞彙：你部門對外文件不能出現的用語

5. **測試 5-10 次，每次不順手就改 Skill**
   - 第一版一定不完美，把「不該這樣」的狀況寫進 Skill 的硬性限制

### Skill 檔案基本骨架

```markdown
---
name: 你的-skill-名
description: |
  [一段話描述 Skill 用途與觸發時機]

  請在以下情境下主動使用此 Skill：
  - 使用者說「XXX」
  - 使用者貼上 YYY
---

# [Skill 名] — [你的部門] 版

## 前置作業
（如有需要讀取的共用規範檔，在這裡寫）

## 角色定位
你是一位任職於 XX 部門的資深 XX 專員。你的專長是...

## 執行步驟

### Step 1：[做什麼]
...

### Step 2：[做什麼]
...

## 輸出規格
- 格式：
- 長度：
- 必備欄位：

## 禁止出現的寫法
| 類型 | 例子 | 上限 |
|------|------|------|
| ... | ... | 全文 0 次 |

## 出稿前自查清單
1. ...
2. ...
```

---

## Q3. 我想直接改編輯部的 Skill 給自己用，要改哪？

如果你看中某支 `[編輯部]` Skill 想挪用，以下是它們的硬依賴點：

### `news-daily` / `deep-analysis` / `draft-polish` / `tutorial-article` / `social-post`

需要調整：
- **風格規範檔**：`bwt-style-guide.md` 改成你部門的
- **署名格式**：搜尋 `本文初稿為AI編撰，整理．編輯/` 之類的字串，改成你部門的格式
- **禁止詞彙**：每支 Skill 裡的「禁止出現的寫法」表格，依你部門需求增刪
- **字數規格**：編輯部的字數與格式是為《數位時代》讀者設計，調整為你部門對外文件的適當長度

### `notion-orchestrator`

重度綁定《數位時代》Notion 資料庫「AI產稿中心 v2」。要挪用必須：
- 新建自己部門的 Notion 資料庫，依 Skill 文件重建欄位結構
- 修改 Skill 內的資料庫 ID（搜尋 `a438232ce0a94fe6b70d9f2e9199a32a`）
- 依你部門的稿件類型（不是 `即時新聞`／`深度分析`）調整 Step 2a 的派發邏輯

### `lint-vault`

綁定 Terry 個人 Obsidian vault 架構（inbox / research / draft 等資料夾名稱）。除非你也用 Obsidian 且採類似架構，否則這支不建議挪用，直接參考概念自建比較快。

### `bwt-style-guide`

這是《數位時代》的用語表與禁用詞彙。其他部門建議整個替換為自己的 style guide，保留同樣的檔名與位置（`~/.claude/agents/bwt-style-guide.md`），其他 Skills 就能無縫使用。

---

## 其他資源

- 想看編輯部怎麼把 inbox 素材流程化：參考主 repo 的 [README](./README.md) 與 `bn-claude-code-init.md`
- 想看 Terry 的 Obsidian vault 設計：CLAUDE.md（非公開，找 Terry 索取範例）
- Claude Code 官方文件：[docs.claude.com/en/docs/claude-code](https://docs.claude.com/en/docs/claude-code)
