## Purpose

These notes help an AI coding agent become immediately productive in this Astro + TinaCMS blog repo.
Focus on build/run commands, data flows, conventions, and the small project-specific gotchas
that aren't obvious from a generic Astro template.

## Quick runbook

- Dev: `npm run dev` — runs `tinacms dev -c "astro dev"` (starts Tina CMS + Astro dev server).
- Build: `npm run build` — runs `tinacms build && astro build && pagefind --site dist && cp -r dist/pagefind public/`.
- Preview: `npm run preview` — runs `astro preview` to serve `./dist` locally.

Preferred (pnpm):
- Dev: `pnpm run dev` — runs `tinacms dev -c "astro dev"` (starts Tina CMS + Astro dev server).
- Build: `pnpm run build` — runs `tinacms build && astro build && pagefind --site dist && cp -r dist/pagefind public/`.
- Preview: `pnpm run preview` — runs `astro preview` to serve `./dist` locally.

Notes: Use `pnpm install` to respect the included `pnpm-lock.yaml`. If you must use npm, the npm equivalents
are still valid (`npm install`, `npm run build`), but keep a single lockfile to avoid conflicts.

Notes: the build step runs `pagefind` against `dist` and copies `dist/pagefind` into `public/` so
search assets are available for the deployed site. Ensure the `pagefind` devDependency is present
before running build locally.

## High-level architecture

- Static site built with Astro (see `astro.config.mjs`).
- Content collections are defined in `src/content.config.ts` using `astro:content` + Zod schema.
- TinaCMS is integrated (`astro-tina`) and participates in `dev` and `build` (see `package.json`).
- Client-side features: Alpine.js + Tailwind; search UI initialized in `src/layouts/Base.astro` via PagefindUI.
- Links data is pulled at build-time from a public Google Sheet CSV via `src/links.ts` (`fetchGoogleSheetData`).

Why this structure: the project is a mostly-static blog with editable content (TinaCMS) and a
client-side search index (Pagefind). Google Sheets acts as a lightweight external CMS for the
links list and is fetched at build-time into the static pages.

## Important files and patterns (use these as anchors)

- `package.json` — scripts and core deps: Astro, TinaCMS, Pagefind, Tailwind
- `astro.config.mjs` — integrations: mdx, sitemap, alpinejs, tina
- `src/content.config.ts` — collection schema (Zod). Frontmatter fields to expect: `title`,
  `description`, `pubDate`, `updatedDate?`, `heroImage?`, `excerpt?`.
- `src/content/blog/*` — markdown / mdx posts follow the collection schema.
- `src/layouts/Base.astro` — mounts header/footer, initializes Pagefind UI, contains small inline
  client scripts (example: `numberForm` redirect logic).
- `src/links.ts` — fetches and parses a public Google Sheets CSV. It filters rows with `Deploy === "TRUE"`
  and computes `Categories` from boolean columns. If you change the CSV columns, update parsing here.
- `public/pagefind` and `pagefind.*` files — search runtime assets; created/updated by the build.

## Project-specific conventions & gotchas

- TinaCMS: content editing is driven by `tinacms` commands. `npm run dev` wires Tina into local dev; use
  `npm run build` to produce Tina-managed content for production.
- Pagefind: the index generation happens after `astro build` and expects a `dist` folder. The build copies
  the `pagefind` directory into `public/` so the site can serve the search UI from the deployed site root.
- Google Sheets: `src/links.ts` fetches a public CSV URL at build-time. This is a network call during
  build; offline builds will fail unless the CSV is reachable or cached.
- Schema-first content: update `src/content.config.ts` when adding/removing frontmatter fields so collection
  typing and `getCollection()` consumers remain correct.

## How to modify common tasks (examples)

- Add a new frontmatter field to blog posts:
  1. Edit `src/content.config.ts` to add the field to the Zod schema.
  2. Update any components that read the field (search `getCollection` consumers in `src/`).

- Update the links data source or column names:
  1. Update the CSV header transformations and `nonCategoryColumns` in `src/links.ts`.
  2. Rebuild with `npm run build` to refresh generated pages and Pagefind index.

## Where to look for related changes

- Content collections and typing: `src/content.config.ts`
- Layout and client scripts: `src/layouts/Base.astro`
- Page components and home page wiring: `src/pages/index.astro` and `src/components/*`
- External data integration: `src/links.ts`

## Checklist for PRs an AI should follow

- Make minimal, focused edits and run `npm run dev` or `npm run build` to validate (prefer dev for iteration).
- If changing frontmatter or collection shape, update `src/content.config.ts` and run a build to catch schema errors.
- If touching search or build output, run `npm run build` to regenerate Pagefind assets.

If anything here is unclear or you want the instructions to emphasize different workflows (for example
CI/deploy steps or preferred package manager), tell me which parts to expand and I'll iterate.

## Deployment (Cloudflare Pages & Workers)

- Recommended Cloudflare Pages settings:
  - Build command: `npm run build`
  - Publish directory: `dist` (Astro's build output)

- Important notes:
  - The repo's `npm run build` already runs `pagefind --site dist` which generates a `dist/pagefind` folder
    (search index) and then copies it into `public/` (`cp -r dist/pagefind public/`). Make sure your Pages
    deployment publishes the contents of `dist` so the `pagefind` assets are included in the deployed site root.
  - If you prefer to serve Pagefind assets from `public/` instead, adjust your Pages configuration or the build
    script to move/copy `pagefind` into the final publish directory used by Cloudflare.
  - If you use Cloudflare Workers to augment the site (API routes, edge transforms), ensure routes that
    expect static assets can access `/pagefind/*` paths — Workers should proxy or serve those assets from
    the static build output.

If you'd like, I can add a short Cloudflare Pages checklist (build settings, environment variables, and a
minimal smoke test) tailored to your Pages/Workers setup.
