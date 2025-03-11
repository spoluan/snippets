 

## **Basic Modes in Vim**
1. **Normal Mode** (default): Used for navigation and operations.
2. **Insert Mode**: Entered by pressing `i`, used for text insertion.
3. **Visual Mode**: Entered by pressing `v` (character-wise) or `V` (line-wise) for text selection.
4. **Command-Line Mode**: Entered by pressing `:` for executing commands like saving, quitting, etc.

---

## **Essential Commands**

### **File Operations**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `:w`           | Save the file                            | `:w`                  |
| `:w filename`  | Save the file with a new name            | `:w newfile.txt`      |
| `:q`           | Quit Vim                                 | `:q`                  |
| `:q!`          | Quit without saving changes              | `:q!`                 |
| `:wq` or `:x`  | Save and quit                            | `:wq` or `:x`         |
| `:e filename`  | Open a file                              | `:e file.txt`         |
| `:saveas file` | Save the current file as a new file       | `:saveas new.txt`     |
| `:r filename`  | Insert the contents of a file            | `:r file.txt`         |

---

### **Cursor Movement**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `h`            | Move left                                | Press `h`             |
| `l`            | Move right                               | Press `l`             |
| `j`            | Move down                                | Press `j`             |
| `k`            | Move up                                  | Press `k`             |
| `0`            | Move to the beginning of the line        | Press `0`             |
| `^`            | Move to the first non-blank character    | Press `^`             |
| `$`            | Move to the end of the line              | Press `$`             |
| `gg`           | Move to the beginning of the file        | Press `gg`            |
| `G`            | Move to the end of the file              | Press `G`             |
| `:n`           | Move to line `n`                         | `:25` (go to line 25) |
| `w`            | Move to the beginning of the next word   | Press `w`             |
| `e`            | Move to the end of the current/next word | Press `e`             |
| `b`            | Move to the beginning of the previous word | Press `b`           |
| `%`            | Jump between matching parentheses/brackets | Press `%`           |

---

### **Insert Mode**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `i`            | Insert before the cursor                 | Press `i`             |
| `I`            | Insert at the beginning of the line      | Press `I`             |
| `a`            | Insert after the cursor                  | Press `a`             |
| `A`            | Insert at the end of the line            | Press `A`             |
| `o`            | Insert a new line below the current line | Press `o`             |
| `O`            | Insert a new line above the current line | Press `O`             |
| `<Esc>`        | Exit insert mode                         | Press `<Esc>`         |

---

### **Editing Text**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `x`            | Delete the character under the cursor    | Press `x`             |
| `X`            | Delete the character before the cursor   | Press `X`             |
| `dd`           | Delete the current line                  | Press `dd`            |
| `D`            | Delete from the cursor to the end of the line | Press `D`          |
| `dw`           | Delete from the cursor to the end of the word | Press `dw`         |
| `d$`           | Delete from the cursor to the end of the line | Press `d$`         |
| `yy`           | Copy (yank) the current line             | Press `yy`            |
| `yw`           | Copy (yank) the current word             | Press `yw`            |
| `p`            | Paste after the cursor                   | Press `p`             |
| `P`            | Paste before the cursor                  | Press `P`             |
| `u`            | Undo the last change                     | Press `u`             |
| `Ctrl+r`       | Redo the undone change                   | Press `Ctrl+r`        |
| `r<char>`      | Replace the character under the cursor   | `rX` replaces with `X`|
| `R`            | Enter replace mode                       | Press `R`             |
| `c`            | Change text (delete and switch to insert mode) | `cw` changes a word |

---

### **Visual Mode**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `v`            | Enter visual mode (character-wise)       | Press `v`             |
| `V`            | Enter visual mode (line-wise)            | Press `V`             |
| `Ctrl+v`       | Enter visual block mode                  | Press `Ctrl+v`        |
| `d`            | Delete the selected text                 | Press `d`             |
| `y`            | Yank (copy) the selected text            | Press `y`             |
| `>`            | Indent the selected text                 | Press `>`             |
| `<`            | Unindent the selected text               | Press `<`             |
| `=`            | Auto-indent the selected text            | Press `=`             |

---

### **Search and Replace**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `/pattern`      | Search forward for `pattern`            | `/word`               |
| `?pattern`      | Search backward for `pattern`           | `?word`               |
| `n`            | Repeat the search in the same direction  | Press `n`             |
| `N`            | Repeat the search in the opposite direction | Press `N`           |
| `:%s/old/new/g` | Replace all occurrences of `old` with `new` | `:%s/foo/bar/g`     |
| `:%s/old/new/gc`| Replace all with confirmation            | `:%s/foo/bar/gc`     |

---

### **Window Management**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `:split`       | Split window horizontally                | `:split`              |
| `:vsplit`      | Split window vertically                  | `:vsplit`             |
| `Ctrl+w w`     | Switch between windows                   | Press `Ctrl+w w`      |
| `Ctrl+w q`     | Close the current window                 | Press `Ctrl+w q`      |
| `Ctrl+w h`     | Move to the left window                  | Press `Ctrl+w h`      |
| `Ctrl+w l`     | Move to the right window                 | Press `Ctrl+w l`      |
| `Ctrl+w +`     | Increase window height                   | Press `Ctrl+w +`      |
| `Ctrl+w -`     | Decrease window height                   | Press `Ctrl+w -`      |

---

### **Tabs**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `:tabnew`      | Open a new tab                           | `:tabnew`             |
| `:tabclose`    | Close the current tab                    | `:tabclose`           |
| `:tabnext`     | Go to the next tab                       | `:tabnext`            |
| `:tabprev`     | Go to the previous tab                   | `:tabprev`            |
| `:tabs`        | List all tabs                            | `:tabs`               |

---

### **Registers and Macros**
| Command         | Description                              | Example Usage          |
|-----------------|------------------------------------------|------------------------|
| `"ay`          | Yank into register `a`                   | `"ay`                 |
| `"ap`          | Paste from register `a`                  | `"ap`                 |
| `q<register>`  | Start recording a macro into a register   | `qa` (record into `a`)|
| `q`            | Stop recording                           | Press `q`             |
| `@<register>`  | Play a macro from a register             | `@a` (play macro `a`) |

