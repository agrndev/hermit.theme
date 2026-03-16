# hermit — colorscheme specification
*A minimal, semantically-driven Neovim colorscheme — by the hermit.*

---

## 1. Design principles

| Principle | Description |
|---|---|
| **Minimalism** | Only essential syntax elements receive color. If a distinction does not aid understanding, it is not made. |
| **Semantic highlighting** | Colors represent meaning, not decoration. A parameter looks different from a local variable because they *are* different, not for visual variety. |
| **Consistency** | Similar constructs share similar visual weight. If two things feel different, they should look different. If they feel the same, they should look the same. |
| **Warmth** | The palette is anchored in warm earth tones. No pure whites, cool blues, or synthetic neons. |

### 1.1 Visual hierarchy

Brightness encodes importance. The eye naturally reads brighter elements first.

```
highest  →  keyword    (#C47433)  — control flow, language structure
high     →  symbol     (#B75433)  — function names, types
medium   →  literal    (#A8522D)  — values, constants
low      →  foreground (#D8D2C4)  — variables, operators, punctuation
faded    →  comment    (#7A7468)  — comments, doc text
hidden   →  background (#121110)  — canvas
```

---

## 2. Core palette

Six roles cover all primary syntax elements. Every color token in this spec maps to one of these.

| Role | Hex | HSL | Description |
|---|---|---|---|
| `background` | `#121110` | 20° 5% 7% | Main editor canvas |
| `foreground` | `#D8D2C4` | 38° 18% 81% | Default text — variables, operators, punctuation |
| `comment` | `#7A7468` | 33° 6% 44% | Non-code informational text |
| `keyword` | `#C47433` | 29° 57% 48% | Language control flow |
| `symbol` | `#B75433` | 22° 55% 45% | Functions, type names |
| `literal` | `#A8522D` | 20° 57% 41% | Constant values and literals |

> **Palette note:** The three active colors (`keyword`, `symbol`, `literal`) are a single hue family stepped down in brightness and shifted slightly toward red. This creates warmth without visual noise — the eye reads them as a coherent family, not as three competing signals.

### 2.1 Diagnostic palette

Three additional roles cover error, warning, and hint states. Each color carries semantic weight — the eye must distinguish a diagnostic from normal code instantly, even in peripheral vision.

| Role | Hex | HSL | Description |
|---|---|---|---|
| `error` | `#C44040` | 0° 50% 51% | Compile errors, type errors, invalid code |
| `warning` | `#8B7340` | 42° 37% 39% | Warnings, deprecations, possible errors |
| `hint` | `#7A7468` | 33° 6% 44% | Hints and informational messages (shares value with `comment`) |

> `error` is a desaturated brick red, `warning` is a muted amber. Both are warm enough to stay within the palette family while still reading unambiguously as status signals. `hint` reuses `comment` — it is the quietest diagnostic level and intentionally indistinct from non-code text.

### 2.2 UI surfaces

The editor shell is built from neutral tones derived from `background`. No color roles from the syntax palette appear in chrome — those are reserved for code.

| Token | Hex | Derivation | Usage |
|---|---|---|---|
| `ui.background` | `#121110` | = background | Main editor canvas |
| `ui.background.sidebar` | `#161513` | +2% lightness | File tree, panels |
| `ui.background.statusbar` | `#0E0D0C` | −2% lightness | Bottom status bar |
| `ui.background.float` | `#1C1A18` | +4% lightness | Autocomplete, hover docs |
| `ui.border` | `#2A2724` | +10% lightness | Panel separators, widget borders |
| `ui.selection` | `rgba(200,180,150, 0.12)` | warm overlay | Selected text |
| `ui.selection.inactive` | `rgba(200,180,150, 0.06)` | dimmed | Unfocused window selection |
| `ui.line.highlight` | `rgba(200,180,150, 0.05)` | very subtle | Current line background |
| `ui.cursor` | `#D8D2C4` | = foreground | Block/bar cursor fill |
| `ui.cursor.inactive` | `#7A7468` | = comment | Unfocused cursor |
| `ui.gutter.fg` | `#3A3530` | mid gray | Line numbers |
| `ui.gutter.fg.current` | `#7A7468` | = comment | Current line number |
| `ui.indent.guide` | `#2A2724` | = border | Indent guide lines |
| `ui.indent.guide.active` | `#3A3530` | one step up | Active scope guide |
| `ui.match.bracket` | `#C47433` | = keyword | Matching bracket pair |
| `ui.search.match` | `rgba(196,116,51, 0.25)` | keyword @ low alpha | Search result highlight |
| `ui.search.match.current` | `rgba(196,116,51, 0.50)` | keyword @ mid alpha | Active search result |

---

## 3. Syntax token mapping

Constructs are described as syntactic word types — language-level concepts independent of any editor or highlighting engine. Each entry lists the canonical node type as a cross-language reference name using the `@capture` vocabulary, which forms a common abstraction layer that grammars for individual languages map onto.

### 3.1 Keywords

All control-flow and structural keywords share `keyword`. Word-form operators that read like prose (`typeof`, `in`, `new`) blend into foreground to avoid over-coloring.

| Construct | Capture | Role | Examples |
|---|---|---|---|
| Control flow | `@keyword` | `keyword` | `if`, `else`, `for`, `while`, `return`, `break`, `continue` |
| Import / export | `@keyword.import` | `keyword` | `import`, `export`, `from`, `require` |
| Declaration | `@keyword.storage` | `keyword` | `const`, `let`, `var`, `function`, `class`, `enum`, `type`, `interface` |
| Modifier | `@keyword.modifier` | `keyword` | `static`, `readonly`, `abstract`, `public`, `private`, `protected`, `override` |
| Concurrency | `@keyword.coroutine` | `keyword` | `async`, `await`, `yield` |
| Exception | `@keyword.exception` | `keyword` | `try`, `catch`, `throw`, `raise`, `finally` |
| Word operator | `@keyword.operator` | `foreground` | `typeof`, `instanceof`, `in`, `of`, `new`, `delete`, `is`, `not` |

### 3.2 Functions and methods

Declaration sites and call sites share the same color — the distinction is structural, handled by the editor's definition highlight, not by the theme.

| Construct | Capture | Role | Examples |
|---|---|---|---|
| Function declaration | `@function` | `symbol` | `function foo() {}` |
| Function call | `@function.call` | `symbol` | `foo()` |
| Method declaration | `@function.method` | `symbol` | `obj.bar() {}` |
| Method call | `@function.method.call` | `symbol` | `obj.bar()` |
| Built-in function | `@function.builtin` | `symbol` | `parseInt`, `len`, `print` |
| Constructor | `@constructor` | `symbol` | `new Foo()`, `__init__` |

### 3.3 Types and namespaces

Types and interfaces are *italic* to signal type-level constructs — they exist in the type system, not at runtime.

| Construct | Capture | Role | Style | Examples |
|---|---|---|---|---|
| Type / interface name | `@type` | `symbol` | *italic* | `string`, `User`, `interface Foo` |
| Built-in type | `@type.builtin` | `symbol` | *italic* | `int`, `str`, `boolean`, `void` |
| Generic type parameter | `@type.parameter` | `literal` | *italic* | `T`, `K`, `V` in `Array<T>` |
| Class name | `@type.class` | `symbol` | *italic* | `class Foo` |
| Namespace / module | `@module` | `foreground` | normal | `std::`, `React.`, `import x` |
| Label | `@label` | `foreground` | normal | `outer:` in labeled loops |

### 3.4 Variables and identifiers

Variables default to `foreground` — they are the background noise of code. Only parameters get *italic* to distinguish binding sites.

| Construct | Capture | Role | Style | Examples |
|---|---|---|---|---|
| Variable | `@variable` | `foreground` | normal | Any name in expression position |
| Constant (inferred) | `@constant` | `literal` | normal | `SCREAMING_SNAKE`, `const` at file scope |
| Built-in constant | `@constant.builtin` | `literal` | normal | `true`, `false`, `null`, `None`, `undefined` |
| Parameter | `@variable.parameter` | `foreground` | *italic* | Names in function signatures |
| Member / field | `@variable.member` | `foreground` | normal | `obj.field`, `self.x` |
| `this` / `self` | `@variable.builtin` | `keyword` | *italic* | `this`, `self`, `super`, `arguments` |

### 3.5 Literals and values

All literal forms map to `literal`. Escape sequences and interpolation delimiters promote to `keyword` — they are structural punctuation, not values.

| Construct | Capture | Role | Examples |
|---|---|---|---|
| String | `@string` | `literal` | `"hello"`, `'world'` |
| Template / interpolated string | `@string.special` | `literal` | `` `Hello ${name}` `` — body only |
| Interpolation delimiters | `@punctuation.special` | `keyword` | `${` and `}` inside templates |
| Escape sequence | `@string.escape` | `keyword` | `\n`, `\t`, `\u0041` |
| Regular expression | `@string.regexp` | `literal` | `/pattern/gi` — body |
| Integer literal | `@number` | `literal` | `42`, `0xFF`, `0b1010` |
| Float literal | `@number.float` | `literal` | `3.14`, `1e10` |
| Character literal | `@character` | `literal` | `'a'` in Rust, C |
| Escape in character | `@character.special` | `keyword` | `'\n'` |

### 3.6 Operators and punctuation

Operators and punctuation are `foreground` throughout — they carry no semantic meaning on their own and should not compete with identifiers for attention. Type-level operators (`:`, `->`, `=>` in type position) promote to `symbol` to match the type hierarchy.

| Construct | Capture | Role | Examples |
|---|---|---|---|
| Arithmetic | `@operator` | `foreground` | `+`, `-`, `*`, `/`, `%` |
| Comparison | `@operator` | `foreground` | `==`, `!=`, `<`, `>`, `<=`, `>=` |
| Logical | `@operator` | `foreground` | `&&`, `\|\|`, `??`, `!` |
| Assignment | `@operator` | `foreground` | `=`, `+=`, `??=`, `\|=` |
| Bitwise | `@operator` | `foreground` | `&`, `\|`, `^`, `~`, `<<`, `>>` |
| Spread / rest | `@operator` | `foreground` | `...` |
| Type annotation separator | `@punctuation.special` | `symbol` | `:` in type position, `->`, `=>` in signatures |
| Brackets | `@punctuation.bracket` | `foreground` | `{`, `}`, `(`, `)`, `[`, `]` |
| Delimiters | `@punctuation.delimiter` | `foreground` | `,`, `;`, `.`, `?.` |

### 3.7 Comments

Comments are uniformly `comment`. Doc comment internals get light structural distinction without introducing new colors.

| Construct | Capture | Role | Style | Examples |
|---|---|---|---|---|
| Line comment | `@comment` | `comment` | normal | `// …`, `# …`, `-- …` |
| Block comment | `@comment` | `comment` | normal | `/* … */` |
| Doc comment body | `@comment.documentation` | `comment` | normal | JSDoc, docstring, `///` |
| Doc tag keyword | `@keyword` (inside doc) | `keyword` | *italic* | `@param`, `@returns`, `@throws` |
| Doc tag type | `@type` (inside doc) | `symbol` | *italic* | `{string}`, `{MyType}` |
| Doc tag parameter name | `@variable.parameter` (inside doc) | `comment` | *italic* | The name after `@param` |

### 3.8 Markup (Markdown, HTML, RST)

| Construct | Capture | Role | Style | Examples |
|---|---|---|---|---|
| Heading | `@markup.heading` | `keyword` | normal | `# H1`, `## H2` |
| Bold | `@markup.strong` | `foreground` | **bold** | `**text**` |
| Italic | `@markup.italic` | `foreground` | *italic* | `*text*` |
| Inline code | `@markup.raw.inline` | `literal` | normal | `` `code` `` |
| Code block | `@markup.raw.block` | `literal` | normal | Fenced blocks |
| List marker | `@markup.list` | `symbol` | normal | `-`, `*`, `1.` |
| Blockquote | `@markup.quote` | `comment` | *italic* | `> text` |
| Link text | `@markup.link.label` | `symbol` | normal | `[link text]` |
| Link URL | `@markup.link.url` | `literal` | underline | `(https://…)` |
| HTML tag name | `@tag` | `keyword` | normal | `<div>`, `<span>` |
| HTML attribute name | `@attribute` | `symbol` | normal | `class`, `id`, `href` |
| HTML attribute value | `@string` | `literal` | normal | `"value"` |

### 3.9 Serialization languages (JSON, YAML, TOML)

| Construct | Capture | Role | Examples |
|---|---|---|---|
| Object / map key | `@property` | `symbol` | `"key":`, `key:` |
| String value | `@string` | `literal` | `"value"` |
| Number value | `@number` | `literal` | `42`, `3.14` |
| Boolean / null | `@constant.builtin` | `literal` | `true`, `false`, `null` |
| Key-value separator | `@punctuation.delimiter` | `foreground` | `:`, `=` |

---

## 4. Semantic token mapping

LSP-based tokens — arrive after file analysis, override or enrich syntax layer tokens. TypeScript, JavaScript, Rust, Go, Java, C/C++ all support these to varying degrees.

### 4.1 Variable modifiers

| Selector | Role | Style | Rationale |
|---|---|---|---|
| `variable` | `foreground` | normal | Default — any resolved variable |
| `variable.readonly` | `literal` | normal | Const binding confirmed by LSP — same as literal since it is effectively a fixed value |
| `variable.readonly.defaultLibrary` | `literal` | underline | Built-in constant (`undefined`, `Infinity`, `NaN`) |
| `parameter` | `foreground` | *italic* | Function / method argument — italic distinguishes it from locals |
| `property` | `foreground` | normal | Object field or class property |
| `property.readonly` | `literal` | normal | Readonly property |

### 4.2 Function and method modifiers

| Selector | Role | Style | Notes |
|---|---|---|---|
| `function` | `symbol` | normal | Any resolved function reference |
| `function.declaration` | `symbol` | normal | At declaration site |
| `method` | `symbol` | normal | Class / object method |
| `method.declaration` | `symbol` | normal | Method definition |
| `function.defaultLibrary` | `symbol` | underline | Built-in functions (`parseInt`, `setTimeout`) |
| `method.defaultLibrary` | `symbol` | underline | Built-in methods (`Array.from`, `Object.keys`) |

### 4.3 Type modifiers

| Selector | Role | Style | Notes |
|---|---|---|---|
| `type` | `symbol` | *italic* | Any resolved type reference |
| `type.defaultLibrary` | `symbol` | *italic* underline | Built-in types (`Promise`, `Map`, `Set`) |
| `class` | `symbol` | *italic* | Class name |
| `class.defaultLibrary` | `symbol` | *italic* underline | Built-in classes (`Error`, `RegExp`) |
| `interface` | `symbol` | *italic* | Interface name |
| `enum` | `symbol` | *italic* | Enum type name |
| `enumMember` | `literal` | normal | Enum value — a constant, so `literal` |
| `typeParameter` | `literal` | *italic* | Generic `T`, `K`, `V` — warm italic distinguishes from concrete types |
| `namespace` | `foreground` | normal | Module or namespace — structural, not semantic |

### 4.4 Semantic modifiers (cross-type)

These apply *on top of* the base type rules above.

| Modifier | Override | Notes |
|---|---|---|
| `*.static` | no change | Static members look like instance members — the `static` keyword on the declaration is enough |
| `*.abstract` | *italic* added | Abstract class/method gets italic to signal "not concrete" |
| `*.deprecated` | strikethrough | Deprecated symbol — cross it out regardless of type |
| `*.declaration` | no change | Declaration site looks the same as usage — use definition highlight in UI instead |
| `*.defaultLibrary` | underline added | Built-in — subtle underline, no color change |
| `*.injected` (Rust) | *italic* | Injected code in macros |

---

## 5. Diagnostics and status indicators

### 5.1 Inline diagnostics

Applies to squiggles, underlines, and gutter signs.

| Token | Hex | Usage |
|---|---|---|
| `diagnostic.error` | `#C44040` | Compile errors, type errors |
| `diagnostic.warning` | `#8B7340` | Warnings, possible errors |
| `diagnostic.info` | `#7A7468` | Informational messages |
| `diagnostic.hint` | `#7A7468` | Hints, code actions |
| `diagnostic.unnecessary` | `#5A5450` | Unused variables, unreachable code |
| `diagnostic.deprecated` | `foreground` + strikethrough | Deprecated symbol usage |

### 5.2 Diff

| Token | Hex | Notes |
|---|---|---|
| `diff.added` | `#4A7A4A` | Muted green — warm dark green |
| `diff.removed` | `#7A3A3A` | Muted red — desaturated brick |
| `diff.changed` | `#7A6030` | Muted amber — earthy yellow-brown |
| `diff.added.fg` | `#A8C8A8` | Text on added line background |
| `diff.removed.fg` | `#C8A8A8` | Text on removed line background |
| `diff.changed.fg` | `#C8B888` | Text on changed line background |

### 5.3 Git blame

| Token | Hex | Notes |
|---|---|---|
| `git.blame.recent` | `#C47433` | Most recent commit — keyword tone |
| `git.blame.old` | `#5A5450` | Older commits — faded toward background |

---

## 6. Terminal ANSI colors

The 16 ANSI colors mapped to the hermit palette. All colors are desaturated and warm-shifted — no pure primaries.

| Index | Name | Hex | Notes |
|---|---|---|---|
| 0 | Black | `#121110` | = background |
| 1 | Red | `#C44040` | = error |
| 2 | Green | `#4A7A4A` | = diff.added |
| 3 | Yellow | `#8B7340` | = warning |
| 4 | Blue | `#4A6A8A` | Only blue — kept desaturated and warm-shifted |
| 5 | Magenta | `#8A5A7A` | Muted violet-pink |
| 6 | Cyan | `#4A7A7A` | Teal-green |
| 7 | White | `#D8D2C4` | = foreground |
| 8 | Bright Black | `#3A3530` | Dark gray |
| 9 | Bright Red | `#D45050` | Lightened error |
| 10 | Bright Green | `#5A9A5A` | Lightened green |
| 11 | Bright Yellow | `#A89050` | Lightened warning |
| 12 | Bright Blue | `#6A8AAA` | Lightened blue |
| 13 | Bright Magenta | `#AA7A9A` | Lightened magenta |
| 14 | Bright Cyan | `#6A9A9A` | Lightened cyan |
| 15 | Bright White | `#F0EAD8` | Near white, warm |

---

## 7. Language-specific notes

### TypeScript / JavaScript

- Template literal expressions (`${...}`) — use `keyword` for `${` and `}` delimiters; `foreground` for the interpolated expression body
- Decorators (`@Component`) — `symbol` *italic*
- Type assertions (`as Type`, `<Type>`) — `keyword` for `as`, `symbol` *italic* for the type name
- JSX: tag names use `keyword` (same as HTML), attribute names use `symbol`, attribute values use `literal`

### Rust

- Lifetimes (`'a`, `'static`) — `literal` *italic*
- Macros (`println!`, `vec!`) — `symbol` with `!` in `keyword`
- Attributes (`#[derive(...)]`) — `comment` for `#[` / `]`, `symbol` for the attribute name
- `unsafe` keyword — `keyword` (same as other keywords; no special treatment)

### Python

- Decorators (`@classmethod`) — `symbol` *italic*
- f-strings — body in `literal`; `{expr}` with `keyword` delimiters
- Type hints (`: str`, `-> None`) — `symbol` *italic*
- Dunder methods (`__init__`) — `symbol` (no special treatment; they are still methods)

### CSS / SCSS

- Property names (`color`, `margin`) — `symbol`
- Property values (`red`, `1rem`) — `literal`
- Selectors (`.class`, `#id`, `div`) — `keyword`
- At-rules (`@media`, `@import`) — `keyword`
- Variables (`--color-bg`, `$spacing`) — `foreground`

---

## 8. Mapping checklist for editor translation

When translating this spec to a specific editor format, every token below should be mapped.

```
Core
  background          ✓
  foreground          ✓
  comment             ✓
  keyword             ✓
  symbol              ✓
  literal             ✓
  error               ✓
  warning             ✓
  hint                ✓

UI (2.2)
  ui.background
  ui.background.sidebar
  ui.background.statusbar
  ui.background.float
  ui.border
  ui.selection
  ui.selection.inactive
  ui.line.highlight
  ui.cursor
  ui.cursor.inactive
  ui.gutter.fg
  ui.gutter.fg.current
  ui.indent.guide
  ui.indent.guide.active
  ui.match.bracket
  ui.search.match
  ui.search.match.current

Diagnostics (5.1)
  diagnostic.error
  diagnostic.warning
  diagnostic.info
  diagnostic.hint
  diagnostic.unnecessary
  diagnostic.deprecated

Diff (5.2)
  diff.added
  diff.removed
  diff.changed
  diff.added.fg
  diff.removed.fg
  diff.changed.fg

Git blame (5.3)
  git.blame.recent
  git.blame.old

Terminal (6)
  ANSI 0–15           ✓ (all 16)

Syntax (3.1–3.9)
  Keywords (3.1)        ✓
  Functions (3.2)       ✓
  Types (3.3)           ✓
  Variables (3.4)       ✓
  Literals (3.5)        ✓
  Operators (3.6)       ✓
  Comments (3.7)        ✓
  Markup (3.8)          ✓
  Serialization (3.9)   ✓

Semantic (4.1–4.4)
  Variables (4.1)       ✓
  Functions (4.2)       ✓
  Types (4.3)           ✓
  Modifiers (4.4)       ✓
```

---

*Last updated: draft v0.1 — palette defined, full token mapping in progress.*
