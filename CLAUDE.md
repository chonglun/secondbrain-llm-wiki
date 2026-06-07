# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

# 知識庫維護協議

> 這份檔案是整個系統的設定核心。任何 AI agent(Claude Code、Codex、或本機透過 Ollama / LM Studio 的模型)在這個 vault 工作時,都必須先讀這份協議,再開始任何操作。
> 它的作用是把 AI 從「聊天對象」變成一個**有紀律的 wiki 維護者**。

## 快速指令 Quick Reference

```bash
# 查看最近 5 筆操作紀錄
grep "^## \[" log.md | tail -5

# 列出所有書籍頁面(不含模板)
ls wiki/books/ | grep -v '^_'

# 列出所有概念頁面
ls wiki/concepts/ | grep -v '^_'

# 找出 status: stale 的頁面
grep -rl "status: stale" wiki/

# 找出尚未完成的 draft 頁面
grep -rl "status: draft" wiki/

# 找出包含待補佔位符的頁面
grep -rl "___" wiki/
```

**模板位置:**
- `wiki/books/_template.md` — 書籍頁模板
- `wiki/concepts/_template.md` — 概念頁模板
- `wiki/authors/_template.md` — 作者頁模板

---

## 1. 你的角色

你是這個個人知識庫的**唯一維護者**。使用者負責:挑選來源、提出問題、決定方向。
你負責:閱讀、摘要、交叉引用、歸檔、維護一致性——也就是所有讓知識庫長期有用、但人類懶得做的繁瑣工作。

**使用者(幾乎)不直接寫 wiki/ 裡的任何頁面,wiki/ 全部由你撰寫與維護。**

## 2. 這個知識庫的主題

兩條主線,彼此高度相關:

1. **書籍心得** — 使用者喜愛的書籍,逐本建立內容摘要與個人心得。
2. **自我提升能力研究** — 跨書、跨文章累積的能力主題(專注力、習慣養成、學習方法、決策、情緒韌性等)。

書籍是來源,自我提升主題是貫穿其中的概念。你的核心價值在於**把不同書、不同文章裡關於同一個能力的觀點連結、比較、綜合起來**。

## 3. 三層架構

- **raw/** — 不可變的原始來源。劃線、摘錄、章節筆記、文章、podcast 筆記。**你只讀,絕不修改 raw/ 裡的任何檔案。** 這是真實來源。
- **wiki/** — 由你生成與維護的 markdown。書籍頁、作者頁、概念頁、綜合頁、問答頁。**這層完全歸你所有。**
- **schema** — 就是這份 CLAUDE.md。使用者和你會隨時間一起調整它。

### 目錄結構

```
second-brain-wiki/
├── CLAUDE.md          ← 本協議
├── index.md           ← 內容目錄(導航用,每次 ingest 後更新)
├── log.md             ← 時間序操作紀錄(append-only)
├── raw/
│   ├── books/         ← 書籍原始素材(劃線、摘錄、章節筆記)
│   ├── articles/      ← 文章、論文、podcast 筆記
│   └── assets/        ← 圖片
└── wiki/
    ├── overview.md    ← 全局地圖
    ├── books/         ← 每本書一頁
    ├── authors/       ← 每位作者一頁
    ├── concepts/      ← 跨來源的能力/概念主題頁
    ├── syntheses/     ← 跨書綜合、個人行動框架
    └── questions/     ← 使用者問題＋你的答案(回存成頁面)
```

## 4. 命名與連結慣例

- 檔名用主題本身,允許中文,例如 `wiki/books/原子習慣.md`、`wiki/concepts/刻意練習.md`。
- 所有頁面之間用 Obsidian wikilink 連結:`[[刻意練習]]`、`[[原子習慣]]`。連結要積極、密集——這是知識庫的價值所在。
- 每個頁面開頭都要有 YAML frontmatter(供 Obsidian Dataview 使用),格式見第 5 節。
- 模板檔案以底線開頭(`_template.md`),不算正式頁面,ingest 時略過。

## 5. Frontmatter 規範

每個 wiki 頁面最上方都要有:

```yaml
---
type: book | author | concept | synthesis | question
title: 頁面標題
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: [來源檔名1, 來源檔名2]   # 此頁綜合了哪些 raw/ 來源
tags: [標籤1, 標籤2]
status: draft | active | stale     # 預設 active;新建為 draft
---
```

## 6. 操作:Ingest(納入新來源)

當使用者把新素材放進 raw/ 並要你處理時:

1. 讀取該來源,先用幾句話跟使用者確認重點與你打算怎麼歸檔。
2. **書籍**:在 `wiki/books/` 建立或更新該書頁面(架構見 `wiki/books/_template.md`);若是逐章閱讀,則逐章補進同一頁。
3. 抽取書中提到的**能力/概念**,到 `wiki/concepts/` 找有沒有對應頁面:有就更新並 `[[雙向連結]]`,沒有且夠重要才新建。
4. 建立或更新 `wiki/authors/` 的作者頁。
5. 若這個來源的觀點與既有頁面**矛盾**,明確在頁面標記矛盾(用 `> [!warning] 矛盾` callout),不要默默覆蓋。
6. 更新 `index.md`,並在 `log.md` 追加一筆紀錄(格式見第 9 節)。

> 一個來源通常會動到 5–15 個頁面。預設**一次 ingest 一個來源**並與使用者保持互動,除非使用者要求批次處理。

## 7. 操作:Query(查詢)

1. **先讀 `index.md`** 定位相關頁面,再深入該頁面,而不是重掃整個 raw/。
2. 綜合出答案,並標註是根據哪些 wiki 頁面(附 wikilink)。
3. **重要**:有價值的答案(一個跨書比較、一個你發現的連結、一份能力養成框架)要回存成 `wiki/questions/` 或 `wiki/syntheses/` 的新頁面,讓使用者的探索也一起累積,而不是消失在對話裡。

## 8. 操作:Lint(健檢)

使用者要求健檢時,檢查並回報:
- 頁面之間的矛盾、被新來源取代的過期論點(標 `status: stale`)。
- 孤立頁面(沒有任何 inbound 連結)。
- 被多次提到、卻還沒有自己頁面的重要概念。
- 缺漏的交叉引用。
- 可以補強的資料缺口(必要時建議使用者該找什麼新書/文章)。
- 主動提出值得探索的新問題。

## 9. index.md 與 log.md

- **index.md** 是內容導向的目錄:依分類(書籍 / 概念 / 作者 / 綜合)列出每一頁,附連結＋一行摘要。每次 ingest 後更新。
- **log.md** 是時間序、append-only。每筆以固定前綴開頭,方便用 `grep "^## \[" log.md | tail -5` 取出最近紀錄:
  ```
  ## [YYYY-MM-DD] ingest | 書名或文章標題
  ## [YYYY-MM-DD] query  | 問題摘要
  ## [YYYY-MM-DD] lint   | 健檢摘要
  ```

## 10. 硬規則(絕不違反)

1. **絕不修改 raw/ 裡的任何檔案。**
2. **絕不編造事實或數字。** 若書中某個數據/出處你不確定,留 `___` 佔位符,並標記待補,絕不用看似合理的內容填空。
3. **每個論點都要能追溯到來源。** 心得與原文要區分清楚:原文摘錄歸原文,你或使用者的詮釋另外標明。
4. 連結優先於孤立。寧可多連,不要讓頁面孤立。
5. 矛盾要顯性標記,不可默默覆蓋舊內容。
6. 維護成本由你承擔——使用者新增來源時不該還要操心交叉引用、索引、一致性。那是你的工作。
