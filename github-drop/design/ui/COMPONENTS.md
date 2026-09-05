# mist 元件索引

**這份是給 AI 和工程師看的。要短 —— 長了會在 context 裡被淹沒。**

引入順序（先 token 再元件）：

```html
<link rel="stylesheet" href="css/mist.tokens.css">
<link rel="stylesheet" href="css/components.css">
<body class="mist">
```

全狀態實際長相：開 `reference.html`。本機預覽：

```bash
python3 scripts/serve.py . 8747
```

然後開 http://localhost:8747/styles/mist/reference.html

---

## 鐵則

1. **頁面只放 class，不寫樣式。** 出現 `style="background:..."` 或頁面層的 `<style>` 規則＝detach instance
2. **缺元件時先擴充既有元件的 variant，並回寫到 `components.css`** —— 不要在頁面裡新寫一個
3. **缺 token 時先在 `tokens/mist.tokens-studio.json` 的 comp 層加一個指向 sys 的 token**，然後跑 `./build.sh`。不要寫死數值、不要直接引用 sys
4. 交檢查：`python3 ../../scripts/check_usage.py --tokens css/mist.tokens.css --components css/components.css --pages pages/*.html`

## 表面（先選對表面，再放元件）

| class | 什麼時候用 |
|---|---|
| `.mist-page` | 最外層。藍灰頁面底 |
| `.mist-app` | app 容器。淺灰、40px 圓角 |
| `.mist-card` / `.mist-card--lg` | 白卡。內容都放這裡面 |
| `.mist-inset` | 卡**內**的填色區塊。**只能放在白卡裡**，放到 `.mist-app` 上會同色消失 |
| `.mist-divider` | hairline 分隔線 |
| `.mist-shell` / `--narrow` | 限寬（1200 / 720） |

## 版面

| class | 用途 |
|---|---|
| `.mist-split` | 主 2 : 次 1 兩欄。`--even` 改成 1:1 |
| `.mist-stack` / `--sm` | 垂直堆疊 |
| `.mist-row` / `--between` | 水平排列 |
| `.mist-grow` | 佔滿剩餘寬度 |

## 排版

`.mist-display` 56 ／ `.mist-h1` 40 ／ `.mist-h2` 28 ／ `.mist-h3` 20 ／ `.mist-body` 14 ／ `.mist-body-strong` 14 ／ `.mist-label` 12 ／ `.mist-caption` 12
顏色：`.mist-muted`（次要）／`.mist-subtle`（最淡）

**同一區塊內要跳級用**，不要相鄰級並用。

## 元件

| 元件 | 什麼時候用哪個 | 最小用法 |
|---|---|---|
| **Button** | `--primary` 一頁只有一顆／`--secondary` 次要／`--ghost` 第三順位、工具列／`--icon` 只有圖示 | `<button class="mist-btn mist-btn--primary">送出</button>` |
| **Badge** | 狀態標籤。`--done` 完成／`--active` 進行中／`--idle` 未開始／`--attention` 逾期或需注意 | `<span class="mist-badge mist-badge--done">已完成</span>` |
| **OptionCard** | 選擇題的選項。**不要用原生 radio** | 見下方 |
| **SpecCard** | 並列比較的可選方案，每張帶固定欄位（一句話講得完就用 OptionCard） | 見下方 |
| **Progress** | 比例用 `.mist-progress`；問卷分段用 `.mist-steps` | `<div class="mist-progress"><div class="mist-progress__bar" style="--mist-progress-value: 62%"></div></div>` |
| **TaskItem** | 可勾選的任務列。完成加 `.is-done` | 見下方 |
| **StatTile** | 大數字 + 標籤。多欄用 `.mist-stats` 包 | `<div class="mist-stat"><span class="mist-stat__value">12</span><span class="mist-stat__label">天</span></div>` |
| **GeneratingState** | AI 生成中。`.mist-dots` 點點／`.mist-skeleton` 骨架線／`.mist-stream` 逐字游標 | 見下方 |
| **Chart** | `.mist-lollipop` 少量分類（一週）／`.mist-hairbars` 密集趨勢 | 見下方 |
| **Field** | 文字輸入。`textarea` 加同一個 class | `<div class="mist-field"><label class="mist-field__label" for="x">標題</label><input class="mist-field__input" id="x"></div>` |
| **Nav** | 頂部導覽。目前頁加 `.is-active` | `<nav class="mist-nav">…</nav>` |

### OptionCard

選中靠 `aria-checked="true"`，不可選用 `aria-disabled="true"`。`__desc` 可省略。

```html
<button class="mist-option" role="radio" aria-checked="false">
  <span class="mist-option__marker" aria-hidden="true"><svg viewBox="0 0 16 16" fill="none"><path d="M3.5 8.5l3 3 6-6.5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg></span>
  <span class="mist-option__body">
    <span class="mist-option__label">選項標題</span>
    <span class="mist-option__desc">補充說明（可省略）</span>
  </span>
</button>
```

### SpecCard

選中同樣靠 `aria-checked="true"`。**同一組 `.mist-specs` 內是單選**，切換要自己清掉別張。
`__lede` 會吃掉剩餘高度，所以同一列卡片的規格區與底部會對齊，不必補等高。
`__foot` 的文字由頁面提供（選中／未選中兩種），元件只負責顏色與勾勾的顯隱。

**欄位名稱在每張卡都要一致** —— 欄位不一致就不叫比較，那只是六張各說各話的卡片。

```html
<div class="mist-specs">
  <button class="mist-spec" role="radio" aria-checked="false">
    <span class="mist-spec__head">
      <span class="mist-spec__id">S-1</span>
      <span class="mist-badge mist-badge--done">資料支持</span>
    </span>
    <span class="mist-spec__title">方案名稱</span>
    <span class="mist-spec__lede">「一句話的願景」</span>
    <dl class="mist-spec__rows">
      <div class="mist-spec__row"><dt class="mist-spec__term">五年</dt><dd class="mist-spec__detail">…</dd></div>
      <div class="mist-spec__row"><dt class="mist-spec__term">代價</dt><dd class="mist-spec__detail">…</dd></div>
    </dl>
    <span class="mist-spec__foot"><svg viewBox="0 0 16 16" fill="none" aria-hidden="true"><path d="M3.5 8.5l3 3 6-6.5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>選這個方案</span>
  </button>
  <!-- 其餘卡片同結構 -->
</div>
```

### TaskItem

```html
<button class="mist-task is-done">
  <span class="mist-task__check" aria-hidden="true"><svg viewBox="0 0 16 16" fill="none"><path d="M3.5 8.5l3 3 6-6.5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg></span>
  <span class="mist-task__body">
    <span class="mist-task__label">任務名稱</span>
    <span class="mist-task__meta">3 天前完成</span>
  </span>
  <span class="mist-badge mist-badge--done">已完成</span>
</button>
```

### GeneratingState

```html
<section class="mist-generating">
  <div class="mist-row">
    <div class="mist-dots" aria-hidden="true"><i></i><i></i><i></i></div>
    <p class="mist-generating__label">正在整理你的規劃表</p>
  </div>
  <p class="mist-generating__hint">說明等待原因與預估時間</p>
  <div class="mist-skeleton" aria-hidden="true"><i></i><i></i><i></i><i></i><i></i></div>
</section>
```

### Chart

選中的那一欄加 `.is-selected` —— 招牌的貫穿高亮柱就在這裡。

```html
<div class="mist-lollipop">
  <div class="mist-lollipop__col is-selected" style="--mist-stem: 78%">
    <span class="mist-lollipop__tip">3 項</span>
    <div class="mist-lollipop__plot"><span class="mist-lollipop__dot"></span><i class="mist-lollipop__stem"></i></div>
    <button class="mist-lollipop__node">二</button>
  </div>
  <!-- 其餘欄同結構，不加 is-selected -->
</div>
```

密集細線圖：`.mist-hairbars`（可加 `--accent` 橘紅 ／ `--mark` 深色），每根 `<i style="--mist-bar: 62%">`。
**根數要多**（20 根以上）才會讀成線；根數少會看起來像實心色塊。

## 資料介面

頁面要把數值傳進元件，**只能用這三個** custom property。其他一律不准寫 inline style。

| 變數 | 用在 |
|---|---|
| `--mist-progress-value` | 進度條填滿比例 |
| `--mist-stem` | lollipop 的柱高比例 |
| `--mist-bar` | 密集細線圖的單根高度 |

## 目前沒有的元件

Dialog / Toast / Tabs / Table / Switch / Avatar / Dropdown（真的下拉）/ 檔案上傳。

需要時照「鐵則 2」處理 —— 在 `components.css` 加，不要在頁面裡寫。
