# Japanese Typography Reference

Detailed reference for Japanese web typography. Read specific sections as needed.

## Table of Contents

1. [Font Stacks](#font-stacks)
2. [Line Height (JLREQ)](#line-height)
3. [Letter Spacing and OpenType Features](#letter-spacing)
4. [Text Emphasis (Bouten)](#text-emphasis)
5. [Kinsoku Shori (Line Breaking Rules)](#kinsoku-shori)
6. [Punctuation Spacing (Yakumono)](#punctuation-spacing)
7. [Mixing Japanese and Latin Text](#mixing-scripts)
8. [Full-Width vs Half-Width](#fullwidth-halfwidth)
9. [Vertical Text Deep Dive](#vertical-text)
10. [Ruby / Furigana](#ruby-furigana)
11. [Font Loading Optimization](#font-loading)
12. [Responsive Typography](#responsive-typography)
13. [Modern CSS Properties for CJK](#modern-css)

---

## Font Stacks

### Sans-Serif (Gothic / ゴシック体)

Use for UI, body text, apps, and most web content. Gothic fonts render clearly at small sizes on screens.

```css
/* Comprehensive Gothic stack */
:lang(ja) {
  font-family:
    "Inter", "Helvetica Neue", Arial,            /* Latin */
    "Hiragino Sans", "Hiragino Kaku Gothic ProN", /* macOS/iOS */
    "BIZ UDPGothic",                              /* Windows 10+ (Universal Design) */
    Meiryo,                                       /* Windows Vista+ */
    "Yu Gothic Medium",                           /* Windows 8.1+ */
    "Noto Sans JP",                               /* Cross-platform fallback */
    sans-serif;
}
```

### Serif (Mincho / 明朝体)

Use for literary content, formal documents, editorial design, and large decorative headings. Mincho has fine strokes that can render poorly at small pixel sizes.

```css
:lang(ja) .serif {
  font-family:
    "Georgia", "Source Serif 4",                  /* Latin */
    "Hiragino Mincho ProN",                       /* macOS/iOS */
    "Yu Mincho",                                  /* Windows 8.1+ */
    "BIZ UDPMincho",                              /* Windows 10+ */
    "Noto Serif JP",                              /* Cross-platform fallback */
    serif;
}
```

### Modern System-UI Shortcut

```css
body { font-family: system-ui, sans-serif; }
```

Yields Hiragino Sans on macOS/iOS, **Yu Gothic UI** (not Yu Gothic) on Windows 10+, Noto Sans CJK JP on Linux. Note: Yu Gothic UI is designed for UI (tighter metrics) while Yu Gothic is for documents (wider metrics). `system-ui` gives you the UI variant. Simple but less control over which variant you get.

### Ordering Rules

1. **Latin fonts before Japanese fonts** — Japanese fonts include Latin glyphs, but they are less refined. List Latin first so Latin characters use the Latin font.
2. **Write font names in English** — all modern browsers recognize English names for Japanese system fonts.
3. **End with the generic family** — `sans-serif` or `serif`.

### Google Fonts Options for Japanese

| Font | Style | Best For |
|------|-------|----------|
| Noto Sans JP | Gothic (sans) | Body text, UI — the gold standard |
| Noto Serif JP | Mincho (serif) | Editorial, formal |
| M PLUS 1p | Gothic (sans) | Rounded, friendly UI |
| M PLUS Rounded 1c | Rounded Gothic | Playful, casual |
| Shippori Mincho | Mincho (serif) | Literary, elegant |
| Sawarabi Gothic | Gothic (sans) | Clean, lightweight |
| Zen Kaku Gothic New | Gothic (sans) | Modern, geometric |
| Zen Maru Gothic | Rounded Gothic | Soft, approachable |

---

## Line Height

### Why CJK Needs More Line Height

CJK ideographic characters fill the **entire em-box** — they are designed as squares using nearly 100% of the vertical space. Latin lowercase letters use only 40-70% of the em-box. Japanese body text at `line-height: 1.5` appears significantly denser than Latin text at the same value.

### JLREQ Specification

The W3C JLREQ spec recommends line gap (行間 / gyoukan) between **0.5em and 1.0em**:
- `line-height: 1.5` = minimum (0.5em gap)
- `line-height: 2.0` = maximum (1.0em gap)
- Shorter lines (<25 chars): 1.5 is acceptable
- Longer lines (>35 chars): closer to 2.0

### Practical Values

```css
:lang(ja) { line-height: 1.7; }                    /* Standard body text */
:lang(ja) .long-form { line-height: 1.8; }          /* Articles, novels */
:lang(ja) h1, :lang(ja) h2, :lang(ja) h3 { line-height: 1.4; }  /* Headings: tighter */
:lang(ja) .has-furigana { line-height: 2.0; }       /* Extra space for ruby annotations */
:lang(ja) .ui-compact { line-height: 1.5; }         /* Compact UI elements: minimum */
```

---

## Letter Spacing

### Default: Zero Extra Spacing

Japanese characters in "solid setting" (ベタ組 / beta-gumi) have zero extra space between character frames. This is already visually balanced. Unlike Latin text, Japanese body text generally does NOT need letter-spacing.

```css
:lang(ja) { letter-spacing: 0; }                     /* Default: no extra spacing for body text */
:lang(ja) .heading-accent { letter-spacing: 0.05em; } /* Slight tracking for decorative headings */
:lang(ja) .tight { letter-spacing: -0.02em; }         /* Tsumegumi: tighter proportional feel */
```

Note: some designers apply a tiny `letter-spacing: 0.02em` for screen readability, especially with Gothic fonts at small sizes. This is a stylistic preference, not a rule — pure beta-gumi (zero) is the typographic standard. The code-snippets in this skill use `0.02em` as a practical screen default.

### OpenType Features for Japanese

```css
/* Proportional alternates — better mixed-script spacing */
:lang(ja) { font-feature-settings: "palt" 1; }

/* Half-width alternates for punctuation (used by text-spacing-trim) */
:lang(ja) { font-feature-settings: "halt" 1; }

/* Contextual half-width spacing */
:lang(ja) { font-feature-settings: "chws" 1; }

/* Localized glyph forms (JP-specific variants of shared CJK codepoints) */
:lang(ja) { font-feature-settings: "locl" 1; }

/* For vertical text */
.tategaki { font-feature-settings: "vert" 1, "vkrn" 1; }

/* Combined for body text */
:lang(ja) { font-feature-settings: "palt" 1, "kern" 1; }
```

Key features:
- **`palt`** — Proportional Alternate Widths: adjusts individual glyph spacing for proportional rendering
- **`halt`** — Alternate Half Widths: makes full-width punctuation half-width (required for `text-spacing-trim`)
- **`chws`** — Contextual Half-width Spacing: context-aware punctuation width adjustment
- **`locl`** — Localized Forms: swaps glyphs for Japan-specific variants
- **`vert`**, **`vkrn`** — Vertical layout features for tategaki

**Caveat**: `palt` makes character widths proportional, which breaks monospace alignment. Do NOT apply `palt` to:
- `<code>`, `<pre>`, `<kbd>` — code blocks need fixed-width
- Table cells with numeric data — numbers should align vertically
- Any context where characters must align in columns

```css
:lang(ja) code, :lang(ja) pre, :lang(ja) kbd {
  font-feature-settings: "palt" 0;
}
```

**Important**: `font-feature-settings` declarations override each other — they do not merge. Always combine features in a single declaration:
```css
/* WRONG: second line overrides the first */
:lang(ja) { font-feature-settings: "palt" 1; }
:lang(ja) { font-feature-settings: "kern" 1; }  /* palt is now OFF */

/* CORRECT: combine in one declaration */
:lang(ja) { font-feature-settings: "palt" 1, "kern" 1; }
```

---

## Text Emphasis

Japanese uses **bouten (傍点/圏点)** — small marks above characters — instead of italic for emphasis. This is equivalent to italic/bold in Western typography.

```css
:lang(ja) em {
  font-style: normal;                    /* CRITICAL: disable italic */
  text-emphasis: filled sesame;          /* ﹅ sesame dots — most traditional */
  text-emphasis-position: over right;    /* above in horizontal, right in vertical */
}
```

### Available Marks

| Value | Filled | Open |
|-------|--------|------|
| `dot` | • | ◦ |
| `circle` | ● | ○ |
| `double-circle` | ◉ | ◎ |
| `triangle` | ▲ | △ |
| `sesame` | ﹅ | ﹆ |

Default: `circle` in horizontal, `sesame` in vertical. Custom characters are also supported: `text-emphasis: "★"`.

Browser support: Widely available since March 2022.

---

## Kinsoku Shori

Kinsoku shori (禁則処理) is the set of Japanese line-breaking rules from JIS X 4051.

### Characters That Cannot START a Line (行頭禁則)

Closing punctuation, small kana, prolonged sound marks:
```
）〕】」』〉》〙〗» 、。，．・：；？！ ー～…‥
ぁぃぅぇぉっゃゅょゎ ァィゥェォッャュョヮ ヵヶ 々〻
```

### Characters That Cannot END a Line (行末禁則)

Opening punctuation:
```
（〔【「『〈《〘〖«
```

### CSS Implementation

```css
:lang(ja) {
  line-break: strict;          /* Enforce full kinsoku rules */
  overflow-wrap: anywhere;     /* Break long unbreakable strings */
  word-break: normal;          /* Standard CJK character-by-character breaking */
}
```

**`line-break` values:**
- `loose` — Relaxed rules. Allows breaks before small kana and ー. Good for narrow columns.
- `normal` — Standard rules.
- `strict` — Full kinsoku. Prohibits breaks before small kana and ー. Best for formal text.

**`word-break: auto-phrase` (Chrome 119+):**

Uses machine learning (BudouX) to detect natural phrase boundaries in Japanese:

```css
@supports (word-break: auto-phrase) {
  :lang(ja) h1, :lang(ja) h2, :lang(ja) h3 {
    word-break: auto-phrase;
    text-wrap: balance;
  }
}
```

Without this, a heading like "窓ぎわのトットちゃん" might break at any character boundary. With it, the browser identifies natural phrase breaks.

---

## Punctuation Spacing

Japanese punctuation (yakumono / 約物) — periods (。), commas (、), brackets (「」) — are full-width characters. The glyph occupies only half the em-square; the other half is built-in whitespace. When punctuation marks appear consecutively (e.g., 」「), this creates excessive visual gaps.

### CSS `text-spacing-trim` (Chrome 123+)

```css
:lang(ja) { text-spacing-trim: normal; }
```

This collapses spacing between adjacent punctuation following JLREQ rules. Requires fonts with OpenType `halt` or `chws` features (Noto Sans/Serif CJK, Yu Gothic, Yu Mincho, Hiragino Sans all support this).

**Fallback for unsupported browsers:**

```css
@supports not (text-spacing-trim: normal) {
  :lang(ja) {
    font-feature-settings: "halt" 1, "chws" 1;
  }
}
```

---

## Mixing Scripts

### The Size Problem

CJK characters fill the entire em-square. Latin lowercase uses only 40-70%. At the same `font-size`, Japanese text appears significantly larger than adjacent Latin text.

### Solutions

1. **List Latin fonts first in font-family** — the browser uses the Latin font for ASCII characters and the Japanese font for CJK. This naturally pairs the two.

2. **`text-autospace: normal`** — auto-inserts ~1/8em spacing at CJK/Latin boundaries (Baseline 2025, widely supported).

3. **For fine-tuning mixed inline text:**
```css
.mixed-text span[lang="en"] {
  font-size: 1.1em;
  vertical-align: -0.05em;
}
```

### Font Pairing Recommendations

| Latin Font | Japanese Font | Style |
|-----------|---------------|-------|
| Inter | Noto Sans JP | Clean, modern |
| Helvetica Neue | Hiragino Sans | Apple ecosystem |
| Source Serif 4 | Noto Serif JP | Editorial |
| Georgia | Hiragino Mincho ProN | Literary |
| JetBrains Mono | Noto Sans Mono CJK JP | Code |

---

## Full-Width vs Half-Width

### Rules

| Context | Width | Example |
|---------|-------|---------|
| Japanese text (kanji, hiragana, katakana) | Full-width | 東京タワー |
| Latin letters/digits in technical context | Half-width | user@example.com |
| Japanese punctuation | Full-width | 。、「」 |
| Half-width katakana | **Avoid** | ｶﾀｶﾅ (legacy only) |

### Input Normalization

```javascript
// Convert full-width alphanumerics to half-width
function toHankaku(str) {
  return str.replace(/[Ａ-Ｚａ-ｚ０-９]/g, (s) =>
    String.fromCharCode(s.charCodeAt(0) - 0xFEE0)
  );
}
```

Best practice: Accept both widths and normalize server-side. Never force IME mode switches.

---

## Vertical Text

### Core Implementation

```css
.tategaki {
  writing-mode: vertical-rl;
  text-orientation: mixed;
  line-height: 1.75;
  letter-spacing: 0.05em;
  font-feature-settings: "vert" 1, "vkrn" 1;

  max-block-size: 80vh;      /* logical: max-width in vertical-rl */
  overflow-block: auto;       /* logical: overflow-x in vertical-rl */
}
```

### Tate-Chu-Yoko (縦中横)

Horizontal text within vertical flow — for numbers and short Latin strings:

```css
.tcy { text-combine-upright: all; }
```

```html
<p class="tategaki">令和<span class="tcy">25</span>年<span class="tcy">12</span>月</p>
```

### Text Orientation Values

| Value | CJK | Latin | Use Case |
|-------|-----|-------|----------|
| `mixed` (default) | Upright | Rotated 90° | Standard Japanese with mixed scripts |
| `upright` | Upright | Upright (stacked) | All characters standing up |
| `sideways` | Rotated | Rotated | All characters on their side |

### Responsive Vertical/Horizontal Switching

```css
.article { writing-mode: horizontal-tb; }

@media (min-width: 768px) and (min-height: 600px) {
  .article.vertical-enabled {
    writing-mode: vertical-rl;
    max-block-size: 80vh;
    overflow-block: auto;
  }
}
```

### Use Logical Properties

| Logical | horizontal-tb | vertical-rl |
|---------|---------------|-------------|
| `inline-size` | width | height |
| `block-size` | height | width |
| `margin-block-start` | margin-top | margin-right |
| `padding-inline` | padding-left/right | padding-top/bottom |

---

## Ruby / Furigana

### HTML Structure

```html
<ruby>漢<rp>(</rp><rt>かん</rt><rp>)</rp>字<rp>(</rp><rt>じ</rt><rp>)</rp></ruby>
```

Always include `<rp>` for fallback. The deprecated `<rb>` and `<rtc>` elements should NOT be used.

### CSS

```css
:lang(ja) ruby {
  ruby-position: over;
  ruby-align: space-between;
}

:lang(ja) rt {
  font-size: 0.5em;
  font-weight: normal;
  line-height: 1;
  letter-spacing: 0;
}

/* Increase container line-height to prevent ruby collisions */
.has-furigana { line-height: 2.0; }

/* Toggleable furigana for learning interfaces */
.hide-furigana rt { visibility: hidden; }
.hide-furigana ruby:hover rt { visibility: visible; }
```

---

## Font Loading

### The Problem

A single Japanese font weight: 5-20 MB. Full Noto Sans JP (7 weights): ~32 MB.

### Strategy 1: Google Fonts (Recommended)

Google auto-splits into ~120 slices using ML-based frequency analysis. Typical page loads: 100-500 KB instead of megabytes.

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;700&display=swap" rel="stylesheet">
```

### Strategy 2: Self-Hosting with Subsetting

```bash
# Create subset using pyftsubset
pyftsubset NotoSansJP-Regular.otf \
  --unicodes="U+3040-309F,U+30A0-30FF,U+4E00-9FFF,U+3000-303F,U+FF00-FFEF" \
  --flavor=woff2 \
  --output-file=NotoSansJP-subset.woff2
```

Use `unicode-range` in `@font-face` to let the browser download only needed slices.

### font-display Strategy

| Value | Recommendation |
|-------|---------------|
| `swap` | Headings — visible layout shift is less noticeable |
| `optional` | Body text on performance-critical pages — prevents layout shift |
| `fallback` | Good compromise |
| `block` | **Avoid** for Japanese — fonts too large for invisible-text duration |

### Variable Fonts

Noto Sans CJK Variable exists (~8 MB OTF, ~4 MB WOFF2). One file covers all weights (Thin–Black), replacing seven 4.6 MB static files. Only `wght` axis available.

```css
@font-face {
  font-family: 'Noto Sans JP VF';
  src: url('/fonts/NotoSansJP-VF.woff2') format('woff2-variations');
  font-weight: 100 900;
  font-display: swap;
}
```

---

## Responsive Typography

### clamp() for Japanese

```css
:root {
  --jp-body:  clamp(1rem, 0.95rem + 0.25vw, 1.125rem);
  --jp-h1:    clamp(1.75rem, 1.5rem + 1.25vw, 2.5rem);
  --jp-h2:    clamp(1.5rem, 1.3rem + 1vw, 2rem);
  --jp-h3:    clamp(1.25rem, 1.1rem + 0.75vw, 1.75rem);
  --jp-small: clamp(0.8125rem, 0.75rem + 0.3vw, 0.9375rem);
}
```

### Minimum Sizes

```css
:lang(ja) { font-size: max(1rem, 16px); }
:lang(ja) small { font-size: max(0.75rem, 12px); }
```

---

## Modern CSS Properties

### Browser Support (March 2026)

| Property | Chrome | Firefox | Safari |
|----------|--------|---------|--------|
| `text-autospace` | 120+ | Yes | 18.4+ |
| `text-spacing-trim` | 123+ (partial) | No | No |
| `word-break: auto-phrase` | 119+ | No | No |
| `hanging-punctuation` | No | No | 10+ |
| `text-emphasis` | 99+ | 46+ | 7+ |
| `text-combine-upright: all` | 48+ | 48+ | 5.1+ |
| `writing-mode: vertical-rl` | 48+ | 41+ | 5.1+ |
| `text-orientation` | 48+ | 41+ | 5.1+ |
| `line-break` | 58+ | 69+ | 11+ |
| Logical properties | 87+ | 66+ | 14.1+ |

Always use `@supports` for newer properties and provide sensible fallbacks.
