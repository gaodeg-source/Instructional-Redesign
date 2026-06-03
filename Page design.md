# Page Design — Agent Instructions

Read this file **first** before building or revising any `page X.html`.  
Then read the matching `page X.md` (or `pageX.md`) in the same module folder.

---

## 1) Workflow (every page)

1. Read `Page design.md` (this file).
2. Read `Module-N/page X.md` (source of truth for order, copy, activities).
3. For every line that references `assets/...`, open the matching file under the repo **`assets/`** folder and use it as the **content** reference (see §3).
4. Implement `Module-N/page X.html` in the same order as the markdown.
5. Use **only** classes from `shared.css` (+ minimal JS for interactions). No page-specific `<style>` blocks unless explicitly approved.
6. Preview from repo root: `python3 -m http.server 8000` → `http://localhost:8000/Module-N/page%20X.html`

**Paths:** Use `Module-1/` (capital M) for new work. Link shared files as `../shared.css`, `../shared.js`, `../config.js`.

---

## 2) How to read `page X.md`

| Markdown element | Meaning |
|------------------|---------|
| Page title / headings | Section titles in HTML (`section-title`, `section-tag`) |
| `What you'll discover:` | `info-box` with `h3` + arrow lines (`&#8594;&ensp;`) |
| `Scenario:` | `section-tag` + `analogy-box` (see §5) |
| `Talk:` | Dialogue block (see §6) |
| `Activity N` | `section-card activity-card` + shared activity components |
| `[...]` | Activity **logic** spec when no asset image exists (items, rules, exact feedback strings) |
| `Image: refer to assets/...` | Read asset image → rebuild **content** with shared components (see §3–4) |
| `[refer to assets/...]` | Same as above, usually for an interactive activity |
| `Callout:` | `callout` |
| `Button to proceed: <label>` | Progressive reveal control (see §7) |
| `Rabbit Hole:` | `rabbit-hole` + `rabbit-hole-trigger` + `rabbit-hole-body`; use `toggleRabbitHole()` from `shared.js` |

Convert prose into learner-facing HTML with light editing only (punctuation, line breaks).

---

## 3) Asset reference images (`refer to assets/...`)

When `page X.md` says `refer to assets/<name>` (with or without brackets):

1. Resolve the file under **`/assets/`** at repo root:
   - Try `<name>.png`, then `.jpg`, `.webp` if extension omitted.
   - Examples: `assets/1.2-activity1` → `assets/1.2-activity1.png`
2. **Read the image** (layout, labels, lists, numbers, speaker, sections).
3. **Recreate the same information** in HTML using **only** `shared.css` components.

### Critical rule: reference ≠ copy the screenshot styling

- Assets show **what** to include (copy, structure, grouping, totals, speaker).
- **How** it looks must follow this doc and `shared.css` — not colors, fonts, or borders from the PNG.
- Do **not** embed reference PNGs as the final learner UI unless the md explicitly says to show that image file.
- Do **not** add new CSS classes or inline visual systems to mimic a mockup.

### Typical mappings from reference images

| What you see in the reference | Shared components to use |
|------------------------------|---------------------------|
| Section title / header | `section-title`, `section-tag`, `subsection-head` |
| Speaker + quoted dialogue | **Talk** pattern (§6) |
| Table / inventory / line items + amounts | `section-card`, `acct-col`, `acct-line`, `acct-total`, or `delta-box` + `delta-row` |
| Summary / “discovered” bullets | `info-box` or `insight-box` + `prose`; use `&#10003;` or text in `<p>` |
| Form with drag zones | `dnd-bank-wrap`, `dnd-bank`, `dnd-zones`, `dnd-zone`, `dnd-item` |
| MCQ | `mcq-list`, `mcq-opt`, `mcq-fb` |
| Completion / recap screen | `section-title`, `section-card`, `insight-box`, `callout` (content from image, not screenshot chrome) |

If an asset is missing, ask or note in the commit; do not invent layout from memory.

---

## 4) `Image:` lines vs activities

- **`Image: refer to assets/...`** — Usually **non-interactive** content revealed in the page flow (tables, Maria’s summary, tutorial-complete recap). Build from shared layout components after reading the asset.
- **`Activity N` + asset reference** — **Interactive** behavior; asset defines fields, bins, items, and labels; implement with shared activity classes + JS. Feedback strings in md must match exactly when provided.

---

## 5) Scenario blocks

- Label: `<div class="section-tag">Scenario</div>`
- Body: `<div class="analogy-box">...</div>`
- Do not use plain `prose`-only scenarios unless md says so.

---

## 6) Talk blocks (dialogue)

Format in md:

```text
Talk: MARIA: “Quote text…”
Talk: MARIA: (quietly) “Quote…”
```

Implement with **shared components only** — one consistent pattern per page:

```html
<div class="section-card">
  <div class="insight-label">Maria</div>
  <p class="section-sub">(quietly)</p>   <!-- only if md includes stage direction -->
  <div class="prose">
    <p>Quote text here.</p>
  </div>
</div>
```

Rules:

- **Speaker** → `insight-label` (short name; match md, e.g. `MARIA` → label text `Maria` or `MARIA` as in copy).
- **Stage direction** e.g. `(quietly)` → `section-sub` between label and quote, or italic phrase inside `prose`.
- **Speech** → `prose` / `<p>`; keep quotation content faithful to md.
- For long multi-line speech (e.g. Maria reading totals), use multiple `<p>` inside one `prose` block.
- Do **not** create a new `.talk` class; do not copy purple/gold boxes from reference PNGs unless they match an existing shared pattern (`analogy-box` is for **Scenario**, not Talk).

Optional: wrap Maria’s **inventory table** (from an asset) in the same `section-card` below her line, using `acct-line` / `acct-total` — still not a custom Talk style.

---

## 7) Button to proceed (progressive reveal)

Format in md:

```text
Button to proceed: Maria reads it back
```

Meaning:

- Render a **single primary button** with that exact label.
- **Initially hide** all markdown content **after** this button until the **next** `Button to proceed:` (or end of page).
- On click: **show** that hidden block; hide or disable the button that was clicked (optional: scroll into view).

### HTML / CSS pattern (shared only)

- Button: `btn btn-primary` or `btn-continue`
- Hidden segment: wrap content in `interlude-panel` (hidden by default).
- On click: add class `show` to the target `interlude-panel` (same pattern as `interlude-panel.show` in `shared.css`).

Example structure:

```html
<!-- Visible up to first proceed button -->
<button type="button" class="btn btn-primary" id="proceed-1">Maria reads it back</button>

<div id="segment-after-proceed-1" class="interlude-panel">
  <!-- Everything from md after this button until the next "Button to proceed:" -->
  ...
  <button type="button" class="btn btn-primary" id="proceed-2">Finish up</button>
  <div id="segment-after-proceed-2" class="interlude-panel">
    ...
  </div>
</div>
```

JavaScript: small handler in the page script — `element.classList.add('show')`; no new CSS.

**Order rule:** First segment on load is everything **before** the first `Button to proceed:`. Each click reveals the next segment only.

---

## 8) Styling rules (strict)

- **Single source of truth:** `shared.css` (+ `shared.js` for `applyConfig`, `toggleRabbitHole`, etc.).
- Allowed building blocks include:  
  `text-block`, `section-tag`, `section-title`, `section-sub`, `prose`, `info-box`, `callout`, `analogy-box`,  
  `section-card`, `activity-card`, `activity-tag`, `activity-title`, `activity-desc`,  
  `insight-box`, `insight-label`, `interlude-panel`,  
  `dnd-*`, `mcq-*`, `btn-*`, `rabbit-hole*`, `accordion` / `acc-*`, `acct-*`, `delta-*`, etc.
- **Dark mode:** Rely on shared dark rules; no page-local `@media (prefers-color-scheme: dark)` unless approved.
- **Activities:** Full logic (drag, MCQ, check, reset, progress, feedback) — not placeholders.

---

## 9) `assets/` naming convention (recommended)

Place reference images in repo root:

```text
assets/
  1.2-table.png
  1.2-activity1.png
  1.2-activity2.png
  1.2-complete.png
```

In `page X.md`:

```text
Image: refer to assets/1.2-activity2
Activity 1
[refer to assets/1.2-activity1, once finished, users receive the feedback "..."]
```

Agent: resolve filename, read image, implement content in HTML.

---

## 10) Quick checklist (copy for each build)

- [ ] Read `Page design.md` then `page X.md`
- [ ] Resolve every `assets/...` reference and read the image file
- [ ] Rebuild **content** from images using **shared.css only**
- [ ] Scenarios → `section-tag` + `analogy-box`
- [ ] Talk → `section-card` + `insight-label` + `prose` (+ `section-sub` if needed)
- [ ] `Button to proceed` → `btn-primary` / `btn-continue` + `interlude-panel` reveal chain
- [ ] Activities complete with specified feedback text
- [ ] No new components, no page `<style>`, no embedding reference PNGs as final UI unless md requires it
- [ ] Test on localhost from repo root

---

## 11) Agent one-liner

Read `Page design.md` → read `page X.md` → load each `assets/*` reference image → implement `page X.html` in order using only `shared.css`, with Scenario (`analogy-box`), Talk (`section-card` + `insight-label` + `prose`), progressive sections (`interlude-panel` + proceed buttons), and asset-driven activity/content layouts.
