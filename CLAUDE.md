# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal Neovim configuration forked from [kickstart.nvim](https://github.com/nvim-lua/kickstart.nvim). It targets **Neovim v0.12+** and uses the **built-in `vim.pack`** plugin manager (not lazy.nvim or packer). The guiding philosophy from upstream is that the config is meant to be read top-to-bottom and understood in full — `init.lua` is heavily commented as a learning document, not just configuration.

## Architecture

The entire active configuration lives in a single ~980-line `init.lua`, organized into numbered `do ... end` block sections. The `do` blocks scope locals so sections stay independent:

- **SECTION 1: OPTIONS** — leaders (`<space>`), `vim.g.have_nerd_font`, `vim.o.*` options, `vim.loader.enable()`
- **SECTION 2: KEYMAPS** — global keymaps and basic autocmds
- **SECTION 3: PLUGIN MANAGER INTRO** — `vim.pack` setup, the `gh` URL helper, build hooks via `vim.pack-events`
- **SECTION 4: UI / CORE UX PLUGINS** — guess-indent, gitsigns, which-key, tokyonight, todo-comments, mini.nvim
- **SECTION 5: SEARCH & NAVIGATION** — telescope and dependencies
- **SECTION 6: LSP** — fidget, LSP server configs, completion-related capabilities
- **SECTION 7: FORMATTING** — conform.nvim
- **SECTION 8: AUTOCOMPLETE & SNIPPETS** — blink.cmp, LuaSnip
- **SECTION 9: TREESITTER** — nvim-treesitter (`main` branch)
- **SECTION 10: OPTIONAL EXAMPLES / NEXT STEPS** — commented `require 'kickstart.plugins.*'` opt-ins and the `require 'custom.plugins'` loader

### Plugin loading model

Plugins are installed by calling `vim.pack.add { ... }` inline within the relevant section, then configured immediately after (e.g. `require('plugin').setup{}`). Version pinning uses `vim.version.range '1.*'` or `version = 'main'`. A `gh 'owner/repo'` helper (defined in Section 3) expands to the full GitHub URL.

`lua/kickstart/plugins/*.lua` are **optional, disabled-by-default** modules (debug, indent_line, lint, autopairs, neo-tree, gitsigns). They are only active if their `require 'kickstart.plugins.X'` line in Section 10 is uncommented. Each file self-contains its own `vim.pack.add` + setup.

`lua/custom/plugins/` is the place for personal additions. `lua/custom/plugins/init.lua` auto-`require`s every other `.lua` file in that directory (following symlinks/links), but only runs if `require 'custom.plugins'` is uncommented in Section 10.

## Conventions

- **Formatting:** stylua, configured in `.stylua.toml` — 2-space indent, 160 column width, `AutoPreferSingle` quotes, `call_parentheses = None` (so `require 'foo'` and `setup { ... }` are written without parentheses). Match this style; it is enforced upstream.
- Keep the explanatory comment density of the surrounding code. This config is intentionally over-documented.

## Common tasks

- **Diagnose problems:** run `:checkhealth` inside Neovim (this is the first thing to do for any install/runtime error).
- **Inspect / update plugins:** `:lua vim.pack.update(nil, { offline = true })` to inspect state; `:lua vim.pack.update()` to fetch updates (`:write` applies, `:quit` cancels).
- **Format Lua:** `stylua init.lua` (or `stylua lua/`).
- **Help search keymap:** `<space>sh` searches Neovim help docs.

## Lockfile

`nvim-pack-lock.json` pins plugin versions. Upstream kickstart `.gitignore`s it to avoid merge conflicts, but in a personal fork it is recommended to track it in version control (`:help vim.pack-lockfile`).
