# 2026 Futuremode Hackathon · 個人專屬教練

三天黑客松專案。做一個**像健身教練一樣追蹤人生目標**的系統：幫使用者確認目標適不適合自己、排出做得完的行動、靠既有痕跡（行事曆、Notion、履歷、健康資料、工作系統、AI 對話）對帳出他實際走了多遠與哪一塊被冷落，並在該補的地方裝上會讓他真的執行的結構。

**預設不要求他新增任何紀錄行為；會新增 routine 的功能一律標成可選，並說明它換到什麼。**

規格拆成兩份：`docs/00-background.md`（persona 與四項痛點）與 `docs/01-solution.md`（解法、功能分層、三天 MVP）。原稿在上層資料夾 `個人教練專案_目標定義.md`，但那份已被定位變更取代，不是副本關係。**這兩份是規格來源，本專案的任何設計決定不得與它們衝突；若要衝突，先在 `PROGRESS.md` 記錄理由。**

---

## 這個專案的一句話定位

外層是**教練全流程**：訂目標 → 排行動 → 排菜單 → 追執行 → 驗成效。這五段對應白板上的五個痛點，上台講的就是這條線。

內層是**稽核，不是工作鏈自動化**。平台級的整合（connector + MCP）遲早會把資料串接做成 commodity，所以差異化不放在「串不串得起來」，放在「串起來之後憑什麼說你這邊需要補」——判準層，不是通道層。

判準層的具體內容：診斷五判準、錨點缺口、出席率 ≠ 進展、成長式修正 vs 逃避式修改。

**換殼不換內容物。** 2026-09-05 把外層敘事從「零 routine 稽核系統」改成教練全流程（理由見 `PROGRESS.md`），判準層一字未動——所以已發佈的三站 demo 與 `docs/02-差異化論述.md` 都還成立。

---

## Demo 是三站，兩個通路

三頁都用 **mist 設計系統**（來源在 `ui-kit/`）。**來源永遠是 `ui-kit/pages/*.html`**，
根目錄那些單檔都是 `inline_page.py` 產生出來的，不要手改。

| 站 | 來源 | GitHub Pages（公開，可直接貼） | Artifact（私有，要自己開分享） |
|---|---|---|---|
| 1 · 上傳與方向 | `ui-kit/pages/01-intake.html` | [/](https://wu0h9625-boop.github.io/guru-intake-prototype/) | `0db48ad7-2db5-426a-ac7e-b82101905227` |
| 2 · 目標樹草案 | `ui-kit/pages/02-plan.html` | [/02-plan.html](https://wu0h9625-boop.github.io/guru-intake-prototype/02-plan.html) | `04cea755-9563-4896-9f4c-4796edcbf34d` |
| 3 · 季度對帳 | `ui-kit/pages/03-ledger.html` | [/03-ledger.html](https://wu0h9625-boop.github.io/guru-intake-prototype/03-ledger.html) | `d9c6f5b0-b21a-4598-b3d7-55ff1a7abbb9` |

Artifact 網址前綴是 `https://claude.ai/code/artifact/`。元件參考站（每個元件的每個狀態）在
[/design/ui/reference.html](https://wu0h9625-boop.github.io/guru-intake-prototype/design/ui/reference.html)。

### 改一頁的完整流程

```bash
cd ui-kit
# 1. 改 pages/0X-*.html（只放 class，不寫樣式）
./check.sh                                              # 2. 沒過就是有問題，不要忽略
python3 scripts/inline_page.py pages/0X-*.html ../demo/0X-*.html                    # 3a. Artifact 片段
python3 scripts/inline_page.py pages/0X-*.html ~/Documents/guru-intake-prototype/0X-*.html --standalone   # 3b. Pages 版
```

站 1 的 Pages 版要產兩次（`01-intake.html` 與 `index.html`）。改完 Pages 版要 `git push`。

**Artifact 那一路的注意事項**：用 `Artifact` 工具更新時要帶 `url` 參數指回上表的 ID，否則會
產生新的 artifact，站與站之間的硬編連結就會斷。**發佈前一定要先 `action:"read"` 讀回來**——
站 1 的 artifact 目前是衝突狀態（線上比本地新）。

兩個通路的差別只有站與站之間的連結：Pages 版是站內相對連結，Artifact 片段由 `inline_page.py`
自動換成絕對網址。

## 設計系統

三頁共用 mist，規則全部在 `ui-kit/CLAUDE.md` 與 `ui-kit/ui/COMPONENTS.md`。**寫任何畫面之前先讀那兩份。**

四條鐵則：`ui/` 唯讀（會被設計系統的更新整個覆蓋）· 頁面只放 class 不寫樣式 · 缺元件回設計系統做
（在 `~/Documents/個人設計系統/styles/mist/`）· 真的來不及才寫 `local-overrides.css`，而且它會被列為債務。

**傳資料進元件只能用三個 custom property**：`--mist-progress-value`、`--mist-stem`、`--mist-bar`。

**每頁形式刻意不同**（別統一成卡片牆）

- 站 1 是**選擇頁** → SpecCard 陣列，每張帶固定欄位與代價
- 站 2 是**計畫書** → Disclosure 手風琴，收合時仍分得出分支類別
- 站 3 是**報表** → Table，扁平可比較

**已知的取捨**（完整理由見 `PROGRESS.md` 2026-09-06 那則）

1. **沒有深色模式**——mist 的亮度分層要整條反轉才做得出來，庫裡還沒做
2. **沒有等寬字與襯線字**——會計語彙只剩用詞，不再有字體上的對應
3. **不用實心色塊圖表**——站 1 的時間分配改用 lollipop

## 語氣

寫在頁面上的每一句話都要通過這三條：

1. **盤點，不審判。** 診斷精準但基調像審判的教練，只會被關掉。
2. **預設懷疑目標，不預設守護目標。** 看到落後，正確反應常常是「該砍了」，不是催他做。
3. **不承諾軟體做不到的事。** 不證明因果，只讓「有做但沒效」被看見。這條寫進了站 3 的頁尾，別拿掉。

**主敘事的用詞跟著會計語彙走**：對帳、歸戶、帳、稽核、體測、處方、錨點。這是判準層的語言，是這個產品跟習慣追蹤 app 的差別所在，不要稀釋掉。

可選執行層（每日菜單、通知、問責）允許用一般語彙，因為那一層本來就是那些產品做的事。但它是可選的、是第二順位，**別讓它的語言蓋過主敘事**——打卡、習慣、連續天數這幾個詞不要出現在主線頁面上。

---

## 三天範圍

**P0 必做**：方向假設 intake（站 1，對應「怎麼訂目標」）· 目標樹資料模型（含四要素、型別、效果假設／反證條件）· 對帳引擎（歸戶 → 四種結果）· 診斷儀表板（落後／失衡／隱形投入／錨點缺口）· 空檔調度問答

**P1 有時間才做**：容量感知排程器（只能刪）· 週檢查（校對式）· 錨點處方 · 每日菜單（可選層，預設關閉）

**P2 只講概念不實作**：目標版本化與五訊號 · 個人樂觀係數 · 累積型能力重測 · 限制因素判準 · 障礙模擬 · 社群問責與獎懲 · 反悔市場／鏈上質押 · 跨使用者交叉分析

**明確不做**：註冊／多人／權限 · 真實 API 串接（用匯出檔或假資料）

範圍的完整推導在 `docs/01-solution.md`，三天要交付什麼收斂在該檔第十一節「三天 MVP · 前端殼與後端交界」。

---

## 兩個已定的取捨

**用假資料，但故事要真。** 對帳引擎的價值在歸戶結果，不在資料怎麼進來。串 OAuth 會吃掉半天卻不增加任何 demo 說服力。

**歸戶規則先寫死。** 關鍵字 + 分支對照表硬編，跨分支一律歸主分支並標記。這個假設要在簡報上講明，頁面上也已經標了（站 3 的 `.footmark`）。

---

## 檔案地圖

```
CLAUDE.md                      ← 你正在讀的這份
PROGRESS.md                    ← 現在進度、決策紀錄、下一步
demo/
  01-intake.html               ← 產生出來的 Artifact 片段，不要手改
  02-plan.html                 ← 同上
  03-ledger.html               ← 同上
ui-kit/                        ← 三頁的來源。mist 設計系統的專案端副本
  pages/                       01-intake / 02-plan / 03-ledger 的來源
  ui/                          唯讀：token、元件、規格、元件參考站
  scripts/inline_page.py       產生單檔（Artifact 片段 / --standalone 自架版）
  check.sh                     交出去前跑這支
docs/
  00-background.md               persona 張雅婷、四項痛點論述
  01-solution.md                 解法規格。教練角色、設計原則、目標樹、
                                 診斷判準、排程、執行力機制、P0/P1/P2
                                 功能分層、三天 MVP 與後端交界
  02-差異化論述.md               回應「平台也做得到」的挑戰
  03-站1-intake-結構.md          站 1 七段結構（HTML 尚未依此改寫）
pitch/
  demo-腳本.md                  三站的上台順序與台詞
```

---

## 在這個專案裡工作時

- 動任何一頁的**資料**（分支名稱、數字、日期）就要檢查跨頁一致性。三頁共用同一組假資料與同一條時間軸（計畫 2026-06-28 起草 → Q3 對帳 09-04），改一處要跟著改另外兩處。
- 頁面沒有 build step、沒有相依套件、不要引入框架。但**頁面不是單檔了**——來源在 `ui-kit/pages/`，單檔是產生出來的。
- 改來源檔，不要另開新檔——新路徑會變成新的 artifact 網址。
