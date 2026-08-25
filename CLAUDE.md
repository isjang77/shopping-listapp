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

- **State**: a single in-memory `items` array of `{ id, text, checked, created_at }` rows, backed by a Supabase Postgres table `shopping_items` (columns: `id` bigint identity, `text` text, `checked` boolean, `created_at` timestamptz). The `@supabase/supabase-js` UMD build is loaded from a CDN `<script>` tag; the client is created inline with a hardcoded project URL and publishable (anon) key.
- **Render loop**: all mutating functions (`addItem`, `toggleItem`, `deleteItem`, `clearChecked`) are async — they write to Supabase first, then update the local `items` array and call `render()`, which clears and fully rebuilds the `<ul id="list">` DOM from `items`. There is no partial/diffed rendering. On load, `init()` fetches all rows via `loadItems()` before the first render.
- Row Level Security is enabled on `shopping_items` with a permissive "allow all" policy (no auth in this app — anyone with the anon key can read/write).
- **No framework, no modules** — plain DOM APIs inside an IIFE, with one external dependency (supabase-js via CDN).
