# Lesson Authoring Guide

Use this guide when adding a new lesson to `Tanulj Romanul`.

Currently integrated lessons:

- Page `#80`: `Casa și curtea bunicii`
- Page `#91`: `Pădurea și animalele sălbatice`
- Page `#78`: `Să circulăm corect!`

## Goal

Given a new manual page or lesson name, add it as a selectable lesson in the app with the same three games:

- `Szó - szó`
- `Kép - szó`
- `Húzd össze`

## Step 1: Identify The Lesson

Find these fields:

- Romanian lesson title from the manual.
- Hungarian helper title.
- Manual page number, for example `#92`.
- Manual source URL ending in that hash.
- Vocabulary words from the `Cuvinte:` block.
- Optional structures from `Structuri:` only if the user asks to include phrases.

Manual base URL:

```text
https://manuale.edu.ro/manuale/Clasa%20I/Comunicare%20in%20limba%20romana%20pentru%20scolile%20si%20sectiile%20cu%20predare%20in%20limba%20maghiara/RURJVFVSQSBDT1JWSU4g/
```

Example lesson URL:

```text
https://manuale.edu.ro/manuale/Clasa%20I/Comunicare%20in%20limba%20romana%20pentru%20scolile%20si%20sectiile%20cu%20predare%20in%20limba%20maghiara/RURJVFVSQSBDT1JWSU4g/#91
```

## Step 2: Prepare Images

Check `Images/` first. If the needed files exist, use them.

Filename convention:

- PascalCase.
- No spaces.
- No Romanian diacritics.
- `.png` preferred.

Examples:

- `semafor` -> `Semafor.png`
- `trecere de pietoni` -> `TrecereDePietoni.png`
- `mașină de poliție` -> `MasinaDePolitie.png`

If an image is missing, ask the user if they plan to provide it, or use the source order in `PROJECT_INSTRUCTIONS.md`.

For manual asset discovery, downloads, cropping, cleanup, and contact-sheet checks, follow `IMAGE_EXTRACTION_GUIDE.md`.

## Step 3: Add Lesson Data

Open `app.js` and add a new object to the `lessons` array.

Template:

```js
{
  id: "ascii-stable-lesson-id",
  title: "Romanian title",
  huTitle: "Hungarian title",
  page: "#92",
  sourceUrl:
    "https://manuale.edu.ro/manuale/Clasa%20I/Comunicare%20in%20limba%20romana%20pentru%20scolile%20si%20sectiile%20cu%20predare%20in%20limba%20maghiara/RURJVFVSQSBDT1JWSU4g/#92",
  color: "#0ea5e9",
  cover: "Images/SomeCover.png",
  coverSource: "Local",
  words: [
    { id: "word-id", hu: "Hungarian", ro: "Romanian", image: "Images/SomeImage.png", color: "#0ea5e9" },
  ],
}
```

Rules:

- `id` values must be ASCII and unique.
- `hu` is the Hungarian meaning shown as the prompt.
- `ro` is the Romanian answer shown on buttons.
- `image` is used by the picture game.
- Use local `Images/...` paths where possible.
- Keep `sourceUrl` so the page badge opens the manual.

## Step 4: Verify

Run:

```powershell
node --check app.js
```

Then check all local image references:

```powershell
@'
const fs = require('fs');
const path = require('path');
const js = fs.readFileSync('app.js', 'utf8');
const refs = [...js.matchAll(/image: "([^"]+)"|cover: "([^"]+)"/g)].map(m => m[1] || m[2]).filter(x => x.startsWith('Images/'));
const missing = refs.filter(ref => !fs.existsSync(path.join(process.cwd(), ref)));
console.log(JSON.stringify({ refs: refs.length, missing }, null, 2));
'@ | node -
```

Open:

```text
file:///H:/My%20Drive/Csal%C3%A1d/Hunor/Huni%20Learning/Tanulj%20Romanul/tanuljromanul.html
```

Check:

- New lesson appears on the first screen.
- Lesson cover image is correct.
- Page badge opens the manual page.
- All three games work.
- Picture game images are fully visible and not animated.
- Matching game uses 5-word batches.

## Do Not Change Unless Asked

- Do not add audio back.
- Do not turn this into a build-based React/Vite app.
- Do not remove existing lessons.
- Do not rename files or folders unless the user asks.
- Do not crop picture-game images.
