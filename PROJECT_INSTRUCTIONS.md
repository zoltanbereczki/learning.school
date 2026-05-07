# Tanulj Romanul - Project Instructions

This folder contains a static one-page Romanian vocabulary learning app for a 7-year-old Hungarian native speaker. It is designed around lessons from the Romanian first-grade manual for schools/classes with Hungarian as the teaching language.

The app is intentionally simple: no build step, no package manager, no backend, and no audio.

## Open The App

Open this file in a browser:

```text
H:\My Drive\Család\Hunor\Huni Learning\Tanulj Romanul\tanuljromanul.html
```

Browser URL:

```text
file:///H:/My%20Drive/Csal%C3%A1d/Hunor/Huni%20Learning/Tanulj%20Romanul/tanuljromanul.html
```

## Files

- `tanuljromanul.html` - Static HTML entry point.
- `app.js` - Lesson data, vocabulary, image references, game state, game logic, and rendering.
- `styles.css` - All layout, visual design, animations, responsive behavior.
- `Images/` - Local image assets used by lesson cards and picture games.
- `PROJECT_INSTRUCTIONS.md` - This high-level handoff.
- `LESSON_AUTHORING_GUIDE.md` - Step-by-step guide for adding future lessons.
- `IMAGE_EXTRACTION_GUIDE.md` - Focused workflow for extracting/cropping new lesson images from the manual.

## Product Summary

The app helps a Hungarian-speaking child practice Romanian vocabulary. The first screen shows lesson cards. After selecting a lesson, the child chooses one of three activities:

1. `Szó - szó`: Hungarian prompt, Romanian answer choices.
2. `Kép - szó`: local image prompt, Romanian answer choices.
3. `Húzd össze`: Hungarian words on the left, Romanian words on the right, matched in batches of 5.

The UI prompts are in Hungarian. Lesson titles, answer choices, and feedback are in Romanian where appropriate.

## Current Lessons

Manual source:

```text
https://manuale.edu.ro/manuale/Clasa%20I/Comunicare%20in%20limba%20romana%20pentru%20scolile%20si%20sectiile%20cu%20predare%20in%20limba%20maghiara/RURJVFVSQSBDT1JWSU4g/
```

Current lessons in `app.js`:

- Page `#80`: `Casa și curtea bunicii`
- Page `#91`: `Pădurea și animalele sălbatice`
- Page `#78`: `Să circulăm corect!`

The `#80`, `#91`, and `#78` page badges in the lesson activity screen are clickable links to the manual pages.

## Current Vocabulary

### Casa și curtea bunicii

- `perete` - fal
- `poartă` - kapu
- `gard` - kerítés
- `acoperiș` - tető
- `horn` - kémény
- `curte` - udvar
- `grădină` - kert
- `copac` - fa
- `bancă` - pad
- `leagăn` - hinta
- `terasă` - terasz
- `grajd` - istálló
- `fântână` - kút
- `grădină cu flori` - virágos kert
- `cutie poștală` - postaláda

### Pădurea și animalele sălbatice

- `pădure` - erdő
- `urs` - medve
- `veveriță` - mókus
- `lup` - farkas
- `vulpe` - róka
- `căprioară` - őz
- `cerb` - szarvas
- `mistreț` - vaddisznó
- `ciocănitoare` - harkály
- `pădurar` - erdész
- `ghindă` - makk
- `alună` - mogyoró
- `zmeură` - málna

### Să circulăm corect!

- `bulevard` - sugárút
- `drum` - út
- `șosea` - országút
- `trotuar` - járda
- `zebră` - zebra
- `trecere de pietoni` - gyalogátkelőhely
- `pieton` - gyalogos
- `pietoni` - gyalogosok
- `semafor` - közlekedési lámpa
- `pornește` - elindul
- `se oprește` - megáll
- `traversează` - átkel
- `viteză` - sebesség
- `taxi` - taxi
- `salvare` - mentőautó
- `pompieri` - tűzoltók
- `mașină de pompieri` - tűzoltóautó
- `poliție` - rendőrség
- `mașină de poliție` - rendőrautó

## App Architecture

Everything lives in `app.js`.

The important top-level structures are:

- `lessons`: array of lesson objects. Add new lessons here.
- `state`: current UI state, selected lesson, current game, progress, matching batch state.
- `MATCH_BATCH_SIZE`: currently `5`; controls how many word pairs appear at once in the matching game.

Each lesson object has this shape:

```js
{
  id: "stable-ascii-id",
  title: "Romanian lesson title",
  huTitle: "Hungarian helper title",
  page: "#91",
  sourceUrl: "manual URL ending in #91",
  color: "#16a34a",
  cover: "Images/Padure.png",
  coverSource: "Local",
  words: [
    { id: "padure", hu: "erdő", ro: "pădure", image: "Images/Padure.png", color: "#15803d" }
  ]
}
```

Each word object should normally use a local image:

```js
{ id, hu, ro, image, color }
```

The app still supports icon URLs via `icon`, but the current preferred approach is local files in `Images/`.

## Rendering Functions

Important functions in `app.js`:

- `renderLessonSelect()` - first screen, lesson cards.
- `renderActivityMenu()` - second screen, three activity options for selected lesson.
- `renderQuiz()` - shared renderer for word-word and picture-word games.
- `renderMatchMode()` - matching game.
- `startRound(mode)` - starts `word` or `picture`.
- `startMatchMode()` - starts matching mode.
- `loadMatchBatch()` - loads the next 5-word matching batch.
- `celebrate()` - full-screen confetti when a lesson activity is completed.

## Behavioral Requirements

- No audio. Do not re-add Web Speech API, speech synthesis, sound effects, or audio buttons unless explicitly requested.
- Wrong answers should only show gentle feedback: `Mai încearcă!`
- Correct answers show: `Corect!`
- Completion shows:

```text
BRAVO!
Ügyes vagy! - Ești un campion!
```

- Picture game images must be fully visible, not cropped.
- Picture game images should not have the floating animation.
- Matching game must remain in batches of 5 words so the screen stays readable.

## Image Rules

Use local image files from `Images/` whenever available.

Naming convention:

- Use Romanian word/phrase converted to PascalCase ASCII where practical.
- Remove Romanian diacritics in filenames.
- Examples:
  - `pădure` -> `Padure.png`
  - `cutie poștală` -> `CutiePostala.png`
  - `trecere de pietoni` -> `TrecereDePietoni.png`

The `Images/` folder already contains assets for the current lessons:

- Current lesson 1 house/yard images: `Casa.png`, `Acoperis.png`, `Horn.png`, `Perete.png`, `Terasa.png`, `Banca.png`, `Fantana.png`, `Grajd.png`, `Copac.png`, `Leagan.png`, `Curte.png`, `Gradina.png`, `GradinaCuFlori.png`, `Poarta.png`, `CutiePostala.png`, `Gard.png`.
- Current lesson 2 forest/animal images: `Padure.png`, `Urs.png`, `Veverita.png`, `Lup.png`, `Vulpe.png`, `Caprioara.png`, `Cerb.png`, `Mistret.png`, `Ciocanitoare.png`, `Padurar.png`, `Ghinda.png`, `Aluna.png`, `Zmeura.png`.
- Current lesson 3 road/city images: `Semafor.png`, `Trotuar.png`, `Sosea.png`, `Drum.png`, `Bulevard.png`, `Zebra.png`, `TrecereDePietoni.png`, `Pietoni.png`, `Pieton.png`, `Viteza.png`, `Porneste.png`, `SeOpreste.png`, `Traverseaza.png`, `Pompieri.png`, `MasinaDePompieri.png`, `Taxi.png`, `Salvare.png`, `Politie.png`, `MasinaDePolitie.png`.
- Extra unused forest image: `Vanator.png`; not used because `vânător` is not in the extracted page 91 words.
- There are currently 49 PNG files in `Images/`.

If images are missing for a future lesson, preferred source order is:

1. User-provided local images in `Images/`.
2. Pixabay, if direct image URLs work reliably.
3. Images from the manual.
4. Generated or other web images.

Avoid busy images, cropped objects, or pictures where another vocabulary word could also be a plausible answer.

## Manual Extraction Notes

The visible manual URL is a web app shell. The useful lesson data is in:

```text
data/book.js
```

The earlier extraction showed that each relevant page contains text blocks like:

```text
Cuvinte: ...
Structuri: ...
```

When adding a lesson, extract the `Cuvinte:` list for the target page and only add `Structuri:` phrases if the user wants them included as practice vocabulary.

For image extraction/cropping, use `IMAGE_EXTRACTION_GUIDE.md`. Keep extraction details there so this file remains the high-level project overview.

## Verification

After any change, run:

```powershell
node --check app.js
```

Then verify local image references:

```powershell
@'
const fs = require('fs');
const path = require('path');
const js = fs.readFileSync('app.js', 'utf8');
const refs = [...js.matchAll(/image: "([^"]+)"|cover: "([^"]+)"/g)].map(m => m[1] || m[2]).filter(x => x.startsWith('Images/'));
const missing = refs.filter(ref => !fs.existsSync(path.join(process.cwd(), ref)));
console.log({ refs: refs.length, missing });
'@ | node -
```

Manual browser checks:

- Lesson selection shows all lessons.
- Each lesson card uses the correct topic image.
- Clicking a lesson opens the three game options.
- The page badge links to the correct manual page.
- Word-word game works.
- Picture-word game shows full uncropped images.
- Matching game shows only 5 pairs at a time and advances to the next batch.
- Back buttons return to the right screen.
- Completion confetti appears.
- No audio plays.

## Style Guidance

Keep it kid-friendly and calm:

- Big rounded buttons.
- Bright but not chaotic colors.
- Minimal instructions.
- Large readable text.
- Local images fully visible.
- No dense paragraphs inside the child-facing UI.

Clarity beats cleverness. The child should immediately understand what to click.
