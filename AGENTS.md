# AGENTS.md

Read @STYLE.md first for my coding style

## Project Overview

CasBlog is a personal blog built with FastHTML, deployed via pla.sh. 

**Notebooks are source of truth. Do NOT edit `.py` files in `/casblog/`—always edit notebooks in `/nbs/`.**

## Notebook → Module Mapping

- `nbs/00_core.ipynb` → `casblog/core.py` (routes, Post dataclass, UI components)
- `nbs/01_crud.ipynb` → `casblog/crud.py` (database operations)

## Stack

- **FastHTML** + **fastlite** (SQLite)
- **MonsterUI**: Tailwind components, Slate dark theme
- **nbdev**: see CLAUDE.md for workflow

## Database

- Production: `data/prod.db` (committed for pla.sh)
- Development: `data/dev.db` (gitignored)
- Detection: `PLASH_PRODUCTION` env var

## Routes

`@rt` decorator. Main routes: `/`, `/post/{slug}`, `/cat/{category}`, `/about`

## Post Model

`title`, `content`, `slug` (auto-generated), `created`, `updated`, `categories` (JSON string), `id`

## Deployment

- pla.sh: `PLASH_APP_NAME=cas-stantonius-site`
- Docs: GitHub Pages via Quarto
