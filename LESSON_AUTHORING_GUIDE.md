# Lesson Authoring Guide

Use this guide when adding a new lesson to `Tanulj Romanul`.

Currently integrated lessons (newest first in the app):

- Page `#100`: `Pe terenul de joacă`
- Page `#92`: `Animale și plante din pădure`
- Page `#91`: `Pădurea și animalele sălbatice`
- Page `#80`: `Casa și curtea bunicii`
- Page `#78`: `Să circulăm corect!`
- Page `#77`: `Cu ce călătorim?`

## Goal

Given a new manual page or lesson name, add it as a selectable lesson in the app with the four games:

- `Szó - szó` — Hungarian prompt, Romanian answer choices.
- `Kép - szó` — local image prompt, Romanian answer choices.
- `Húzd össze` — Hungarian ↔ Romanian matching in batches of 5.
- `Kép - mondat` — activity image prompt, full Romanian sentence as answer (only shown when the lesson has a `sentences` array with at least 4 entries).

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

**Lesson order:** put the **new lesson at the top** of the array (index `0`), so it appears first on the lesson selection screen. Older lessons stay below it. The list is not sorted automatically — order in `app.js` is the order on screen.

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
  // Optional. Add when the manual page has a scene with named activities
  // and short propositions. Min 4 entries to unlock the "Kép - mondat" game.
  sentences: [
    {
      id: "s-action-id",
      ro: "Romanian sentence.",
      hu: "Magyar fordítás.",
      image: "Images/ActivitySomethingP00.png",
      color: "#0ea5e9",
    },
  ],
}
```

Rules:

- `id` values must be ASCII and unique within their list (words and sentences are separate).
- `hu` is the Hungarian meaning shown as the prompt for words; in sentences it is the Hungarian translation (not shown to the child).
- `ro` is the Romanian answer shown on buttons.
- `image` is used by the picture/sentence game.
- Use local `Images/...` paths where possible.
- Keep `sourceUrl` so the page badge opens the manual.

### Activity images for `sentences`

For the **Kép - mondat** game we crop **scenes** (not standalone object panels) from the manual.

- Source: large scene PNG on the same manual page that shows multiple children doing different activities.
- Convention: name with the prefix `Activity` + Romanian keyword + page suffix, e.g. `ActivityLeaganP100.png`, `ActivityCoardaP100.png`.
- Keep enough context around the subject so the activity is recognisable, but the **main subject must not be truncated**.
- Avoid panels that contain text or labels — `Kép - mondat` images are meant to be word-free scenes.
- Each Romanian sentence comes from the page text (`Acesta este un teren de joacă...` style narration). Prefer the manual's own wording over invented sentences.

For manual asset discovery, downloads, cropping, cleanup, and contact-sheet checks, follow `IMAGE_EXTRACTION_GUIDE.md`.

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

- New lesson appears **first** (top-left) on the lesson selection screen.
- Lesson cover image is correct.
- Page badge opens the manual page.
- All three (or four) games work.
- Picture game images are fully visible and not animated.
- Matching game uses 5-word batches.
- If the lesson has `sentences`, the `Kép - mondat` card appears in the activity menu and the activity images load.

## Do Not Change Unless Asked

- Do not add audio back.
- Do not turn this into a build-based React/Vite app.
- Do not remove existing lessons.
- Do not rename files or folders unless the user asks.
- Do not crop picture-game images.
