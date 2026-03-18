# Japanese UI Patterns Reference

Comprehensive reference for Japanese-specific UI/UX patterns, form design, UX copy, and cultural conventions.

## Table of Contents

1. [Form Design](#form-design)
2. [UX Copy and Politeness](#ux-copy)
3. [Layout Conventions](#layout-conventions)
4. [Color and Seasonal Themes](#color-themes)
5. [Mobile Design for Japan](#mobile-design)
6. [Payment Methods](#payment-methods)
7. [Navigation Patterns](#navigation-patterns)
8. [Accessibility (JIS X 8341-3)](#accessibility)

---

## Form Design

### Name Fields (お名前)

Japanese forms require up to three name components:

**1. Full Name (氏名 / お名前)**
- Family name (姓 *sei*) FIRST, given name (名 *mei*) SECOND
- Always use **separate fields** for sei and mei
- Label clearly: 姓 and 名, or お名前（姓）and お名前（名）

**2. Furigana (フリガナ / ふりがな)**
- REQUIRED field — kanji names have multiple valid pronunciations
- Staff need furigana to correctly read/pronounce customer names
- Most forms require **katakana** furigana (フリガナ), some accept hiragana (ふりがな)
- Label: フリガナ（セイ）and フリガナ（メイ）

**3. Romaji (ローマ字)** — optional
- Latin-alphabet romanization
- Needed for credit card processing and international documents

**Validation patterns:**
```javascript
// Full-width katakana only (for furigana fields)
const katakanaRegex = /^[ァ-ヾー\s]*$/;

// Full-width characters (kanji + kana)
const zenkakuRegex = /^[一-龯ぁ-んァ-ヾー々\s]*$/;
```

### Address Fields (住所)

Japanese addresses run largest → smallest (opposite of Western):

```
〒150-0001
東京都渋谷区神宮前1-2-3 ABCマンション401号室
```

**Standard form structure:**

| Field | Label | Content | Width |
|-------|-------|---------|-------|
| 1 | 郵便番号 (postal code) | 〒 XXX-XXXX (7 digits) | Narrow (~8em) |
| 2 | 都道府県 (prefecture) | Dropdown: 47 prefectures | Medium |
| 3 | 市区町村 (city/ward) | Auto-filled from postal code | Medium |
| 4 | 町名 (district) | Auto-filled from postal code | Wide |
| 5 | 番地 (block/lot number) | Manual entry | Medium |
| 6 | 建物名・部屋番号 (building/room) | Optional, manual entry | Wide |

**Critical UX: Postal code auto-fill**

This is standard on virtually all Japanese web forms. When the user enters a 7-digit postal code, fields 2-4 auto-populate. Use libraries like AjaxZip3 or jpostal.

```html
<label>郵便番号</label>
<input type="text" name="postal_code" inputmode="numeric"
       pattern="[0-9]{3}-?[0-9]{4}"
       placeholder="例: 150-0001"
       data-autofill="address">
```

### Phone Number (電話番号)

Use **three separate input fields**:

| Type | Field 1 | Field 2 | Field 3 | Total |
|------|---------|---------|---------|-------|
| Mobile | 090/080/070 | 4 digits | 4 digits | 11 digits |
| Landline | 2-5 digit area code | 1-4 digits | 4 digits | 10 digits |

```html
<div class="phone-fields">
  <input type="tel" inputmode="numeric" maxlength="3" placeholder="090"> -
  <input type="tel" inputmode="numeric" maxlength="4" placeholder="1234"> -
  <input type="tel" inputmode="numeric" maxlength="4" placeholder="5678">
</div>
```

Validation: `/^(070|080|090)-?\d{4}-?\d{4}$/` for mobile.

### Date Format (日付)

**Format: YYYY年MM月DD日**

- Always Year-Month-Day order
- Use **dropdown selects** for year — never free text
- Support both Gregorian (西暦) and Imperial era (和暦)

**Imperial Era (和暦) reference:**

| Era | Japanese | Start | Example |
|-----|----------|-------|---------|
| Reiwa | 令和 | 2019-05-01 | 2026 = 令和8年 |
| Heisei | 平成 | 1989-01-08 | 2018 = 平成30年 |
| Showa | 昭和 | 1926-12-25 | 1988 = 昭和63年 |
| Taisho | 大正 | 1912-07-30 | Rare, but some elderly users' birth dates |

Note: The first year of any era is written 元年 (*gannen*), not 1年.

Government and school forms strongly favor the era system.

### Time Format (時刻)

- 24-hour format is common in digital/technical contexts: 14:30
- 12-hour format uses 午前 (AM) / 午後 (PM): 午後2時30分
- Time suffixes: 時 (hour), 分 (minute), 秒 (second)

```javascript
// Format time in Japanese
function formatTimeJa(date) {
  const h = date.getHours();
  const m = date.getMinutes();
  const period = h < 12 ? '午前' : '午後';
  const h12 = h % 12 || 12;
  return `${period}${h12}時${m}分`;
}
```

### Number Formatting (数字)

Japanese uses a 10,000-based grouping system (万進法), not 1,000-based:

| Number | Western | Japanese | Reading |
|--------|---------|----------|---------|
| 10,000 | 10K | 1万 | ichiman |
| 100,000 | 100K | 10万 | juuman |
| 1,000,000 | 1M | 100万 | hyakuman |
| 100,000,000 | 100M | 1億 | ichioku |
| 1,000,000,000,000 | 1T | 1兆 | itchou |

```javascript
// Format number in Japanese units
function formatNumberJa(n) {
  if (n >= 1_0000_0000) return (n / 1_0000_0000).toFixed(1).replace('.0', '') + '億';
  if (n >= 1_0000) return (n / 1_0000).toFixed(1).replace('.0', '') + '万';
  return n.toLocaleString('ja-JP');
}
```

When displaying large numbers in Japanese context, use 万/億 units instead of raw digits for readability. E.g., display "1.5億円" not "150,000,000円".

```html
<select name="year_era">
  <option value="">--</option>
  <option value="reiwa">令和</option>
  <option value="heisei">平成</option>
  <option value="showa">昭和</option>
</select>
<select name="year_number">
  <!-- 1-99 or 元 for first year -->
</select>
<span>年</span>
<select name="month"><!-- 1-12 --></select>
<span>月</span>
<select name="day"><!-- 1-31 --></select>
<span>日</span>
```

### Currency (通貨)

- Symbol: **¥** (yen)
- **No decimal places** — yen has no subunit
- Comma separators for thousands: ¥1,000,000
- Display: ¥ symbol precedes the amount
- In Japanese text context: 1,000円 (using 円 suffix)

```javascript
// Format yen
function formatYen(amount) {
  return '¥' + amount.toLocaleString('ja-JP');
}
// Or in Japanese text:
function formatEn(amount) {
  return amount.toLocaleString('ja-JP') + '円';
}
```

### Email Field (メールアドレス)

```html
<label>メールアドレス</label>
<input type="email" inputmode="email" placeholder="例: example@mail.com"
       autocomplete="email">

<!-- Common pattern: confirmation field -->
<label>メールアドレス（確認）</label>
<input type="email" inputmode="email" placeholder="確認のため再入力してください">
```

Japanese forms almost always include a confirmation email field.

### Gender Field (性別)

Include a third option or make it optional — increasingly standard in Japan:

```html
<label>性別</label>
<select name="gender">
  <option value="">選択してください</option>
  <option value="male">男性</option>
  <option value="female">女性</option>
  <option value="other">その他</option>
  <option value="prefer_not_to_say">回答しない</option>
</select>
```

### Form Flow: Confirmation Screen (確認画面)

Japanese forms almost always follow a three-step flow. This is a critical pattern that Western forms often skip:

```
入力 (Input) → 確認 (Confirm) → 完了 (Complete)
ステップ 1/3    ステップ 2/3    ステップ 3/3
```

**Step 1: 入力 (Input)** — The user fills out the form.
**Step 2: 確認 (Confirm)** — A read-only summary of ALL entered data is displayed. The user reviews everything before submitting. Include:
- All form data displayed clearly (not editable)
- 「戻る」(Back/Edit) button to return to input
- 「送信する」(Submit) button for final submission
- Step indicator showing progress (ステップ 2/3)

**Step 3: 完了 (Complete)** — Confirmation that the form was submitted successfully. Include:
- Success message: 送信が完了しました
- Reference number if applicable
- What happens next (e.g., confirmation email notification)
- 「トップページへ戻る」(Return to top page) link

This confirmation step builds trust and prevents submission errors. Skipping it feels foreign to Japanese users.

### Social Login Ordering

In Japan, LINE is as important as Google for social login. Display order:

1. LINE (green, dominant messaging app)
2. Google
3. Apple
4. Twitter/X
5. Facebook (less prominent in Japan)

### Privacy Policy and Terms

Japanese sites prominently display consent mechanisms:
- プライバシーポリシー (Privacy Policy)
- 利用規約 (Terms of Use)
- 個人情報の取り扱い (Handling of Personal Information)

Often implemented as checkboxes before form submission, not just footer links.

---

## UX Copy

### Politeness Register

| Level | Name | Usage | Example |
|-------|------|-------|---------|
| Polite | 丁寧語 | All standard UI | 送信しました (Sent) |
| Respectful | 尊敬語 | Addressing user's actions | ご確認ください (Please confirm) |
| Humble | 謙譲語 | Brand describing its own actions | お送りいたします (We will send) |
| Casual | タメ口 | Only for explicitly casual brands | 送ったよ！(Sent!) |

**Default: Always use 丁寧語 (polite) with です/ます endings.**

### Common Button Labels

| Action | Japanese | Reading |
|--------|----------|---------|
| Submit | 送信する | soushin suru |
| Confirm | 確認する | kakunin suru |
| Register | 登録する | touroku suru |
| Cancel | キャンセル | kyanseru |
| Next | 次へ | tsugi e |
| Back | 戻る | modoru |
| Login | ログイン | roguin |
| Logout | ログアウト | rogauto |
| Search | 検索 | kensaku |
| Save | 保存する | hozon suru |
| Delete | 削除する | sakujo suru |
| Edit | 編集する | henshuu suru |
| Close | 閉じる | tojiru |
| Apply | 適用する | tekiyou suru |
| Download | ダウンロード | daunroodo |
| Upload | アップロード | appuroodo |
| See More | もっと見る | motto miru |
| Add to Cart | カートに入れる | kaato ni ireru |
| Purchase | 購入する | kounyuu suru |

### User Addressing

| Context | Suffix | Usage |
|---------|--------|-------|
| E-commerce, formal | 〜様 (*-sama*) | Order confirmations, formal emails |
| General customer reference | お客様 | "Honored customer" — universal |
| SaaS, community | 〜さん (*-san*) | Friendly but respectful |
| Internal tools | Name only or 〜さん | Company culture dependent |

### Error Messages

Pattern: Apologetic prefix + explanation + suggestion

```
恐れ入りますが、入力内容にエラーがあります。
以下の項目をご確認ください。

(We apologize, but there is an error in your input.
Please check the following items.)
```

Common error patterns:
- `必須項目です` — This field is required
- `正しい形式で入力してください` — Please enter in the correct format
- `○文字以内で入力してください` — Please enter within ○ characters
- `パスワードが一致しません` — Passwords do not match

### Success / Confirmation States

- `完了しました` — Completed
- `ありがとうございます` — Thank you (polite)
- `登録が完了しました` — Registration complete
- `送信しました` — Sent successfully
- `保存しました` — Saved

### Empty States

- `データがありません` — No data available
- `まだ投稿がありません` — No posts yet
- `検索結果が見つかりませんでした` — No search results found
- `お気に入りはまだ登録されていません` — No favorites registered yet

### Loading States

- `読み込み中...` — Loading...
- `処理中...` — Processing...
- `しばらくお待ちください` — Please wait a moment

---

## Layout Conventions

### Why Japanese Sites Are Information-Dense

- **Cultural trust signal**: Exhaustive information upfront signals trustworthiness
- **Linguistic density**: Kanji compounds convey in 2 characters what takes entire English phrases
- **A/B tested**: Major Japanese sites (Rakuten, Yahoo Japan) consistently find dense layouts outperform cleaner alternatives in conversion
- **Omotenashi**: Hospitality means anticipating every user question before it's asked

### Modern Approach: Bento-Style Layouts

Content blocks inspired by bento boxes — organized, modular density rather than chaotic density. This is the evolution of Japanese web design: structured density, not stripped minimalism.

### Key Patterns

- **Show all options** — Japanese users prefer seeing all navigation at once rather than hidden behind hamburger menus
- **Mega-menus** dominate desktop sites
- **Detailed footers** serve as secondary navigation
- **Trust indicators** everywhere: company info, certifications, customer counts

---

## Color and Seasonal Themes

### Japanese Color Associations

| Color | Association | Notes |
|-------|------------|-------|
| Red (赤) | Celebration, vitality, protection | Torii gates, national flag |
| White (白) | Purity, cleanliness | Also used for funerals — dual meaning |
| Black (黒) | Formality, power, elegance | Linked to ink painting (墨) |
| Blue/Indigo (藍) | Sophistication, depth | Historically the commoners' color |
| Pink (桃色) | Cherry blossoms, spring, transience | More gender-neutral than in the West |
| Gold (金) | Prosperity, authority | Buddhist significance |

### Seasonal Color Themes (四季)

Japanese brands routinely update visual themes per season:

| Season | Period | Colors | Motifs |
|--------|--------|--------|--------|
| Spring (春) | Mar-May | Soft pinks, light greens | Cherry blossoms (桜) |
| Summer (夏) | Jun-Aug | Indigo, cool blues, white | Water, fireworks, wind chimes |
| Autumn (秋) | Sep-Nov | Deep reds, golds, burnt orange | Maple leaves (紅葉), harvest moon |
| Winter (冬) | Dec-Feb | White, silver, deep blue | Snow, plum blossoms, New Year |

### Japanese Aesthetic Concepts

- **Wabi-sabi (侘び寂び)**: Beauty in imperfection and impermanence. Different from minimalism — embraces the incomplete and weathered.
- **Ma (間)**: Meaningful negative space. An intentional, active element of design, not mere emptiness.
- **Iki (粋)**: Sophisticated, understated elegance. Implies cultural knowledge and refined taste.

---

## Mobile Design

### Market Context

Japan is heavily mobile-dominant: ~87% smartphone penetration, 50%+ of purchases via mobile, ~70% mobile content market share.

### Flick Input (Kana Keyboard)

The dominant Japanese mobile input uses a 12-key layout:
- Tap a consonant group (あ, か, さ, etc.)
- Flick in one of 4 directions to select the vowel
- IME offers kanji conversion suggestions

**UX implications:**
- Text input takes longer — minimize required typing
- Auto-complete and predictive text are critical
- Postal code auto-fill is essential, not optional
- Keyboard occupies significant screen space — keep critical UI visible above keyboard
- Search suggestions should appear early

### Form Input Hints

```html
<!-- Numeric input — suppresses IME -->
<input type="text" inputmode="numeric" pattern="[0-9]*">

<!-- Email — ensures half-width keyboard -->
<input type="email" inputmode="email">

<!-- Phone — numeric pad -->
<input type="tel" inputmode="tel">

<!-- Japanese text — activates IME -->
<input type="text" inputmode="text">
```

Ensure `font-size: 16px` minimum on all inputs to prevent iOS auto-zoom on focus.

---

## Payment Methods

### Konbini Payment (コンビニ決済)

Second most-used online payment in Japan (~10% of purchases). Users pay cash at convenience stores.

**Flow:**
1. Customer selects "コンビニ決済" at checkout
2. Chooses convenience store chain (7-Eleven, Lawson, FamilyMart, etc.)
3. System generates payment code + deadline (typically 3-7 days)
4. Customer visits store, pays in cash using the code
5. Order ships after payment confirmation

**Design requirements:**
- Clearly display payment deadline
- Support selecting from multiple convenience store chains
- Generate scannable barcode or memorable reference number
- Send confirmation email with all payment details
- Handle asynchronous nature (order now, pay later)

### Other Japanese Payment Methods

| Method | Description |
|--------|-------------|
| PayPay | QR code mobile payment — market leader |
| LINE Pay | Integrated with LINE messaging app |
| Suica/PASMO | NFC transit cards used for payments |
| JCB | Japan-origin credit card, widely used domestically |
| Bank transfer (振込) | Direct bank transfer |
| Carrier billing | Charged to mobile phone bill |
| Pay-easy | Bank ATM/online banking payment |

---

## Navigation Patterns

### Desktop

- **Mega-menus** dominate — showing all categories at once
- **Multiple navigation layers** with rich sub-categories
- **Breadcrumb trails** widely used
- **Footer mega-navigation** — detailed footers as secondary nav
- **Social integration**: LINE, X (Twitter), Instagram

### Mobile

- **Tab bars** (bottom navigation) preferred over hamburger menus
- Japanese users want to see all options at once
- **Bottom sheet modals** for complex interactions

---

## Accessibility

### JIS X 8341-3:2016

Japan's web accessibility standard, aligned with WCAG 2.0. As of April 2024, private-sector entities are legally required to provide reasonable accommodations. Non-compliance may result in penalties up to ¥200,000.

### Japanese-Specific Accessibility Rules

**1. Language switching for mixed-language content:**
```html
<p>この<span lang="en">website</span>は日本語です。</p>
```
Without `lang="en"`, screen readers try to pronounce English using Japanese phonetics.

**2. Do not insert spaces within Japanese words for styling:**
Writing `日　時` instead of `日時` causes screen readers to pronounce characters individually and incorrectly.

**3. Kanji misreadings by screen readers:**
Generally leave them alone. Only intervene when a misreading could convey a completely different meaning. Adding furigana (`<ruby>`) helps when critical.

**4. Alt text in Japanese:**
Use natural Japanese sentence structures in polite (です/ます) form.

**5. Contrast and focus states:**
Follow standard WCAG AA minimums — same as any language.

**6. Screen readers used in Japan:**
- VoiceOver (iOS/macOS) — dominant on Apple devices
- NVDA — popular open-source screen reader
- PC-Talker — Japan-specific commercial screen reader
- JAWS — enterprise environments
