# Vim/Neovim — Buffers, Windows, Tabs Mastery Sheet

## Core Mental Model (yaad rakhna sabse pehle)

```
Vim Session
  └── Tab (ek ya zyada) — ek "layout/workspace"
        └── Window (ek ya zyada, split se banti hai) — ek "viewport/camera"
              └── Buffer (jo dikh raha hai us window me) — file ka RAM representation
```

- **Buffer** = file ka in-memory representation. Changes tab tak sirf RAM me hain jab tak write (`:w`) na karo.
- **Window** = viewport jo kisi ek buffer ko dikhata hai. Window khud content nahi hai, sirf ek buffer ko "point" karta hai.
- **Tab** = windows ka ek collection/layout. Naya "file container" nahi hai — sirf window-arrangement store karta hai.

**Sabse important fact**: Buffers **global** hain (poore session ke), Windows **tab-local** hain (har tab ka apna window layout).

**Dusra important fact**: Ek hi buffer ko do-teen windows me ek saath dikha sakte ho (`ctrl+w v` se split karo) — dono windows sync me rahenge kyunki underlying buffer ek hi hai.

---

## 1. Buffer Commands

| Command | Kya karta hai |
|---|---|
| `:e <file>` | File open/load karo (agar already loaded hai toh usi pe jump) |
| `:enew` | Blank naya buffer, unnamed |
| `:bn` / `:bnext` | List me next buffer |
| `:bp` / `:bprev` / `:bprevious` | List me peechla buffer |
| `:bf` / `:bfirst` | Pehla buffer |
| `:bl` / `:blast` | Aakhri buffer |
| `:b <N>` | Buffer number se direct jump |
| `:b <partial-name>` + `<Tab>` | Naam se fuzzy match/jump |
| `ctrl+^` (ya `ctrl+6`) | Alternate buffer pe turant toggle (pichhla wala) |
| `:ls` / `:buffers` | Saari buffers list (numbers ke saath). `%`=current, `#`=alternate, `a`=active |
| `:bd <N/name>` | Buffer delete karo list se (file disk se delete NAHI hoti) |
| `:bd!` | Force delete bina save kiye |
| `:bufdo <cmd>` | Har buffer pe command run karo (bulk operation) |

---

## 2. Window Commands

### Split banana
| Command | Kya |
|---|---|
| `:sp` / `:split` / `ctrl+w s` | Horizontal split, same buffer |
| `:vsp` / `:vsplit` / `ctrl+w v` | Vertical split, same buffer |
| `:sp <file>` / `:vsp <file>` | Split + naya buffer load us window me |
| `:new` / `:vnew` | Split + blank unnamed buffer |
| `:close` / `ctrl+w c` | Current window band karo |
| `:only` / `ctrl+w o` | Sirf current window rakho, baaki close |

### Navigate / focus move
| Command | Kya |
|---|---|
| `ctrl+w h/j/k/l` | Direction me focus move (buffer wahi rehta hai) |
| `ctrl+w w` | Next window (cycle) |
| `ctrl+w p` | Last active window pe jump |

### Resize / rearrange (god-level control)
| Command | Kya |
|---|---|
| `ctrl+w =` | Saari windows equal size |
| `ctrl+w _` | Current window max height |
| `ctrl+w \|` | Current window max width |
| `ctrl+w +` / `ctrl+w -` | Height badhao/ghatao |
| `ctrl+w >` / `ctrl+w <` | Width badhao/ghatao |
| `ctrl+w r` | Windows ka position rotate (swap) |
| `ctrl+w x` | Current aur next window swap |
| `ctrl+w H/J/K/L` (capital) | Current window ko forcefully left/bottom/top/right pe move karo — layout hi restructure ho jaata hai |

---

## 3. Tab Commands

| Command | Kya |
|---|---|
| `:tabnew` | Naya blank tab (ek window, blank buffer) |
| `:tabnew <file>` | Naya tab + file load |
| `:tabclose` | Current tab band (windows close, buffers list me rehte hain) |
| `:tabonly` | Sirf current tab rakho, baaki close |
| `gt` | Next tab |
| `gT` | Previous tab |
| `{count}gt` | Directly tab number `{count}` pe jao (e.g. `2gt`) |
| `:tabs` | Saare tabs aur unke buffers ka summary |
| `:tabmove <N>` | Current tab ko position N pe move karo |
| `ctrl+w T` | Current window ko cheer ke naye tab me daal do |

---

## Practice Drill (NIBMG project pe try karna)

1. `:tabnew` — naya tab banao
2. `:vsp job-portal.html` — split + alag buffer
3. `ctrl+w H` — layout restructure dekho
4. `ctrl+w =` — wapas equalize
5. `2gt` phir `1gt` — dono tabs ke beech direct jump
6. `:tabs` — clean summary dekho

---

## Yaad rakhne wali cheezein

- `:b <filename>` **naya buffer nahi kholta** agar already loaded hai — sirf current window ka display switch karta hai.
- `:bd` = list se remove, disk se delete nahi.
- Tabs ko browser-tabs ki tarah mat treat karo — zaroorat na ho toh tab mat banao, `:bn`/`:bp` se hi kaam chalao.
- Yeh sab vanilla Neovim commands hain. LazyVim ke `<leader>b...` / `<leader>w...` inhi ke shortcuts hain, naya concept nahi.
