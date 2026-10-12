# soap-calculator

A cold-process soap recipe calculator in a single HTML file, in English, French and Arabic. Build an oil blend from 17 common soapmaking oils, set the superfat, water-to-lye ratio and essential oil rate, and it gives the exact weight of every ingredient in grams, plus the bar's predicted qualities (hardness, cleansing, lather and so on). Everything updates as you change any input.

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

### Language (English / Français / العربية)

The **EN | FR | عربي** switch at the top of the page changes the language.

French follows French number style: a comma for decimals, a space between thousands and before the percent sign (`1 434,27 g`, `33,3 %`). It uses French soapmaking terms such as *surgraissage* (superfat), *trace* and *cure*. French typography's narrow no-break space doesn't render in the page's main font, so numbers use a regular no-break space instead.

Arabic is a full right-to-left layout:

- the whole page is mirrored, with inputs on the right and the recipe on the left
- sliders fill from the right
- chart bars start at the right edge, and their 0% mark is on the right

Arabic text uses IBM Plex Sans Arabic, with Noto Kufi Arabic for headings.

Arabic numbers use Western digits (0–9) with English-style separators (`1,434.27`), so they match what kitchen scales show. Units are translated: g is غ, oz is أونصة, lb is رطل, and g/kg is غ/كغ.

On the first visit the page picks French or Arabic if the browser is set to one of them, and English otherwise. After that it remembers your choice.

### Batch parameters

| Control | Default | Range | Notes |
|---|---|---|---|
| Total oil weight | 1000 g | 50–20,000 g (slider 100–5,000 g) | Type it in g, oz or lb with the unit switch. Every calculation runs in grams. |
| Water to lye ratio | 2.0 : 1 | 1.5–3.0 : 1 | The lye concentration is shown under the slider (2:1 is 33.3%). |

### Oil percentages

The default blend is olive oil 80%, coconut oil (76°) 15% and castor oil 5%.

**Add an oil** opens a list of the oils not yet in the recipe. Each one shows its SAP value and how hard, cleansing and conditioning a soap made only from it would be, so you can see what it brings. A new oil starts at 0%, and the cursor moves to its percentage box. A recipe can have up to 8 oils. The **×** next to an oil removes it (the last oil can't be removed).

| Oil | NaOH SAP | Oil | NaOH SAP |
|---|---|---|---|
| Olive oil | 0.135 | Sweet almond oil | 0.139 |
| Coconut oil (76°) | 0.183 | Avocado oil | 0.133 |
| Castor oil | 0.128 | Sunflower oil | 0.135 |
| Shea butter | 0.128 | Sunflower oil (high oleic) | 0.135 |
| Cocoa butter | 0.138 | Rice bran oil | 0.133 |
| Mango butter | 0.136 | Hemp seed oil | 0.138 |
| Palm oil | 0.142 | Lard | 0.141 |
| Palm kernel oil | 0.176 | Beef tallow | 0.143 |
| Babassu oil | 0.175 | | |

All oil data (SAP, iodine, INS and fatty acids) comes from [SoapCalc's oil table](https://soapcalc.net/oil-list). Olive, coconut and castor match the SAP values this calculator started with.

Presets replace the blend with olive, coconut and castor:

| Preset | Olive / Coconut / Castor |
|---|---|
| Classic | 70 / 25 / 5 |
| High Olive | 80 / 15 / 5 |
| Castile Hybrid | 85 / 10 / 5 |
| Pure Castile | 100 / 0 / 0 |

The blend must total 100%. When it doesn't, a warning shows how far off it is, and the recipe, qualities and charts fade with a note explaining why. Two buttons fix it:

- **Scale to 100%** keeps the oils in proportion and scales them up or down.
- **Fill the gap** sets one oil to whatever is left and leaves the others alone. It uses olive oil when the recipe has it, otherwise the largest oil.

Above 10%, castor oil gets a warning under its slider: it tends to make a soft, sticky bar, and most recipes use 5–8%.

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

**Copy recipe** puts a plain-text version on the clipboard, in the current language. It is turned off while the oils don't total 100%. The English and French versions are laid out in columns, as below. The Arabic version uses one `label: value` line per ingredient, because space-padded columns don't line up in right-to-left text.

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

### Bar qualities

A card under the recipe predicts how the bar will behave, using [SoapCalc's method](https://soapcalc.net/soapqualities). Each quality has a meter: the shaded band is the usual range and the dot is your recipe. A label says whether the value is low, in range or high, and a line explains what that means for the bar.

| Quality | Usual range | Higher means |
|---|---|---|
| Hardness | 29–54 | a harder, longer-lasting bar |
| Cleansing | 12–22 | more oil-stripping; can feel drying |
| Conditioning | 44–69 | more moisturizing |
| Bubbly lather | 14–46 | more big, fluffy bubbles |
| Creamy lather | 16–48 | denser, more stable lather |
| Iodine | 41–70 | softer bar, more prone to rancidity |
| INS | 136–165 | harder bar; 160 is often cited as ideal |

Being outside a range isn't an error. An 80% olive bar, for example, is low on hardness and high on conditioning, which is why castile-style soaps need a long cure. **Show the fatty acid profile** lists the blend's eight fatty acids.

### Batch breakdown chart

- **Oil blend**: one bar per oil, showing its share of the total oil weight.
- **Wet batch**: a stacked bar of oils, water, lye and essential oil as shares of the total weight.

Point at an oil bar, a segment, a legend entry or a line in the recipe card to highlight the matching item and see its weight. Keyboard users can Tab through the wet-batch segments.

### Help

A **?** next to Water to lye ratio, Oil percentages, Superfat, Essential oil usage and Bar qualities opens a short explanation in place, with a **More in the guide** link to the matching part of the soapmaking guide.

### Undo

Changes that replace several values at once show a message at the bottom of the screen with an **Undo** button. This covers presets, Reset to defaults, adding or removing an oil, Scale to 100%, Fill the gap, and the guide's **Use** buttons. The message stays for 7 seconds. While it's showing, Ctrl+Z (⌘Z on a Mac) also undoes, except while you're typing in a number box. Moving a slider or typing a value doesn't show the message.

### Soapmaking guide

A reference section at the bottom of the page, in all three languages. The **Read the soapmaking guide** link under the page intro jumps to it. It has four parts:

- **Water to lye ratio**: what changing the amount of water does, and what to expect at 1.5, 2.0, 2.5 and 3.0 : 1, with each ratio's lye concentration.
- **Superfat by use**: suggested ranges for hair (shampoo bars, 4–5%), hands (4–6%), body (5–7%), and face or dry and sensitive skin (7–8%), with the reasons.
- **Essential oils**: lavender, rosemary, peppermint, lemon, sweet orange, tea tree, eucalyptus and cedarwood. Each card gives the scent, what it suits, what it pairs with, a typical rate in g/kg, and a note on how it behaves in soap. Each card also says whether your current rate is below, within or above the oil's typical range. A safety note covers IFRA limits, oils to keep out of children's soap, and oils to avoid.
- **How the numbers are calculated**: every formula in the order the calculator runs them, then the bar quality formulas, each worked with your current recipe, so the numbers change as you edit.

Most suggestions have a **Use** button that applies the value to the calculator, for example **Use 4.5%** on the hair row, and offers to undo it. When the calculator already uses that value, the button is replaced by **Current**. The essential oil buttons set the total rate, so if you blend several oils, the rate covers the whole blend.

The ranges are common starting points from soapmaking practice, not limits. For each essential oil's maximum, check your supplier's safety data or IFRA certificate.

### Saved state and reset

Your last recipe is saved in the browser's `localStorage` under the key `cp-soap-calculator:v1`, and your language choice under `cp-soap-calculator:lang`, so both are still there when you reopen the page. Recipes saved before oils could be added still load. The save is per browser and per device. If storage is blocked, for example in a private window, the calculator still works but starts from the defaults each time.

**Reset to defaults** restores 1000 g, olive/coconut/castor at 80/15/5, 5% superfat, 2:1 water and 30 g/kg essential oil. It does not change the language, and it can be undone.

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

Bar qualities use each oil's share of the blend (its percentage divided by the blend's total, so they stay meaningful while the oils don't add up to 100%). The blend's fatty acids are the share-weighted sum of each oil's fatty acids, and then:

| Quality | Formula |
|---|---|
| Hardness | lauric + myristic + palmitic + stearic |
| Cleansing | lauric + myristic |
| Conditioning | oleic + linoleic + linolenic + ricinoleic |
| Bubbly lather | lauric + myristic + ricinoleic |
| Creamy lather | palmitic + stearic + ricinoleic |
| Iodine | Σ (oil share × oil's iodine value) |
| INS | Σ (oil share × oil's INS value) |

Qualities are shown rounded to whole numbers, and the low / in range / high label uses the rounded value. At the defaults they come to Hardness 25, Cleansing 10, Conditioning 72, Bubbly 15, Creamy 20, Iodine 74 and INS 127.

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

### Change oil data, defaults or presets

Edit these constants near the top of the script:

- `OIL_LIBRARY`: every oil you can add, one `defineOil(key, sap, iodine, ins, [lauric, myristic, palmitic, stearic, ricinoleic, oleic, linoleic, linolenic])` line each
- `PRESETS`: preset buttons, as `{ id, pct: { oilKey: percent, … } }`
- `DEFAULTS`: the starting recipe, also used by **Reset to defaults**; its `blend` is a list of `{ key, pct }`
- `LIMITS`: the min, max and step for every slider and number box
- `MAX_OILS`: how many oils a recipe can have (8)
- `CASTOR_WARN_ABOVE`: the castor oil percentage that triggers its warning (10)
- `QUALITIES`: each bar quality's fatty acids, usual range (`min`, `max`) and meter `scale`

Display names are kept separately, in `STRINGS` (see [Edit or add translations](#edit-or-add-translations)).

### Change the guide

The guide's numbers and its text are kept separately:

- `WATER_GUIDE`: the ratios listed, as numbers
- `SUPERFAT_GUIDE`: each use as `{ id, min, max, use }`, where `use` is the value its **Use** button applies
- `ESSENTIAL_OILS`: each oil as `{ id, latin, min, max }`, with the typical rate in g/kg. Its **Use** button applies the midpoint, rounded to the nearest 5.

All the text is under `guide` in each `STRINGS` block. The water rows are a list in the same order as `WATER_GUIDE`. Superfat rows and oil cards are keyed by `id`. To add an essential oil, add an entry to `ESSENTIAL_OILS` and a matching entry under `guide.eo.oils` in every language.

### Edit or add translations

All visible text, including oil and preset names, units and screen-reader labels, is in the `STRINGS` object, with one block per language (`en`, `fr`, `ar`). Every block has the same keys. Some entries are functions because a number goes inside the sentence, for example ``totalShort: (p) => `${p} short of 100%` ``.

To change wording, edit the string in the right block. To add a language:

1. Copy the `en` block under a new code, for example `es`, and translate every value.
2. Add an entry to `NUMBER_FORMATS` with the language's number locale and percent sign, for example `es: { tag: 'es-ES', pctSign: '\u00a0%' }`.
3. Add it to the `options` list in `LanguageToggle`, and to the browser-language check in `loadLang` if it should be picked automatically.
4. If it is right-to-left, update the check in `applyLang` and `makeLocale` (currently `lang === 'ar'`) so it sets `dir="rtl"`.
5. If its script needs its own font, add the font to the Google Fonts link and override `--font-sans` and `--font-display` for it, as `:root[lang="ar"]` does.

Numbers in the interface go through the formatters from `makeLocale` (`fmtG`, `fmtN`, `fmtFixed`, `fmtPct`, `fmtW`, `fmtU`), so they follow each language's style. Use those rather than `toFixed` or a hard-coded `%` when you add text with numbers in it.

### Add an oil to the list

1. Add a `defineOil(...)` line to `OIL_LIBRARY` with its NaOH SAP value, iodine value, INS value and eight fatty acid percentages. SoapCalc's [oil table](https://soapcalc.net/oil-list) lists these for 150 oils; its SAP values there are KOH, so multiply by 40 / 56.1 for NaOH.
2. Add its name to `oils` in every `STRINGS` block, for example `jojoba: 'Jojoba oil'`, `jojoba: 'Huile de jojoba'` and `jojoba: 'زيت الجوجوبا'`.

It then appears in the **Add an oil** list, sorted by name in each language.

The chart colours are oils, water, lye and essential oil, tested as a set to stay distinct for colour-blind readers in both light and dark mode. Oils share one colour because each oil is labelled by name, so adding oils doesn't need new colours. If you change a colour, check it is still easy to tell apart from its neighbours.

### Theme

The page follows the system light or dark setting. Every colour is a CSS custom property (`--ground`, `--surface`, `--ink`, `--accent` and so on), defined once for light and again for dark. The Tailwind config maps these to class names such as `bg-surface`, `text-ink` and `border-line`, so changing a token restyles the whole page.

### Move it into a React + Tailwind project

1. Copy the script block into a component file such as `SoapCalculator.jsx`.
2. Replace `const { useState, useMemo, useEffect, useLayoutEffect, useRef, useId, createContext, useContext } = React;` with `import React, { useState, useMemo, useEffect, useLayoutEffect, useRef, useId, createContext, useContext } from 'react';`.
3. Replace the final `ReactDOM.createRoot(...)` line with `export default SoapCalculator;`.
4. Move everything in the `<style>` block into your global stylesheet: the colour and font tokens, the Arabic rules, the `.range` slider styles (including the `[dir="rtl"]` rule) and the number-input rules.
5. Copy the `colors` and `fontFamily` entries from the inline `tailwind.config` into your project's Tailwind config. The layout uses logical utilities (`ps-*`, `pe-*`, `ms-*`, `text-start`, `text-end`, `start-0`, `end-0`, `rounded-e-*`) and the `rtl:` variant, which need Tailwind 3.3 or newer.
6. Load the five Google Fonts (Bricolage Grotesque, Public Sans, IBM Plex Mono, IBM Plex Sans Arabic, Noto Kufi Arabic), or change the `--font-*` tokens.

The component sets `lang` and `dir` on the `<html>` element when the language changes (see `applyLang`). If your app already controls those attributes, move that logic into your app instead.

## Project structure

```
index.html   the whole app: markup, styles, Tailwind config, English, French and Arabic text, and the React component
README.md    this file
```

## Safety

Sodium hydroxide is caustic. Wear goggles and gloves, work somewhere ventilated, and always pour the lye into the water, never water onto lye. Check any new recipe against a second lye calculator before your first batch. This tool uses one typical SAP value per oil, and real oils vary by supplier and batch, which is one reason to keep a superfat. Bar qualities are predictions from typical fatty acid profiles, not measurements of your soap.
