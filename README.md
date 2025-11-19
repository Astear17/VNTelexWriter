# VN Telex Writer

The **VN Telex Writer** is a fast, single-file, web-based tool for typing Vietnamese text using the Telex input method. It is designed to be fully responsive, work offline, and include advanced logic to handle complex vowel combinations (like `uoiw -> ươi`) common in modern Vietnamese typesetting.

This project is deployed live on GitHub Pages at:

➡️ [Live Demo: VN Telex Writer](https://your-github-pages-link)

## ✨ Features

- **Single-File Application**: The entire editor (HTML, CSS/Tailwind, and JavaScript Telex Engine) is contained in one portable file.
- **Revised Telex Engine**: Implements advanced rules, fixing edge cases like `cuoiw` to correctly output `cười` and handling complex triphthongs.
- **Real-time Conversion**: Converts Telex input to Vietnamese characters immediately as you type within the word boundary.
- **Theme Toggle**: Supports Light and Dark mode.
- **Font Selection**: Allows users to choose from several modern, legible fonts.
- **Copy Functionality**: Easily copy the final converted text.
- **Telex On/Off Toggle**: Allows switching the conversion engine on or off instantly.

## ✍️ How to Use (Telex Rules)

The editor uses the standard Telex input method. The system relies on key combinations to input diacritics (tone marks) and vowel modifications (breves, horns, circumflexes).

### Vowel Modifications (Vowels with Hats/Horns)

| Vowel | Input Key | Example | Output |
|-------|-----------|---------|--------|
| ă     | aw        | banw    | băn    |
| â     | aa        | laam    | lâm    |
| ê     | ee        | teem    | têm    |
| ô     | oo        | tooi    | tôi    |
| ơ     | ow        | mow     | mơ     |
| ư     | uw or w   | tuw/tw  | tư     |
| đ     | dd        | ddien   | điên   |

### Tone Marks (Diacritics)

The tone mark is typed at the end of the word.

| Tone Mark | Name  | Input Key | Example | Output |
|-----------|-------|-----------|---------|--------|
| s         | Sắc (Acute) | s         | tas     | tá     |
| f         | Huyền (Grave) | f         | taf     | tạ     |
| r         | Hỏi (Hook)   | r         | tar     | tả     |
| x         | Ngã (Tilde)  | x         | tax     | tã     |
| j         | Nặng (Dot)   | j         | taj     | tại    |
| z         | Remove Tone   | z         | táz     | ta     |

## 💻 Technical Deep Dive: The Telex Engine

The core functionality resides in the `convertWordToVietnamese` JavaScript function, which uses a prioritized sequence of regular expressions and vowel placement logic to ensure correct conversion.

The conversion happens in two main phases:

### Phase 1: Vowel and Consonant Modification (Breves, Horns, Circumflex)
This phase handles the transformation of base vowels and the 'd' consonant using `o`, `e`, and `w` keys. Crucially, the engine applies special logic to handle complex vowel clusters before simple replacements.

| Rule Category               | Input Pattern       | Output Vowel    | Purpose                                      |
|-----------------------------|---------------------|-----------------|----------------------------------------------|
| **Complex Vowels (Fixes)**   | uo([a-zA-Z]*)w       | ươ + group      | Fixes the `cuoiw` issue. Converts complex sequences like `uoiw` to `ươi` (e.g., `tươi`). |
| **Special Vowel Clusters**   | uaw                 | ưa              | Correctly handles the `ua` cluster (e.g., `quaw` is an exception). |
| **Simple Breves/Horns**      | aw, ow, uw           | ă, ơ, ư         | Standard conversion for these three core vowels. |
| **Circumflex/Đ**            | aa, ee, oo, dd       | â, ê, ô, đ       | Standard conversion for "hatted" vowels and 'đ'. |

### Phase 2: Tone Application
After vowel modification, the engine checks the last character of the word for a tone key (`s`, `f`, `r`, `x`, `j`). If a tone key is found, it uses the `applyTone` function, which contains Smart Tone Placement Logic:

1. **Identify Vowels**: Finds all vowel indices within the word.
2. **One Vowel Rule**: If there is only one vowel (e.g., `lam`), the tone is placed there (e.g., `làm`).
3. **Complex Vowel Rule (Diphthongs/Triphthongs)**: For words with two or more vowels (e.g., `hoan`, `tươi`), the logic determines the "nuclear" vowel—the one that carries the tone—based on phonetic rules of open vs. closed syllables (i.e., whether the word ends in a vowel or a consonant).
    - Example (Two Vowels, Closed Syllable): In `hoan`, the tone is typically placed on the second vowel (`a`) to become `hoán`.
4. **Apply Tone**: Uses the CHAR_MAP (e.g., `aàáảãạ`) to apply the tone corresponding to the input key (0=Huyền, 1=Sắc, 2=Hỏi, 3=Ngã, 4=Nặng) to the determined nuclear vowel.

This layered approach ensures that complex sequences are handled correctly before the tone is applied, providing highly accurate conversion.

---

## 🔧 Development

To run the VN Telex Writer locally, clone the repository and open the `index.html` file in your browser.

```bash
git clone https://github.com/yourusername/vn-telex-writer.git
cd vn-telex-writer
open index.html
