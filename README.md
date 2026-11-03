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

**https://raw.githubusercontent.com/Wyfy0107/studio-json-feed-poc/master/feed-sample.csv**

(Alternate: https://gist.githubusercontent.com/Wyfy0107/d22ca7e95a2dba74e65350ab111291f5/raw/feed-sample.csv)

### Option B — Google Sheet for Studio “Google Docs” source

**Sheet:** [Studio JSON Feed POC - LayoutJson](https://docs.google.com/spreadsheets/d/1C8_Zb87gUfXXmZn0Gt7dgZLAG9AjWbg1Lii5zvbLXfQ/edit)

Studio does **not** use “anyone with the link”. Per [Google’s feed guide](https://support.google.com/richmedia/answer/3399836), the spreadsheet must be shared with:

- `StudioDynamicCreative@system.gserviceaccount.com` (Viewer)
- `studio-dynamic-creatives@google.com` (Viewer)

Those accounts are on the sheet now, plus **Smartly domain** read (`@smartly.io`). Smartly policy still blocks public / personal-Gmail link sharing.

In Studio, paste the URL from **Share → Copy link** (not a truncated browser bar URL). Pick tab **Sheet1**. Sheet must stay in **My Drive** (not a Shared drive).

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
| `Feed_Tagline` | **Text** | **ON** |

Each `LayoutJson` cell is one-line JSON, for example:

```json
{"layout":"hero","headline":"Summer sale","subtitle":"Up to 50% off","theme":"dark","showCta":true,"ctaText":"Shop now"}
```

Supported `layout` values in the template: `hero`, `promo`, `minimal`. Optional JSON fields: `theme`, `badge`, `subtitle`, `showCta`, `ctaText`, `imageUrl`.

**`Feed_Tagline`** is a normal feed column (not in JSON). The template reads `dynamicContent.…[0].Feed_Tagline` and shows italic text under the headline block.

**Published profile feed update:** add column in Sheet → open element → map new field → **Transform** → re-upload creative zip if HTML changed.

## Studio setup (your steps)

1. **Profile → Manage Data** → New content → **Google Docs** → paste the Sheet URL.
2. **Element properties**: map ID / Reporting label / Active; set `LayoutJson` to **Text**, toggle **Display ON** → **Transform** → save.
3. **Generate Code (HTML5)** — template is wired for profile **10965888** and element **`Studio_JSON_Feed_POC__LayoutJson_Sheet1`** (see `dynamicContent.…[0].LayoutJson` in [index.html](./index.html)).
4. Upload [studio-json-feed-poc.zip](./studio-json-feed-poc.zip) (`index.html` + `backup.jpg`). In **Manage files**, set **`backup.jpg` → Use as backup image** for the HTML in-page asset (Studio requires one backup per in-page asset). Add a **MainExit** exit if prompted.
5. **Dynamic preview**: cycle feed rows — you should see hero (dark + subtitle), promo (badge), and minimal (centered, no CTA). The green debug strip shows `layout=` and a snippet of the injected JSON when live data is used.

## Local sanity check

Open `index.html` in a browser (file or simple static server). Without Studio, the stub Enabler runs and you should see the **dev** hero layout from `devDynamicContent`. In Studio preview with a linked profile, content should come from `dynamicContent`, not the dev defaults.

## Tweaks

- Set `SHOW_DEBUG = false` in `index.html` before a “clean” upload.
- Replace `Enabler.setProfileId(0)` with your profile ID from Generate Code (Studio may inject this when you merge setup code).
