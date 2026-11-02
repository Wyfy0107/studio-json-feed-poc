# Studio JSON feed POC

Spike to test whether **JSON stored as text in a dynamic feed column** can be read in an HTML5 Studio creative and used for conditional rendering.

Google does not support JSON as a feed **file format**; this POC uses a **Text** column (`LayoutJson`) with minified JSON strings per row.

## Files

| File | Purpose |
|------|---------|
| [index.html](./index.html) | 300×250 HTML5 creative (Enabler + `JSON.parse` + layout switch) |
| [feed-sample.csv](./feed-sample.csv) | Sample feed rows |
| [studio-json-feed-poc.zip](./studio-json-feed-poc.zip) | Upload bundle for Studio |

## Feed data (pick one)

### Option A — Public CSV URL (no Google login)

Use this as Studio **Remote file** (CSV) content source:

**https://gist.githubusercontent.com/Wyfy0107/d22ca7e95a2dba74e65350ab111291f5/raw/feed-sample.csv**

Gist page: https://gist.github.com/Wyfy0107/d22ca7e95a2dba74e65350ab111291f5

### Option B — Your own public Google Sheet (~1 min)

Smartly Drive **blocks** public / personal-Google sharing on the auto-created sheet. The sheet is shared **domain-wide for `@smartly.io`** ([link](https://docs.google.com/spreadsheets/d/1C8_Zb87gUfXXmZn0Gt7dgZLAG9AjWbg1Lii5zvbLXfQ/edit) — open while signed into a Smartly Google account). If you use a personal Gmail, Google shows *“Sorry, the file you have requested does not exist”* even though the file is real.

To get a **public** Sheet:

1. Open [sheets.google.com](https://sheets.google.com) on a **personal** Google account (or any account without that restriction).
2. **File → Import → Upload** → choose [feed-sample.csv](./feed-sample.csv).
3. **Share → General access → Anyone with the link** → Viewer (or Editor if Studio requires it).
4. Use that Sheet URL in Studio as **Google Docs** source.

Local copy of the data is always in [feed-sample.csv](./feed-sample.csv).

### Columns

| Column | Studio field type | Display |
|--------|-------------------|---------|
| `id` | ID mapping | Usually off |
| `Reporting_Label` | Reporting label | Usually off |
| `Active` | Active | — |
| `LayoutJson` | **Text** | **ON** |

Each `LayoutJson` cell is one-line JSON, for example:

```json
{"layout":"hero","headline":"Summer sale","subtitle":"Up to 50% off","theme":"dark","showCta":true,"ctaText":"Shop now"}
```

Supported `layout` values in the template: `hero`, `promo`, `minimal`. Optional fields: `theme` (`dark` \| `light`), `badge`, `subtitle`, `showCta`, `ctaText`.

## Studio setup (your steps)

1. **Profile → Manage Data** → New content → **Google Docs** → paste the Sheet URL.
2. **Element properties**: map ID / Reporting label / Active; set `LayoutJson` to **Text**, toggle **Display ON** → **Transform** → save.
3. **Generate Code (HTML5)** → note the element name (e.g. `MySheet_Sheet1`). If it is not `Dynamic_Feed`, edit `DYNAMIC_ELEMENT_NAME` at the top of the script in [index.html](./index.html) and re-zip.
4. Upload [studio-json-feed-poc.zip](./studio-json-feed-poc.zip) (or zip `index.html` yourself). Add a **MainExit** exit in Studio if prompted.
5. **Dynamic preview**: cycle feed rows — you should see hero (dark + subtitle), promo (badge), and minimal (centered, no CTA). The green debug strip shows `layout=` and a snippet of the injected JSON when live data is used.

## Local sanity check

Open `index.html` in a browser (file or simple static server). Without Studio, the stub Enabler runs and you should see the **dev** hero layout from `devDynamicContent`. In Studio preview with a linked profile, content should come from `dynamicContent`, not the dev defaults.

## Tweaks

- Set `SHOW_DEBUG = false` in `index.html` before a “clean” upload.
- Replace `Enabler.setProfileId(0)` with your profile ID from Generate Code (Studio may inject this when you merge setup code).
