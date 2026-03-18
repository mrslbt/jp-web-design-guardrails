# Japanese Design — Production CSS Snippets

Copy-paste-ready CSS for Japanese web interfaces. Each snippet is self-contained and production-tested.

## Table of Contents

1. [Complete Japanese Baseline](#baseline)
2. [Heading Hierarchy](#headings)
3. [Mixed Japanese/English Content](#mixed-content)
4. [Vertical Text Sidebar](#vertical-sidebar)
5. [Form Input Styling](#form-styling)
6. [Ruby / Furigana](#ruby-styling)
7. [Japanese Card Component](#card-component)
8. [Error / Success States](#state-messages)

---

## Complete Japanese Baseline

Apply this as the foundation for any Japanese web project:

```css
/* === Japanese Web Typography Baseline === */

/* 1. Font stack */
:lang(ja) {
  font-family:
    "Inter", "Helvetica Neue", Arial,
    "Hiragino Sans", "Hiragino Kaku Gothic ProN",
    "BIZ UDPGothic", Meiryo, "Yu Gothic Medium",
    "Noto Sans JP",
    sans-serif;
}

/* 2. Core typography */
:lang(ja) {
  line-height: 1.7;
  letter-spacing: 0.02em;
  font-feature-settings: "palt" 1, "kern" 1;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* 3. Line breaking rules */
:lang(ja) {
  line-break: strict;
  overflow-wrap: anywhere;
  word-break: normal;
}

/* 4. Modern CJK spacing (progressive enhancement) */
@supports (text-autospace: normal) {
  :lang(ja) { text-autospace: normal; }
}

@supports (text-spacing-trim: normal) {
  :lang(ja) { text-spacing-trim: normal; }
}

@supports (word-break: auto-phrase) {
  :lang(ja) p { word-break: auto-phrase; }
}

/* 5. Emphasis: NO italic for Japanese */
:lang(ja) em {
  font-style: normal;
  text-emphasis: filled sesame;
  text-emphasis-position: over right;
}

/* Only target <i> used for text emphasis, not icon fonts */
:lang(ja) i:not([class]) {
  font-style: normal;
  font-weight: bold;
}

/* 6. Quotation marks: corner brackets */
:lang(ja) q { quotes: "「" "」" "『" "』"; }

/* 7. Ruby annotations */
:lang(ja) ruby {
  ruby-position: over;
  ruby-align: space-between;
}

:lang(ja) rt {
  font-size: 0.5em;
  font-weight: normal;
  line-height: 1;
}

/* 8. Underline positioning */
:lang(ja) a {
  text-underline-position: under;
  text-underline-offset: 0.15em;
  text-decoration-thickness: 1px;
}

/* 9. Paragraph indentation (standard in Japanese prose) */
:lang(ja) .prose p + p {
  text-indent: 1em;
}

/* 10. Selection styling */
:lang(ja) ::selection {
  background-color: rgba(0, 100, 200, 0.2);
}
```

---

## Heading Hierarchy

```css
/* === Japanese Heading System === */

:lang(ja) h1,
:lang(ja) h2,
:lang(ja) h3,
:lang(ja) h4,
:lang(ja) h5,
:lang(ja) h6 {
  font-family:
    "Hiragino Sans", "Hiragino Kaku Gothic ProN",
    "Noto Sans JP", "Yu Gothic Medium",
    sans-serif;
  line-height: 1.4;
  letter-spacing: 0.04em;
  font-feature-settings: "palt" 1;
}

@supports (word-break: auto-phrase) {
  :lang(ja) h1,
  :lang(ja) h2,
  :lang(ja) h3 {
    word-break: auto-phrase;
    text-wrap: balance;
  }
}

:lang(ja) h1 {
  font-size: clamp(1.75rem, 1.5rem + 1.25vw, 2.5rem);
  font-weight: 900;
  letter-spacing: 0.06em;
}

:lang(ja) h2 {
  font-size: clamp(1.375rem, 1.2rem + 0.875vw, 2rem);
  font-weight: 700;
  letter-spacing: 0.04em;
}

:lang(ja) h3 {
  font-size: clamp(1.125rem, 1rem + 0.625vw, 1.5rem);
  font-weight: 700;
}

:lang(ja) h4 {
  font-size: clamp(1rem, 0.95rem + 0.25vw, 1.25rem);
  font-weight: 700;
}

/* Decorative Mincho heading variant */
:lang(ja) .heading-mincho {
  font-family:
    "Hiragino Mincho ProN", "Yu Mincho",
    "Noto Serif JP", serif;
  font-weight: 600;
  letter-spacing: 0.08em;
}
```

---

## Mixed Japanese/English Content

```css
/* === Mixed-Script Content === */

.mixed-content {
  font-family:
    "Inter",
    "Hiragino Sans", "Hiragino Kaku Gothic ProN",
    "Noto Sans JP", Meiryo,
    sans-serif;
  font-size: clamp(0.9375rem, 0.875rem + 0.3vw, 1.0625rem);
  line-height: 1.8;
  letter-spacing: 0.02em;
  overflow-wrap: anywhere;
  word-break: normal;
  line-break: normal;  /* normal (not strict) allows more flexible breaks around Latin text */
}

@supports (text-autospace: normal) {
  .mixed-content { text-autospace: normal; }
}

/* Code within Japanese text */
:lang(ja) code,
:lang(ja) pre,
:lang(ja) kbd {
  font-family:
    "JetBrains Mono", "Source Code Pro",
    "Noto Sans Mono CJK JP",
    monospace;
  font-size: 0.9em;
  font-feature-settings: "palt" 0;  /* Disable proportional widths — code needs fixed-width */
  text-autospace: no-autospace;
}

/* When you need Google Fonts for both */
.mixed-google-fonts {
  font-family:
    "Roboto",
    "Noto Sans JP",
    sans-serif;
}
```

---

## Vertical Text Sidebar

```css
/* === Vertical Text Sidebar === */

.vertical-sidebar {
  writing-mode: vertical-rl;
  text-orientation: mixed;

  /* Sizing */
  block-size: 300px;
  max-inline-size: 80vh;
  overflow-inline: auto;

  /* Typography */
  font-family:
    "Hiragino Mincho ProN", "Yu Mincho",
    "Noto Serif JP", serif;
  font-size: clamp(0.9375rem, 0.875rem + 0.25vw, 1.0625rem);
  line-height: 2;
  letter-spacing: 0.1em;
  font-feature-settings: "vert" 1, "vkrn" 1;

  /* Spacing */
  padding-block: 2rem;
  padding-inline: 1.5rem;

  /* Decoration */
  border-inline-start: 3px solid #333;
  background: #faf8f5;
}

.vertical-sidebar .tcy {
  text-combine-upright: all;
}

/* Responsive: horizontal on mobile */
@media (max-width: 767px) {
  .vertical-sidebar {
    writing-mode: horizontal-tb;
    text-orientation: initial;
    block-size: auto;
    max-inline-size: 100%;
    overflow-inline: visible;
    border-inline-start: none;
    border-block-start: 3px solid #333;
  }
}
```

---

## Form Input Styling

```css
/* === Japanese Form Styling === */

:lang(ja) input,
:lang(ja) textarea,
:lang(ja) select {
  font-family:
    "Hiragino Sans", "Hiragino Kaku Gothic ProN",
    Meiryo, "Noto Sans JP",
    sans-serif;
  font-size: max(1rem, 16px);     /* 16px minimum prevents iOS zoom */
  line-height: 1.5;
  letter-spacing: 0.02em;
  padding: 0.625em 0.875em;
  border: 1px solid #ccc;
  border-radius: 4px;
  transition: border-color 0.2s;
}

:lang(ja) input:focus,
:lang(ja) textarea:focus,
:lang(ja) select:focus {
  outline: 2px solid #0066cc;
  outline-offset: 1px;
  border-color: #0066cc;
}

/* Labels */
:lang(ja) label {
  font-size: max(0.875rem, 14px);
  font-weight: 700;
  display: block;
  margin-block-end: 0.375em;
}

/* Required indicator */
:lang(ja) .required::after {
  content: "必須";
  font-size: 0.75em;
  font-weight: 700;
  color: #fff;
  background: #d32f2f;
  padding: 0.1em 0.4em;
  border-radius: 2px;
  margin-inline-start: 0.5em;
  vertical-align: middle;
}

/* Optional indicator */
:lang(ja) .optional::after {
  content: "任意";
  font-size: 0.75em;
  font-weight: 700;
  color: #666;
  background: #eee;
  padding: 0.1em 0.4em;
  border-radius: 2px;
  margin-inline-start: 0.5em;
  vertical-align: middle;
}

/* Placeholder */
:lang(ja) input::placeholder,
:lang(ja) textarea::placeholder {
  color: #999;
  font-size: 0.875em;
}

/* Hint text below input */
:lang(ja) .input-hint {
  font-size: max(0.75rem, 12px);
  color: #666;
  margin-block-start: 0.25em;
}

/* Error state */
:lang(ja) .field-error input,
:lang(ja) .field-error textarea,
:lang(ja) .field-error select {
  border-color: #d32f2f;
  background: #fff5f5;
}

:lang(ja) .error-message {
  font-size: max(0.75rem, 12px);
  color: #d32f2f;
  margin-block-start: 0.25em;
}

/* Name fields layout */
:lang(ja) .name-fields {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

/* Phone number fields */
:lang(ja) .phone-fields {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

:lang(ja) .phone-fields input {
  text-align: center;
}

/* Postal code with auto-fill indicator */
:lang(ja) .postal-code-group {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

:lang(ja) .postal-code-group .prefix {
  font-weight: 700;
  color: #333;
}

/* Textarea: needs more height for Japanese */
:lang(ja) textarea {
  min-block-size: 8em;
  line-height: 1.8;
  resize: vertical;
}

/* Checkbox / Radio in Japanese forms */
:lang(ja) .checkbox-group label,
:lang(ja) .radio-group label {
  display: flex;
  align-items: center;
  gap: 0.5em;
  font-weight: 400;
  cursor: pointer;
  padding: 0.375em 0;
}
```

---

## Ruby / Furigana Styling

```css
/* === Ruby / Furigana === */

/* Base ruby */
:lang(ja) ruby {
  ruby-position: over;
  ruby-align: space-between;
}

:lang(ja) rt {
  font-size: 0.5em;
  font-weight: normal;
  line-height: 1;
  letter-spacing: 0;
  font-style: normal;
  text-decoration: none;
}

/* Container needs extra line-height for ruby */
:lang(ja) .has-furigana {
  line-height: 2.0;
}

/* Toggleable furigana (for learning interfaces) */
:lang(ja) .furigana-toggle rt {
  visibility: hidden;
  transition: visibility 0.15s;
}

:lang(ja) .furigana-toggle ruby:hover rt,
:lang(ja) .furigana-toggle ruby:focus rt,
:lang(ja) .furigana-toggle.show-furigana rt {
  visibility: visible;
}

/* Vertical ruby */
:lang(ja) .tategaki ruby {
  ruby-position: over;  /* renders to the right in vertical-rl */
}
```

---

## Japanese Card Component

```css
/* === Japanese Content Card === */

:lang(ja) .card {
  font-family:
    "Hiragino Sans", "Hiragino Kaku Gothic ProN",
    "Noto Sans JP", Meiryo, sans-serif;
  background: #fff;
  border: 1px solid #e5e5e5;
  border-radius: 8px;
  padding: 1.5rem;
  line-height: 1.7;
}

:lang(ja) .card-title {
  font-size: clamp(1.125rem, 1rem + 0.5vw, 1.375rem);
  font-weight: 700;
  line-height: 1.4;
  letter-spacing: 0.02em;
  margin-block-end: 0.5em;
}

:lang(ja) .card-meta {
  font-size: max(0.75rem, 12px);
  color: #666;
  margin-block-end: 0.75em;
}

:lang(ja) .card-body {
  font-size: max(0.875rem, 14px);
  color: #333;
}

:lang(ja) .card-body p + p {
  margin-block-start: 0.75em;
}

:lang(ja) .card-tag {
  display: inline-block;
  font-size: max(0.6875rem, 11px);
  padding: 0.15em 0.5em;
  background: #f0f0f0;
  border-radius: 3px;
  margin-inline-end: 0.375em;
  margin-block-end: 0.375em;
}
```

---

## Error / Success State Messages

```css
/* === Japanese State Messages === */

:lang(ja) .message {
  font-family:
    "Hiragino Sans", "Hiragino Kaku Gothic ProN",
    "Noto Sans JP", Meiryo, sans-serif;
  padding: 1em 1.25em;
  border-radius: 6px;
  font-size: max(0.875rem, 14px);
  line-height: 1.7;
}

/* Error */
:lang(ja) .message-error {
  background: #fff5f5;
  border: 1px solid #ffcdd2;
  color: #c62828;
}

/* Success */
:lang(ja) .message-success {
  background: #f1f8e9;
  border: 1px solid #c5e1a5;
  color: #33691e;
}

/* Warning */
:lang(ja) .message-warning {
  background: #fff8e1;
  border: 1px solid #ffe082;
  color: #e65100;
}

/* Info */
:lang(ja) .message-info {
  background: #e3f2fd;
  border: 1px solid #90caf9;
  color: #0d47a1;
}

/* Optional: apologetic prefix for errors (use when error text doesn't already include an apology) */
:lang(ja) .message-error-with-prefix::before {
  content: "恐れ入りますが、";
  font-weight: 700;
}
```
