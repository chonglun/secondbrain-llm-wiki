# second-brain-wiki

一個依 Karpathy「LLM Wiki」模式建立的個人知識庫,主題:**書籍心得 + 自我提升能力研究**。
核心觀念:AI 增量維護一個持久、互相連結的 markdown wiki,而不是每次查詢都重撈原始資料。

## 安裝(約 5 分鐘)

1. **放到不被雲端同步的路徑**(避免和 git 衝突):
   - macOS:`~/Knowledge/second-brain-wiki`
   - Windows:`C:\Knowledge\second-brain-wiki`

2. **用 Obsidian 開啟**:Obsidian →「Open folder as vault」→ 選這個資料夾。

3. **(建議)做版控**:在資料夾內 `git init`,日後每次 ingest 後 commit,就有完整版本歷史。

4. **接上 AI agent**:
   - 連雲端最強:在這個資料夾跑 **Claude Code**,它會自動讀 `CLAUDE.md`。
   - 完全本機:用 **Ollama / LM Studio** 跑本機模型(建議 14B–32B,如 Qwen 3 32B),搭配支援本機的工具(如 Synto)。

## 日常用法

- **加東西**:把劃線/摘錄/文章丟進 `raw/books/` 或 `raw/articles/`,跟 AI 說「ingest 這個檔」。
- **問問題**:直接問 AI,例如「比較我讀過的書對『習慣養成』的看法」。好答案 AI 會回存成頁面。
- **健檢**:偶爾叫 AI「lint 一次」,找矛盾、過期、孤立頁面。

## 結構

- `CLAUDE.md` — 維護協議(AI 的行為規範,最重要的檔)
- `index.md` — 內容目錄
- `log.md` — 操作紀錄
- `raw/` — 原始來源(AI 只讀不改)
- `wiki/` — AI 維護的知識頁面

## 推薦的 Obsidian 外掛

- **Dataview** — 用 frontmatter 自動生成清單/表格
- **Graph view**(內建) — 看知識庫的連結形狀、找孤立頁面
- **Web Clipper**(瀏覽器擴充) — 一鍵把文章轉成 markdown 存進 `raw/articles/`
