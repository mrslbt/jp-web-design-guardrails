# jp-web-design-guardrails

Guardrails for building Japanese-ready web interfaces with correct CJK typography, cultural UI patterns, and localization best practices.

## Install

```bash
claude plugin install https://github.com/mrslbt/jp-web-design-guardrails
```

## What It Does

When you ask Claude Code to build anything involving Japanese — a landing page, a form, a component — this skill automatically activates and ensures the output follows professional Japanese web standards.

### Typography
- Correct font stacks (Gothic/Mincho with Latin-first ordering)
- CJK line-height rules (JLREQ spec)
- Kinsoku shori (line-breaking prohibition rules)
- Text emphasis marks (bouten) instead of italic
- Punctuation spacing (`text-spacing-trim`, yakumono)
- Ruby/furigana annotations
- Vertical text (tategaki) with tate-chu-yoko
- CJK font loading optimization (Google Fonts auto-slicing)
- Modern CSS: `text-autospace`, `word-break: auto-phrase`, logical properties

### Forms
- Name fields: family name first (姓/名) + furigana (フリガナ)
- Address: postal code auto-fill, prefecture-to-building order
- Phone: three-field layout (090-XXXX-XXXX)
- Dates: YYYY年MM月DD日, Imperial era support (令和/平成/昭和)
- Currency: yen formatting (¥, no decimals, comma separators)
- Confirmation screen flow: 入力 → 確認 → 完了

### UX Copy
- Politeness registers (丁寧語/敬語/謙譲語)
- 50+ common Japanese button labels and UI strings
- Error, success, empty, and loading state patterns
- User addressing conventions (さん/様/お客様)

### Cultural Patterns
- Information density conventions (bento-style layouts)
- Seasonal color themes (四季)
- Social login ordering (LINE first)
- Payment methods (konbini, PayPay)
- Privacy policy and terms patterns

### Accessibility
- JIS X 8341-3 compliance
- Screen reader rules for mixed Japanese/English content
- Kanji misreading handling

### Production CSS
- 8 copy-paste-ready CSS snippets (baseline, headings, forms, vertical text, cards, state messages)

## Usage

The skill triggers automatically when Claude detects Japanese-related context. You can also invoke it manually:

```
/jp-design
```

### Example prompts that activate the skill

- "Build a Japanese contact form"
- "Add Japanese language support to my site"
- "Create a landing page for a Tokyo restaurant"
- "Style this component for `lang="ja"`"

## File Structure

```
skills/jp-design/
├── SKILL.md                        Main instructions (280 lines)
└── references/
    ├── typography.md               Font stacks, line-height, CSS properties
    ├── ui-patterns.md              Forms, UX copy, cultural conventions
    └── code-snippets.md            Production-ready CSS snippets
```

## Author

**Marsel Bait** — Designer based in Tokyo
- [Portfolio](https://marselbait.replit.app/)
- [LinkedIn](https://www.linkedin.com/in/marselbait/)
- [GitHub](https://github.com/mrslbt)

## License

MIT
