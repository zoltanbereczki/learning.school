# Image Extraction Guide

Use this file when the user provides a new lesson URL and asks for images to be extracted/prepared. This guide is intentionally focused only on image extraction. App wiring belongs in `LESSON_AUTHORING_GUIDE.md`.

## Current Project Location

Work inside:

```text
H:\My Drive\Család\Hunor\Huni Learning\Tanulj Romanul
```

Final image assets go here:

```text
H:\My Drive\Család\Hunor\Huni Learning\Tanulj Romanul\Images
```

Temporary downloads, crops, and previews go here:

```text
H:\My Drive\Család\Hunor\Huni Learning\Tanulj Romanul\.codex_tmp\pageXX
```

The current `Images/` folder contains 49 PNG files. Pages already extracted:

- Page `#91`, `Pădurea și animalele sălbatice`.
- Page `#80`, `Casa și curtea bunicii`.
- Page `#78`, `Să circulăm corect!`.

These three pages are also wired into `app.js` as selectable lessons. For app wiring details, use `LESSON_AUTHORING_GUIDE.md`.

Do not start from scratch. Continue the same asset-extraction pattern.

## Goal For A New Lesson URL

When the user provides a new manual lesson URL:

1. Detect the page number from the URL hash, for example `#78`.
2. Parse the manual `data/book.js`.
3. Find the section for that page.
4. Extract the `Cuvinte:` line.
5. Download only image assets used by that section.
6. Inspect the large labelled page image, any unlabelled scene image, and standalone object panels.
7. Save clean named PNG files into `Images/`.
8. Report the detected page number, extracted vocabulary, created filenames, final `Images` path, and total PNG count.

Do not edit `app.js` unless the user explicitly asks to wire the lesson into the app.

## Manual Source

Manual root:

```text
https://manuale.edu.ro/manuale/Clasa%20I/Comunicare%20in%20limba%20romana%20pentru%20scolile%20si%20sectiile%20cu%20predare%20in%20limba%20maghiara/RURJVFVSQSBDT1JWSU4g/
```

Lesson URLs use hash page numbers:

```text
https://manuale.edu.ro/manuale/Clasa%20I/Comunicare%20in%20limba%20romana%20pentru%20scolile%20si%20sectiile%20cu%20predare%20in%20limba%20maghiara/RURJVFVSQSBDT1JWSU4g/#78
```

The visible manual page is a web app shell. The useful structured data is in:

```text
data/book.js
```

Asset URLs are relative paths such as:

```text
data/static/media/example.png
```

Download with:

```text
manual_root + resource.url
```

## Inspecting `book.js`

Technique:

- Download `data/book.js` if it is not already present in the page temp folder.
- Evaluate it in Node with a VM context containing `{ window: {} }`.
- Read `window.BOOK_DB` and `window.BOOK_ID`.
- Find the TOC item whose content contains `<p>PAGE</p>`.
- Use that `item.section` to inspect the lesson section.
- Walk `section.body` for text nodes.
- Read `section.resources` for image resource IDs.

Sample Node script:

```js
const fs = require("fs");
const vm = require("vm");

const bookPath = "H:/My Drive/Család/Hunor/Huni Learning/Tanulj Romanul/.codex_tmp/page78/book.js";
const code = fs.readFileSync(bookPath, "utf8");
const context = { window: {} };
vm.createContext(context);
vm.runInContext(code, context);

const db = context.window.BOOK_DB;
const projectId = context.window.BOOK_ID;
const page = "78";

const toc = db[projectId].body.toc;
const item = toc.find((x) => (x.content || "").includes(`<p>${page}</p>`));
const section = db[item.section];

function strip(s) {
  return (s || "")
    .replace(/<[^>]+>/g, " ")
    .replace(/&[^;]+;/g, " ")
    .replace(/\s+/g, " ")
    .trim();
}

const resources = (section.resources || []).map((id) => {
  const r = db[id];
  const m = r.sources?.media?.[0];
  return {
    id,
    type: r.type,
    url: m?.url,
    name: m?.name,
    mimetype: m?.mimetype,
    width: m?.width,
    height: m?.height,
    bytes: m?.bytes,
  };
});

const texts = [];
function walk(nodes) {
  for (const n of nodes || []) {
    if (n.content) texts.push(strip(n.content));
    if (n.kids) walk(n.kids);
  }
}
walk(section.body);

console.log(JSON.stringify({ section: item.section, resources, texts }, null, 2));
```

## Image Selection Rules

Prefer, in order:

1. Standalone object panels from the manual.
2. Unlabelled scene images from the same page.
3. Crops from labelled scene images, with labels carefully removed.
4. User-provided local images.
5. Generated or web images only if manual/local assets are insufficient.

When cropping:

- Crop out workbook borders, answer boxes, labels, dots, arrows, and task text.
- If the standalone panel includes printed text on/near the object, paint it out with Pillow `ImageDraw` when practical.
- If the large labelled PNG has words/dots, look for an unlabelled JPG/PNG version of the same scene before cropping the labelled one.
- Keep the object fully visible.
- Avoid tight crops that cut off context needed to understand the object.
- Make a contact sheet preview before finalizing to catch labels, borders, or bad crops.

## File Naming

Save final assets directly under `Images/`.

Rules:

- Use PascalCase.
- Use ASCII filenames without Romanian diacritics.
- Use `.png`.
- Do not overwrite unrelated existing assets.
- Overwrite only if the new asset intentionally replaces the same concept.

Examples:

- `cerb` -> `Cerb.png`
- `cutie poștală` -> `CutiePostala.png`
- `trecere de pietoni` -> `TrecereDePietoni.png`
- `mașină de poliție` -> `MasinaDePolitie.png`

## Runtime Notes

Python/Pillow runtime:

```text
C:\Users\zolta\.cache\codex-runtimes\codex-primary-runtime\dependencies\python\python.exe
```

Use Pillow for:

- Cropping.
- Transparency/background handling if needed.
- Painting out labels.
- Contact sheet generation.

## Completed Examples

Page `#91`, `Pădurea și animalele sălbatice`:

```text
Padure.png, Urs.png, Veverita.png, Lup.png, Vulpe.png, Caprioara.png, Cerb.png, Mistret.png, Ciocanitoare.png, Padurar.png, Ghinda.png, Aluna.png, Zmeura.png
```

Page `#80`, `Casa și curtea bunicii`:

```text
Casa.png, Acoperis.png, Horn.png, Perete.png, Terasa.png, Copac.png, Leagan.png, Fantana.png, Grajd.png, Curte.png, Gradina.png, GradinaCuFlori.png, Poarta.png, CutiePostala.png, Banca.png, Gard.png
```

Page `#78`, `Să circulăm corect!`:

```text
Bulevard.png, Drum.png, Sosea.png, Trotuar.png, Zebra.png, TrecereDePietoni.png, Pieton.png, Pietoni.png, Semafor.png, Porneste.png, SeOpreste.png, Traverseaza.png, Viteza.png, Taxi.png, Salvare.png, Pompieri.png, MasinaDePompieri.png, Politie.png, MasinaDePolitie.png
```

## Final Report Format

When finished, report:

- Detected page number.
- Extracted `Cuvinte:` words.
- Created filenames.
- Final image folder path.
- Total PNG count in `Images/`.
- Any unused image candidates or uncertain crops.
