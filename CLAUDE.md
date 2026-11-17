# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal portfolio site (forked from Gianmarco/Ladvace's `astro-bento-portfolio` template) for a robotics research assistant. Astro 5 with a bento-grid home page, a blog, a research "project" section, and a travel globe page. Production site: https://noelsadventure.my.id/ (deployed via Netlify adapter).

## Commands

- `npm run dev` (or `npm start`) — dev server
- `npm run build` / `npm run preview` — production build / preview
- `npm run check` — `astro check` (type check)
- `npm run eslint` — lints `src/**/*.{js,astro}`

There is no test suite. Both `package-lock.json` and `pnpm-lock.yaml` exist; npm is what the scripts assume.

## Architecture

- **Rendering:** `output: "server"` with `@astrojs/netlify` (edge middleware on). Pages are SSR by default; static routes opt in with `export const prerender = true` (e.g. `src/pages/project/[id].astro` uses `getStaticPaths`).
- **Multi-framework islands:** Astro components for layout/content, Solid (`Globe.tsx`, `Tooltip/index.tsx`, `playground/rifle-1.tsx`; tsconfig sets `jsxImportSource: solid-js`) and Svelte for interactive pieces. Globe uses d3 with `src/lib/world.json`; Rive animations live in `src/riveAnimations` (`vite.assetsInclude` handles `*.riv`).
- **Styling:** UnoCSS (`uno.config.ts`, `presetWind3`) with custom theme colors, `custom` box-shadows, and `CabinetGrotesk`/`Satoshi` fonts (from `public/fonts`). Utility classes are written inline in `.astro` files. Animations use `motion` (entrance stagger on `.card` elements, driven by `loaderAnimation` in `src/lib/constants.ts`), plus gsap and lenis.
- **Home page:** `src/pages/index.astro` composes bento `Card`s (`src/components/Card/`) and section components (IntroCard, AboutMe, ContactsCard, TimeZoneCard, Now, Globe) in a grid; positioning is via `order-*` and `col-span`/`row-span` classes.
- **Layouts:** `src/layouts/` — `Layout.astro` (base), `BasicLayout`, `LayoutBlogPost`, `LayoutProject`.
- **Shared data:** social links/contact info and animation config in `src/lib/constants.ts` (`LINKS`); helpers in `src/lib/helpers.ts`; `remark-reading-time.mjs` is a markdown remark plugin.

## Content collections (gotcha)

Two collections come from markdown in `src/data/`:
- `blog` → `src/data/blog/*.md`, schema in **`src/content.config.ts`** (title, pubDate, selected, tags, password, draft, …). Posts with `selected: true` appear on the home page. Routes: `src/pages/blog/`.
- `project` → `src/data/project/*.md` (title, authors, pubDate, venue, description, image), routes in `src/pages/project/`. Its schema is defined in a **separate** file, `src/content/content.config.ts`. Astro only auto-loads `src/content.config.ts`, so if `project` entries fail to resolve, the `project` collection likely needs to be merged into `src/content.config.ts`.

There are also near-duplicate routes `src/pages/projects/` (plural, using `components/projects/ProjectsRow.astro`) alongside `src/pages/project/`; check which one is actually linked before editing.

## Notes

- `LINKS.email` in `constants.ts` has a stray trailing `<`; several constants (e.g. medium, dribble) are leftovers from the template.
- `getCurrentTimeInItaly`/`formatTimeForItaly` in helpers are template leftovers with hardcoded Italy time zone/CET.
