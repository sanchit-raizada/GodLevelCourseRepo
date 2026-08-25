# Neovim HTML Speed Cheatsheet

> Plugins: emmet, nvim-surround, nvim-ts-autotag, treesitter, conform.nvim

---

## Emmet Expansions (Insert Mode → Tab)

| Shortcut | Result |
|---|---|
| `!` + Tab | Full HTML5 boilerplate |
| `div.container` + Tab | `<div class="container"></div>` |
| `ul>li*3` + Tab | `<ul>` with 3 `<li>` children |
| `p*3>span` + Tab | 3 paragraphs each with a `<span>` |
| `a{click here}` + Tab | `<a href="">click here</a>` |
| `form>input[type=text]` + Tab | form with text input |
| `div#app>header+main+footer` + Tab | semantic layout skeleton |
| `nav>ul>li.nav__item*4>a{link $}` + Tab | nav with 4 numbered links |

---

## nvim-surround (kylechui/nvim-surround)

### Add Surrounding

| Shortcut | What it does |
|---|---|
| `ysst` | Wrap line in HTML tag (prompts for tag name) |
| `ysiw"` | Wrap word in double quotes |
| `ysiw<` | Wrap word in HTML tag |
| `ys$<` | Wrap to end of line in HTML tag |
| `ysip<` | Wrap whole paragraph in HTML tag |
| `V` then `S<div>` | Visual select line → wrap in `<div>` |

### Change Surrounding

| Shortcut | What it does |
|---|---|
| `cs"'` | Change `"quotes"` → `'quotes'` |
| `cst` + `div` | Change surrounding tag to `<div>` |
| `cs(<` | Change `(parens)` to HTML tag |

### Delete Surrounding

| Shortcut | What it does |
|---|---|
| `dst` | Delete surrounding HTML tag (keeps content) |
| `ds"` | Delete surrounding double quotes |
| `ds{` | Delete surrounding curly braces |

---

## nvim-ts-autotag

| Behavior | What happens |
|---|---|
| Type `<div>` | Closing `</div>` auto-inserted |
| Rename opening tag | Closing tag auto-renames to match |

---

## Text Objects — Inside / Around

| Shortcut | What it does |
|---|---|
| `cit` | Change inside HTML tag |
| `dit` | Delete inside HTML tag |
| `vit` | Visual select inside HTML tag |
| `dat` | Delete around tag (tag + content) |
| `ci"` | Change inside double quotes |
| `ci'` | Change inside single quotes |
| `ci{` | Change inside curly braces |
| `ci[` | Change inside square brackets |
| `ci(` | Change inside parentheses |
| `ci<` | Change inside `< >` — great for JSX class names |
| `di{` | Delete inside curly braces |
| `va{` | Visual select around braces (includes braces) |

---

## Fast Movement

| Shortcut | What it does |
|---|---|
| `f<` | Jump to next `<` on line |
| `;` | Repeat last `f` / `t` jump forward |
| `,` | Repeat last `f` / `t` jump backward |
| `%` | Jump between matching HTML tags / brackets |
| `W` / `B` | Jump by WORD (skips punctuation) |
| `*` | Jump to next occurrence of word under cursor |
| `#` | Jump to previous occurrence of word under cursor |
| `g;` | Go back to last edit position |
| `ctrl-o` | Jump back in jump list |
| `ctrl-i` | Jump forward in jump list |
| `[[` / `]]` | Jump between sections / functions |

---

## Power Combos Seniors Use

| Shortcut | What it does |
|---|---|
| `.` | **Repeat last change** — most powerful key in Vim |
| `cgn` | Change next search match → `.` to repeat across file |
| `yyt<` | Yank from cursor to next `<` (copy attribute value) |
| `dt<` | Delete from cursor to next `<` |
| `ct<` | Change from cursor to next `<` |
| `gUiw` | Uppercase entire word (useful for CSS constants) |
| `:%s/foo/bar/g` | Replace all in file — rename class globally |
| `:%s/foo/bar/gc` | Same but confirm each replacement |

---

## LazyVim Leader Key Shortcuts (`<Space>`)

| Shortcut | What it does |
|---|---|
| `<Space><Space>` | Recent files |
| `<Space>/` | Grep in project |
| `<Space>sf` | Telescope find files |
| `<Space>ls` | Start Live Server (port 8080) |
| `<Space>lx` | Stop Live Server |

---

## The `cgn` Workflow (Senior Secret Weapon)

1. `/className` — search for the class or word
2. `cgn` — change that match
3. Press `.` on every next occurrence to repeat

No macro needed. Works for bulk renaming classes across a file.

---

## The `.` (dot) Workflow

Any multi-step edit can be repeated with `.`:

1. `cit` → type new content → `Esc`
2. Jump to next tag
3. `.` — repeats the change-inside-tag + new content

---

## Emmet + Surround Combo

Write plain text, then wrap it:

1. Type: `hello world`
2. `ysst` → type `span` → Enter
3. Result: `<span>hello world</span>`

Or use Emmet directly in insert mode for structured HTML.

---

## Useful `f` + `;` Flow for HTML Attributes

Cursor anywhere on a tag line:

1. `f"` — jump to first quote
2. `ci"` — change inside quotes
3. Type new value → `Esc`
4. `;` — jump to next quote on same line
5. Repeat

---

*Plugins required: emmet-language-server, nvim-surround, nvim-ts-autotag, nvim-treesitter*
