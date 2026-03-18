---
name: jp-design
description: Build Japanese-ready web interfaces with correct CJK typography, cultural UI patterns, and localization best practices. Use this skill whenever building a website, app, or component for the Japanese market, when the UI includes Japanese text (kanji, hiragana, katakana), when adding Japanese language support or i18n/l10n for Japan, when designing forms for Japanese users, when implementing vertical text (tategaki), when working with CJK font loading, or when the user mentions Japanese, Japan, 日本語, nihongo, CJK, kanji, hiragana, katakana, furigana, or any Japanese-specific design requirement. Also trigger when localizing an existing product for Japan, adding a lang="ja" attribute, or handling yen currency formatting.
---

# Japanese UI/UX Design Skill

This skill ensures Claude builds web interfaces that are typographically correct, culturally appropriate, and production-ready for Japanese audiences. It covers typography, CSS, form design, UX copy, accessibility, and cultural patterns.

## When Building Any Japanese Interface

Always apply these foundational rules. They are non-negotiable for professional Japanese web output.

### 1. HTML Foundations

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
```

The `lang="ja"` attribute is critical. Without it, browsers cannot apply correct line-breaking (kinsoku shori), screen readers mispronounce everything, and CJK-specific CSS properties like `word-break: auto-phrase` will not work.

For multilingual sites, use `hreflang` for SEO:
```html
<link rel="alternate" hreflang="ja" href="https://example.com/ja/">
<link rel="alternate" hreflang="en" href="https://example.com/en/">
```

### 2. Use a Proper Japanese Font Stack

Always list Latin fonts BEFORE Japanese fonts. Japanese fonts include Latin glyphs, but they are less refined than dedicated Latin typefaces. The browser tries each font in order — Latin characters render in the Latin font, CJK characters fall through to the Japanese font.

**Sans-serif (Gothic) — use for UI, body text, apps:**
```css
font-family:
  "Inter", "Helvetica Neue", Arial,
  "Hiragino Sans", "Hiragino Kaku Gothic ProN",
  "BIZ UDPGothic", Meiryo, "Yu Gothic Medium",
  "Noto Sans JP",
  sans-serif;
```

**Serif (Mincho) — use for editorial, literary, formal content:**
```css
font-family:
  "Georgia", "Source Serif 4",
  "Hiragino Mincho ProN",
  "Yu Mincho", "BIZ UDPMincho",
  "Noto Serif JP",
  serif;
```

Why this order: Hiragino is macOS/iOS, BIZ UDPGothic and Meiryo are Windows, Noto is the cross-platform fallback.

### 3. Japanese Typography Essentials

These CSS rules should ALWAYS be applied to `:lang(ja)` content:

```css
:lang(ja) {
  line-height: 1.7;            /* CJK fills the full em-box; needs more breathing room than Latin's 1.5 */
  line-break: strict;           /* Enforce kinsoku shori: prohibit line-breaks before closing punctuation, small kana, etc. */
  overflow-wrap: anywhere;      /* Break long unbreakable strings (URLs) to prevent overflow */
  word-break: normal;           /* Standard CJK character-by-character breaking */
  font-feature-settings: "palt" 1; /* Proportional alternate widths — do NOT apply to <code>, <pre>, or table cells */
}

/* Progressive enhancement — wrap newer properties in @supports */
@supports (text-autospace: normal) {
  :lang(ja) { text-autospace: normal; }  /* Auto-insert spacing between CJK and Latin/numbers */
}
```

**For headings, add phrase-aware line breaking (Chrome 119+):**
```css
@supports (word-break: auto-phrase) {
  :lang(ja) h1, :lang(ja) h2, :lang(ja) h3 {
    word-break: auto-phrase;
    text-wrap: balance;
  }
}
```

**For punctuation kerning (Chrome 123+):**
```css
@supports (text-spacing-trim: normal) {
  :lang(ja) {
    text-spacing-trim: normal;  /* Collapse spacing between adjacent CJK punctuation */
  }
}
```

### 4. Never Use Italic for Japanese Emphasis

Japanese has no italic tradition. CJK characters rendered in italic look broken and wrong. Instead, use:

- **Emphasis dots (bouten/圏点)** — the traditional Japanese method:
```css
:lang(ja) em {
  font-style: normal;
  text-emphasis: filled sesame;
  text-emphasis-position: over right;
}
```

- **Bold weight** for strong emphasis
- **Brackets**  like 「」for quotation (not "curly quotes")

### 5. Minimum Font Sizes

Japanese characters have more visual complexity (strokes) than Latin letters. They become illegible at smaller sizes:

| Context | Latin minimum | Japanese minimum |
|---------|--------------|-----------------|
| Body text | 14px | 16px |
| Captions | 10px | 12px |
| Form labels | 12px | 14px |
| Form inputs | 16px | 16px (prevents iOS zoom) |

### 6. CJK Font Loading

CJK fonts are massive (5-20MB per weight). Never load a full CJK font file.

**Best approach: Use Google Fonts** — it auto-splits Japanese fonts into ~120 slices via `unicode-range`, so the browser only downloads characters actually used on the page:

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;700&display=swap" rel="stylesheet">
```

**For fixed strings (logos, hero text), use the `text=` parameter:**
```html
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP&text=東京タワー" rel="stylesheet">
```

For detailed font loading strategies, see `references/typography.md`.

---

## Japanese UI Patterns

### Forms

Japanese forms have specific conventions that differ from Western forms. Getting these wrong instantly signals "this wasn't built for Japan." Read `references/ui-patterns.md` for the full guide. The critical rules:

1. **Name fields**: Family name (姓) FIRST, given name (名) second. Always provide separate fields. Always include a furigana (フリガナ) field — kanji names have multiple possible readings.

2. **Address format**: Largest to smallest — postal code (〒) → prefecture (都道府県) → city → district → building. Implement postal code auto-fill (enter 7-digit postal code → auto-populate prefecture/city/district).

3. **Phone numbers**: Use three separate input fields (e.g., 090 - 1234 - 5678).

4. **Dates**: Year-Month-Day order (YYYY年MM月DD日). Support both Gregorian (西暦) and Imperial era (和暦: 令和) calendars. Use dropdowns for year selection.

5. **Currency**: ¥ symbol, no decimal places, comma separators (¥1,000,000).

6. **Input width handling**: Accept both full-width (全角) and half-width (半角) characters and normalize server-side. Never force users to switch IME modes.

7. **Confirmation screen (確認画面)**: Japanese forms almost always follow a three-step flow: 入力 (input) → 確認 (confirm/review) → 完了 (complete). After the user fills out the form, show a read-only summary of all entered data for review before final submission. Include 「戻る」(Back) and 「送信する」(Submit) buttons. This is expected, not optional.

### UX Copy and Politeness

Japanese UI copy must use the correct politeness register. The baseline for all UI text is **teineigo (丁寧語)** — polite form with です/ます endings.

| Context | Honorific Level | Example |
|---------|----------------|---------|
| General UI | 丁寧語 (polite) | 送信する (Submit) |
| Addressing users | 〜さん or 〜様 | 田中様 (Tanaka-sama) |
| Error messages | Polite + apologetic | 恐れ入りますが... (We apologize but...) |
| Success states | Polite | 完了しました (Completed) |
| Buttons | Verb or noun form | 検索 (Search), 次へ (Next), 戻る (Back) |

**Never use casual/informal language** in customer-facing UI unless the brand explicitly targets a young, casual audience.

### Cultural Considerations

- **Information density is intentional** in Japanese web design — dense layouts signal trustworthiness. Do not strip information away when building for Japan. Organize density using bento-style modular layouts.
- **Seasonal themes (四季)** — Japanese brands routinely update visuals per season: cherry blossoms in spring, indigo/cool blues in summer, maple reds in autumn, white/silver in winter.
- **Mascot characters (キャラクター)** — commonly used to soften error states and add personality. This is culturally normal, not childish.
- **White carries dual meaning** — purity AND funerals. Be aware in contexts where white-heavy design could evoke mourning.
- **Social login priority**: In Japan, LINE login is as important as Google login. Display order: LINE, Google, Apple, Twitter/X. Facebook is less prominent.
- **Privacy policy and terms**: Japanese sites prominently display プライバシーポリシー (privacy policy) and 利用規約 (terms of use) links, often with explicit checkboxes for consent.

Read `references/ui-patterns.md` for comprehensive cultural guidance.

---

## Vertical Text (Tategaki / 縦書き)

Use for literary content, stylistic headings, traditional poetry, or editorial layouts:

```css
.tategaki {
  writing-mode: vertical-rl;   /* top-to-bottom, columns right-to-left */
  text-orientation: mixed;      /* CJK upright, Latin sideways */
  line-height: 1.75;
  font-feature-settings: "vert" 1;
}
```

Key rules:
- Use **logical properties** (`inline-size`, `block-size`, `margin-block`) instead of physical ones — they adapt automatically to writing mode
- Use `text-combine-upright: all` for numbers that should stay horizontal (tate-chu-yoko / 縦中横): `<span class="tcy">12</span>月`
- Switch to horizontal on mobile when viewport height is insufficient
- For standard apps and websites, use horizontal writing (yokogaki / 横書き) — vertical is for specific design choices

See `references/typography.md` for the full vertical text implementation guide.

---

## Ruby / Furigana

Use `<ruby>` elements for reading aids on kanji:

```html
<ruby>漢<rp>(</rp><rt>かん</rt><rp>)</rp>字<rp>(</rp><rt>じ</rt><rp>)</rp></ruby>
```

Always include `<rp>` for fallback in browsers without ruby support.

```css
:lang(ja) rt {
  font-size: 0.5em;
  font-weight: normal;
}

/* Increase line-height when using ruby to prevent collisions */
.has-furigana {
  line-height: 2.0;
}
```

---

## Accessibility (JIS X 8341-3)

Japan's web accessibility standard (JIS X 8341-3:2016) aligns with WCAG 2.0. As of April 2024, private-sector entities are legally required to provide reasonable accessibility accommodations.

Key Japanese-specific accessibility rules:

1. **Wrap English text in `<span lang="en">`** so screen readers switch pronunciation engines correctly. Without this, VoiceOver and NVDA will try to read English using Japanese phonetics.

2. **Do not insert spaces within Japanese words for visual styling** — writing 日 時 instead of 日時 causes screen readers to pronounce characters individually and incorrectly.

3. **Kanji misreadings by screen readers are generally acceptable** — only intervene when a misreading could convey a completely different meaning. Adding furigana via `<ruby>` helps but is not always necessary.

4. **Alt text should be in natural Japanese** using polite (です/ます) form.

5. **Contrast ratios and focus states** follow the same WCAG AA minimums as any language.

---

## Reference Files

This skill includes detailed reference documents. Read them when you need deeper guidance:

- **`references/typography.md`** — Complete font stacks, line-height rules, letter-spacing, OpenType features, vertical text deep dive, font loading optimization, responsive typography, mixing Japanese and Latin text, punctuation spacing (yakumono), and text-emphasis marks.

- **`references/ui-patterns.md`** — Comprehensive Japanese form design (name fields, furigana, address format, phone numbers, dates, currency), honorific-aware UX copy patterns, seasonal color themes, cultural design conventions, mobile/flick input considerations, payment methods (konbini, PayPay), and accessibility (JIS X 8341-3) details.

- **`references/code-snippets.md`** — Production-ready CSS snippets: Japanese body text foundation, heading hierarchy, mixed Japanese/English content, vertical text sidebar, form input styling, ruby/furigana styling, and a modern CSS properties browser support table.

---

## Common Mistakes to Avoid

1. **Using italic for emphasis** — Japanese has no italic tradition. Use `text-emphasis` (bouten) or bold instead.
2. **Putting given name before family name** — In Japanese, family name comes first: 田中太郎 (Tanaka Taro), not Taro Tanaka.
3. **Using straight quotes " "** — Japanese uses corner brackets: 「」for single quotes, 『』for nested quotes.
4. **Loading full CJK font files** — A single weight can be 16MB. Always use subsetting or Google Fonts' auto-slicing.
5. **Using `line-height: 1.5` for Japanese body text** — Too tight. Use 1.7 minimum, 1.8 for long-form reading.
6. **Forcing half-width or full-width input** — Accept both and normalize server-side. Forcing IME mode changes frustrates users.
7. **Ignoring kinsoku shori** — Always set `line-break: strict` to prevent punctuation like 。or 」from appearing at the start of a line.
8. **Treating whitespace the same as Western design** — Japanese sentences have no spaces between words. Don't add them artificially. Use `text-autospace` for CJK/Latin boundaries instead.
9. **Forgetting the `lang="ja"` attribute** — Without it, browsers cannot apply CJK-specific typographic rules, screen readers mispronounce text, and CSS properties like `auto-phrase` fail silently.
10. **Using Western address or date formats** — Japanese addresses go from large to small (prefecture → city → building). Dates are YYYY年MM月DD日, never MM/DD/YYYY.
