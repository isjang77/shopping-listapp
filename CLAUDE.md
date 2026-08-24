# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repo is a single-page shopping list app (`index.html`). There is no build system, package manager, or test suite — it is one self-contained HTML file with inline `<style>` and `<script>`, meant to run directly in a browser.

## Running the app

Open `index.html` directly in a browser (double-click, or `start index.html` on Windows), or serve it locally:

```
python -m http.server 8000
```

There is no build, lint, or test command — there is no `package.json` or other tooling config in this repo.

## Architecture

Everything lives in `index.html`:

- **State**: a single in-memory `items` array of `{ id, text, checked }` objects, persisted to `localStorage` under the key `shopping-list-items` via `loadItems()` / `saveItems()`.
- **Render loop**: all mutating functions (`addItem`, `toggleItem`, `deleteItem`, `clearChecked`) mutate `items`, call `saveItems(items)`, then call `render()`, which clears and fully rebuilds the `<ul id="list">` DOM from `items`. There is no partial/diffed rendering.
- **No framework, no modules, no external dependencies** — plain DOM APIs inside an IIFE.
