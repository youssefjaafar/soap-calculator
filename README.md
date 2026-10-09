# soap-calculator

A cold-process soap recipe calculator in a single HTML file, in English and Arabic. Enter your oil blend, superfat, water-to-lye ratio and essential oil rate, and it gives the exact weight of every ingredient in grams, updating as you change any input.

Built with React 18, Tailwind CSS (Play CDN) and in-browser JSX via Babel standalone. There is no build step and nothing to install.

## Running it

Open `index.html` in any modern browser (double-clicking the file works).

An internet connection is needed on first load: React, ReactDOM, Babel and Tailwind come from public CDNs, and the fonts come from Google Fonts.

To serve it locally instead, run any static file server from the project folder, for example:

```sh
npx serve .
# or
python3 -m http.server 8000
```

## Using the calculator

The page has two columns. Inputs are on the left and the recipe is on the right. On a phone the columns stack, and a bar at the bottom of the screen keeps the lye, water and total weights in view while you scroll.

### Language (English / العربية)

The **EN | عربي** switch at the top of the page changes the language. Arabic is a full right-to-left layout:

- the whole page is mirrored, with inputs on the right and the recipe on the left
- sliders fill from the right
- chart bars start at the right edge, and their 0% mark is on the right

Arabic text uses IBM Plex Sans Arabic, with Noto Kufi Arabic for headings.

Numbers use Western digits (0–9) in both languages, so they match what kitchen scales show. Units are translated: g is غ, oz is أونصة, lb is رطل, and g/kg is غ/كغ.

On the first visit the page picks Arabic if the browser's language is Arabic, and English otherwise. After that it remembers your choice.

### Batch parameters

| Control | Default | Range | Notes |
|---|---|---|---|
| Total oil weight | 1000 g | 50–20,000 g (slider 100–5,000 g) | Type it in g, oz or lb with the unit switch. Every calculation runs in grams. |
| Water to lye ratio | 2.0 : 1 | 1.5–3.0 : 1 | The lye concentration is shown under the slider (2:1 is 33.3%). |

### Oil percentages

| Oil | Default | NaOH SAP |
|---|---|---|
| Olive oil | 80% | 0.135 |
| Coconut oil (76°) | 15% | 0.183 |
| Castor oil | 5% | 0.128 |

Presets set all three at once:

| Preset | Olive / Coconut / Castor |
|---|---|
| Classic | 70 / 25 / 5 |
| High Olive | 80 / 15 / 5 |
| Castile Hybrid | 85 / 10 / 5 |
| Pure Castile | 100 / 0 / 0 |

The blend must total 100%. When it doesn't, a warning shows how far off it is, and the recipe and charts fade with a note explaining why. Two buttons fix it:

- **Scale to 100%** keeps the oils in proportion and scales them up or down.
- **Fill the gap with olive oil** leaves coconut and castor alone and sets olive to whatever is left.

The slider moves in 1% steps. Type into the number box for tenths (for example 33.3%).

### Additives

| Control | Default | Range | Notes |
|---|---|---|---|
| Superfat | 5% | 4–8%, in 0.5% steps | The grams of lye it removes are shown under the slider. |
| Essential oil usage | 30 g/kg | 15–45 g/kg | Grams per kilogram of oil. 30 g/kg is 3% of the oil weight. |

### Recipe output

The recipe card lists:

- each oil, with its grams, its percentage, and how much NaOH it needs before superfat
- the oil subtotal
- NaOH lye and distilled water, with the lye before superfat and the lye concentration
- essential oil
- the total wet batch weight

The "% of oils" column also covers lye, water and essential oil (13.5%, 27% and 3% at the defaults), because many soapmakers check those figures.

When the batch is entered in oz or lb, each weight also shows in that unit underneath the grams.

**Copy recipe** puts a plain-text version on the clipboard, in the current language. It is turned off while the oils don't total 100%. The English version is laid out in columns, as below. The Arabic version uses one `label: value` line per ingredient, because space-padded columns don't line up in right-to-left text.

```
Cold-process soap recipe
1,000.00 g oils · 5% superfat · 2.0:1 water to lye

OILS
Olive oil 80%                   800.00 g
Coconut oil (76°) 15%           150.00 g
Castor oil 5%                    50.00 g

LYE SOLUTION
NaOH lye                        134.76 g
Distilled water                 269.52 g

ADDITIVES
Essential oil (30 g/kg)          30.00 g

Total wet batch               1,434.27 g
```

### Batch breakdown chart

Two stacked bars:

- **Oil blend**: each oil's share of the total oil weight.
- **Wet batch**: oils, water, lye and essential oil as shares of the total weight.

Point at a segment, a legend entry or a line in the recipe card to highlight the matching item and see its weight. Keyboard users can Tab through the segments.

### Saved state and reset

Your last recipe is saved in the browser's `localStorage` under the key `cp-soap-calculator:v1`, and your language choice under `cp-soap-calculator:lang`, so both are still there when you reopen the page. The save is per browser and per device. If storage is blocked, for example in a private window, the calculator still works but starts from the defaults each time.

**Reset to defaults** restores 1000 g, 80/15/5, 5% superfat, 2:1 water and 30 g/kg essential oil. It does not change the language.

## How the numbers are calculated

| Value | Formula |
|---|---|
| Oil weight (g) | total oil weight × oil % / 100 |
| Unadjusted lye (g) | Σ (oil weight × NaOH SAP) |
| NaOH lye (g) | unadjusted lye × (1 − superfat / 100) |
| Distilled water (g) | NaOH lye × water-to-lye ratio |
| Essential oil (g) | total oil weight × usage rate / 1000 |
| Total wet batch (g) | all oils + NaOH lye + distilled water + essential oil |
| Lye concentration | NaOH lye / (NaOH lye + water), which is 1 / (1 + ratio) |

Weights are displayed to two decimal places. Calculations are not rounded along the way.

### Worked example (defaults)

| Step | Calculation | Result |
|---|---|---|
| Olive oil | 1000 × 80 / 100 | 800.00 g |
| Coconut oil | 1000 × 15 / 100 | 150.00 g |
| Castor oil | 1000 × 5 / 100 | 50.00 g |
| Unadjusted lye | 800 × 0.135 + 150 × 0.183 + 50 × 0.128 = 108.00 + 27.45 + 6.40 | 141.85 g |
| NaOH lye | 141.85 × (1 − 0.05) | 134.76 g |
| Distilled water | 134.7575 × 2 | 269.52 g |
| Essential oil | 1000 × 30 / 1000 | 30.00 g |
| Total wet batch | 1000 + 134.7575 + 269.515 + 30 | 1,434.27 g |

## Customising

All of the code is in the `<script type="text/babel">` block in `index.html`, and the colours are in the `<style>` block above it.

### Change SAP values, defaults or presets

Edit these constants near the top of the script:

- `OILS`: each oil's `key`, NaOH `sap` value and chart `color`
- `PRESETS`: preset buttons, as `{ id, pct: { olive, coconut, castor } }`
- `DEFAULTS`: the starting recipe, also used by **Reset to defaults**
- `LIMITS`: the min, max and step for every slider and number box

Display names are kept separately, in `STRINGS` (see [Edit or add translations](#edit-or-add-translations)). If you change an SAP value, also update the sentence at the bottom of the page: it is the `footer` string in both languages.

### Edit or add translations

All visible text, including oil and preset names, units and screen-reader labels, is in the `STRINGS` object, with one block for `en` and one for `ar`. Both blocks have the same keys. Some entries are functions because a number goes inside the sentence, for example ``totalShort: (p) => `${p} short of 100%` ``.

To change wording, edit the string in the right block. To add a language:

1. Copy the `en` block under a new code, for example `fr`, and translate every value.
2. Add it to the `options` list in `LanguageToggle`.
3. If it is right-to-left, update the check in `applyLang` and `makeLocale` (currently `lang === 'ar'`) so it sets `dir="rtl"`.
4. If its script needs its own font, add the font to the Google Fonts link and override `--font-sans` and `--font-display` for it, as `:root[lang="ar"]` does.

### Add an oil

1. Add an entry to `OILS`, for example `{ key: 'shea', sap: 0.128, color: 'var(--s-shea)' }`.
2. Add its name to `oils` in every `STRINGS` block, for example `shea: 'Shea butter'` and `shea: 'زبدة الشيا'`.
3. Define `--s-shea` in the `:root` block and in both dark-mode blocks (`@media (prefers-color-scheme: dark)` and `:root[data-theme="dark"]`).
4. Add the new key to `DEFAULTS.pct` and to every preset's `pct`.
5. Update **Fill the gap with olive oil**. It assumes the three original oils: see `canBalance` in `BlendStatus` and the `onBalance` handler in `SoapCalculator`.

Saved recipes from before the change still load. An oil missing from the saved data falls back to its default percentage.

The chart colours were picked as a set and tested to stay distinct for colour-blind readers, in this order: olive, coconut, castor, water, lye, essential oil. If you add or change a colour, check that it is still easy to tell apart from its neighbours in both light and dark mode.

### Theme

The page follows the system light or dark setting. Every colour is a CSS custom property (`--ground`, `--surface`, `--ink`, `--accent` and so on), defined once for light and again for dark. The Tailwind config maps these to class names such as `bg-surface`, `text-ink` and `border-line`, so changing a token restyles the whole page.

### Move it into a React + Tailwind project

1. Copy the script block into a component file such as `SoapCalculator.jsx`.
2. Replace `const { useState, useMemo, useEffect, useLayoutEffect, useRef, createContext, useContext } = React;` with `import React, { useState, useMemo, useEffect, useLayoutEffect, useRef, createContext, useContext } from 'react';`.
3. Replace the final `ReactDOM.createRoot(...)` line with `export default SoapCalculator;`.
4. Move everything in the `<style>` block into your global stylesheet: the colour and font tokens, the Arabic rules, the `.range` slider styles (including the `[dir="rtl"]` rule) and the number-input rules.
5. Copy the `colors` and `fontFamily` entries from the inline `tailwind.config` into your project's Tailwind config. The layout uses logical utilities (`ps-*`, `pe-*`, `ms-*`, `text-start`, `text-end`, `start-0`, `end-0`, `rounded-e-*`) and the `rtl:` variant, which need Tailwind 3.3 or newer.
6. Load the five Google Fonts (Bricolage Grotesque, Public Sans, IBM Plex Mono, IBM Plex Sans Arabic, Noto Kufi Arabic), or change the `--font-*` tokens.

The component sets `lang` and `dir` on the `<html>` element when the language changes (see `applyLang`). If your app already controls those attributes, move that logic into your app instead.

## Project structure

```
index.html   the whole app: markup, styles, Tailwind config, English and Arabic text, and the React component
README.md    this file
```

## Safety

Sodium hydroxide is caustic. Wear goggles and gloves, work somewhere ventilated, and always pour the lye into the water, never water onto lye. Check any new recipe against a second lye calculator before your first batch. This tool uses only the three SAP values listed above, and real oils vary by supplier.
