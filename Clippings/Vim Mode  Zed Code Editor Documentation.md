---
title: "Vim Mode | Zed Code Editor Documentation"
source: "https://zed.dev/docs/vim"
author:
published:
created: 2025-12-02
description: "Learn how to use and customize Zed, the fast, collaborative code editor. Official docs on features, configuration, AI tools, and workflows."
tags:
  - "clippings"
---
## [Zed-specific features](https://zed.dev/docs/#zed-specific-features)

Zed is built on a modern foundation that (among other things) uses Tree-sitter and language servers to understand the content of the file you're editing and supports multiple cursors out of the box.

Vim mode has several "core Zed" key bindings that will help you make the most of Zed's specific feature set.

### [Language server](https://zed.dev/docs/#language-server)

The following commands use the language server to help you navigate and refactor your code.

| Command | Default Shortcut |
| --- | --- |
| Go to definition | `g d` |
| Go to declaration | `g D` |
| Go to type definition | `g y` |
| Go to implementation | `g I` |
| Rename (change definition) | `c d` |
| Go to All references to the current word | `g A` |
| Find symbol in current file | `g s` |
| Find symbol in entire project | `g S` |
| Go to next diagnostic | `g ]` or `] d` |
| Go to previous diagnostic | `g [` or `[ d` |
| Show inline error (hover) | `g h` |
| Open the code actions menu | `g .` |

### [Git](https://zed.dev/docs/#git)

| Command | Default Shortcut |
| --- | --- |
| Go to next git change | `] c` |
| Go to previous git change | `[ c` |
| Expand diff hunk | `d o` |
| Toggle staged | `d O` |
| Stage and next (in diff view) | `d u` |
| Unstage and next (in diff view) | `d U` |
| Restore change | `d p` |

### [Tree-sitter](https://zed.dev/docs/#tree-sitter)

Tree-sitter is a powerful tool that Zed uses to understand the structure of your code. Zed provides motions that change the current cursor position, and text objects that can be used as the target of actions.

| Command | Default Shortcut |
| --- | --- |
| Go to next/previous method | `] m` / `[ m` |
| Go to next/previous method end | `] M` / `[ M` |
| Go to next/previous section | `] ]` / `[ [` |
| Go to next/previous section end | `] [` / `[ ]` |
| Go to next/previous comment | `] /`, `] *` / `[ /`, `[ *` |
| Select a larger syntax node | `[ x` |
| Select a smaller syntax node | `] x` |

| Text Objects | Default Shortcut |
| --- | --- |
| Around a class, definition, etc. | `a c` |
| Inside a class, definition, etc. | `i c` |
| Around a function, method etc. | `a f` |
| Inside a function, method, etc. | `i f` |
| A comment | `g c` |
| An argument, or list item, etc. | `i a` |
| An argument, or list item, etc. (including trailing comma) | `a a` |
| Around an HTML-like tag | `a t` |
| Inside an HTML-like tag | `i t` |
| The current indent level, and one line before and after | `a I` |
| The current indent level, and one line before | `a i` |
| The current indent level | `i i` |

Note that the definitions for the targets of the `[m` family of motions are the same as the boundaries defined by `af`. The targets of the `[[` are the same as those defined by `ac`, though if there are no classes, then functions are also used. Similarly `gc` is used to find `[ /`. `g c`

The definition of functions, classes and comments is language dependent, and support can be added to extensions by adding a \[`textobjects.scm`\]. The definition of arguments and tags operates at the Tree-sitter level, but looks for certain patterns in the parse tree and is not currently configurable per language.

### [Multi cursor](https://zed.dev/docs/#multi-cursor)

These commands help you manage multiple cursors in Zed.

| Command | Default Shortcut |
| --- | --- |
| Add a cursor selecting the next copy of the current word | `g l` |
| Add a cursor selecting the previous copy of the current word | `g L` |
| Skip latest word selection, and add next | `g >` |
| Skip latest word selection, and add previous | `g <` |
| Add a visual selection for every copy of the current word | `g a` |

### [Pane management](https://zed.dev/docs/#pane-management)

These commands open new panes or jump to specific panes.

| Command | Default Shortcut |
| --- | --- |
| Open a project-wide search | `g /` |
| Open the current search excerpt | `g <space>` |
| Open the current search excerpt in a split | `<ctrl-w> <space>` |
| Go to definition in a split | `<ctrl-w> g d` |
| Go to type definition in a split | `<ctrl-w> g D` |

### [In insert mode](https://zed.dev/docs/#in-insert-mode)

The following commands help you bring up Zed's completion menu, request a suggestion from GitHub Copilot, or open the inline AI assistant without leaving insert mode.

| Command | Default Shortcut |
| --- | --- |
| Open the completion menu | `ctrl-x ctrl-o` |
| Request GitHub Copilot suggestion (requires GitHub Copilot to be configured) | `ctrl-x ctrl-c` |
| Open the inline AI assistant (requires a configured assistant) | `ctrl-x ctrl-a` |
| Open the code actions menu | `ctrl-x ctrl-l` |
| Hides all suggestions | `ctrl-x ctrl-z` |

### [Supported plugins](https://zed.dev/docs/#supported-plugins)

Zed's vim mode includes some features that are usually provided by very popular plugins in the Vim ecosystem:

- You can surround text objects with `ys` (yank surround), change surrounding with `cs`, and delete surrounding with `ds`.
- You can comment and uncomment selections with `gc` in visual mode and `gcc` in normal mode.
- The project panel supports many shortcuts modeled after the Vim plugin `netrw`: navigation with `hjkl`, open file with `o`, open file in a new tab with `t`, etc.
- You can add key bindings to your keymap to navigate "camelCase" names. [Head down to the Optional key bindings](https://zed.dev/docs/#optional-key-bindings) section to learn how.
- You can use `gR` to do [ReplaceWithRegister](https://github.com/vim-scripts/ReplaceWithRegister).
- You can use `cx` for [vim-exchange](https://github.com/tommcdo/vim-exchange) functionality. Note that it does not have a default binding in visual mode, but you can add one to your keymap (refer to the [optional key bindings](https://zed.dev/docs/#optional-key-bindings) section).
- You can navigate to indent depths relative to your cursor with the [indent wise](https://github.com/jeetsukumaran/vim-indentwise) plugin `[-`, `]-`, `[+`, `]+`, `[=`, `]=`.
- You can select quoted text with AnyQuotes and bracketed text with AnyBrackets text objects. Zed also provides MiniQuotes and MiniBrackets which offer alternative selection behavior based on the [mini.ai](https://github.com/echasnovski/mini.nvim/blob/main/readmes/mini-ai.md) Neovim plugin. See the [Quote and Bracket text objects](https://zed.dev/docs/#quote-and-bracket-text-objects) section below for details.
- You can configure AnyQuotes, AnyBrackets, MiniQuotes, and MiniBrackets text objects for selecting quoted and bracketed text using different selection strategies. See the [Any Bracket Functionality](https://zed.dev/docs/#any-bracket-functionality) section below for details.