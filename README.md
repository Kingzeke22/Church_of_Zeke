# Astro Starter Kit: Blog

```sh
npm create astro@latest -- --template blog
```

[![Open in StackBlitz](https://developer.stackblitz.com/img/open_in_stackblitz.svg)](https://stackblitz.com/github/withastro/astro/tree/latest/examples/blog)
[![Open with CodeSandbox](https://assets.codesandbox.io/github/button-edit-lime.svg)](https://codesandbox.io/p/sandbox/github/withastro/astro/tree/latest/examples/blog)
[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/withastro/astro?devcontainer_path=.devcontainer/blog/devcontainer.json)

> 🧑‍🚀 **Seasoned astronaut?** Delete this file. Have fun!

![blog](https://github.com/withastro/astro/assets/2244813/ff10799f-a816-4703-b967-c78997e8323d)

Features:

- ✅ Minimal styling (make it your own!)
- ✅ 100/100 Lighthouse performance
- ✅ SEO-friendly with canonical URLs and OpenGraph data
- ✅ Sitemap support
- ✅ RSS Feed support
- ✅ Markdown & MDX support

## 🚀 Project Structure

Inside of your Astro project, you'll see the following folders and files:

```text
├── public/
├── src/
│   ├── components/
│   ├── content/
│   ├── layouts/
│   └── pages/
├── astro.config.mjs
├── README.md
├── package.json
└── tsconfig.json
```

Astro looks for `.astro` or `.md` files in the `src/pages/` directory. Each page is exposed as a route based on its file name.

There's nothing special about `src/components/`, but that's where we like to put any Astro/React/Vue/Svelte/Preact components.

The `src/content/` directory contains "collections" of related Markdown and MDX documents. Use `getCollection()` to retrieve posts from `src/content/blog/`, and type-check your frontmatter using an optional schema. See [Astro's Content Collections docs](https://docs.astro.build/en/guides/content-collections/) to learn more.

Any static assets, like images, can be placed in the `public/` directory.

## 🧞 Commands

All commands are run from the root of the project, from a terminal:

| Command                   | Action                                           |
| :------------------------ | :----------------------------------------------- |
| `pnpm install` (preferred) / `npm install` | Installs dependencies                            |
| `pnpm run dev` (preferred) / `npm run dev` | Starts local dev server at `localhost:4321`      |
| `pnpm run build` (preferred) / `npm run build` | Build your production site to `./dist/`          |
| `pnpm run preview` (preferred) / `npm run preview` | Preview your build locally, before deploying     |
| `npm run astro ...`       | Run CLI commands like `astro add`, `astro check` |
| `npm run astro -- --help` | Get help using the Astro CLI                     |

Note: This repository contains a `pnpm-lock.yaml`. We recommend using `pnpm` (Corepack or global)
to install and build for deterministic installs. See `.github/copilot-instructions.md` for more details.

## TinaCMS admin build

The project integrates TinaCMS for in-place content editing. The `build` script is configured to
skip `tinacms build` when Tina Cloud credentials are not present (so CI or dev machines without
Tina credentials can still build the site). To generate the Tina admin locally or in CI, provide
the following environment variables before running `pnpm run build`:

```
TINA_PUBLIC_CLIENT_ID=<your-client-id>
TINA_TOKEN=<your-token>
```

Alternatively, run the Tina dev experience locally with:

```
pnpm run dev
```

If you prefer not to generate the admin in the repository, it's fine to keep the guarded build (the
default). In that case the generated `admin/` artifacts are created by Tina Cloud or on an
authorized machine and do not need to be committed to the repo.

CI / Deployment notes (generate admin in CI)

If you want CI to produce the Tina `admin/` artifacts (and optionally commit them), provide the
Tina Cloud credentials as environment variables in your CI provider or Cloudflare Pages settings.

- GitHub Actions (example): add the following to `.github/workflows/generate-admin.yml` and set
	the repository secrets `TINA_PUBLIC_CLIENT_ID` and `TINA_TOKEN`.

```yaml
name: Build and generate Tina admin
on: [push]
jobs:
	build:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v4
			- name: Use Node
				uses: actions/setup-node@v4
				with:
					node-version: 18
			- name: Install deps
				run: pnpm install --frozen-lockfile
			- name: Build (includes Tina admin)
				env:
					TINA_PUBLIC_CLIENT_ID: ${{ secrets.TINA_PUBLIC_CLIENT_ID }}
					TINA_TOKEN: ${{ secrets.TINA_TOKEN }}
				run: pnpm run build
			# Optionally commit generated admin/ here (careful: consider branch strategy)
```

- Cloudflare Pages: add `TINA_PUBLIC_CLIENT_ID` and `TINA_TOKEN` as build environment variables
	in your Pages project settings. Set the build command to `pnpm run build` and publish directory to
	`dist`.

Be cautious about committing generated admin artifacts: many projects prefer to keep `admin/` out of
source control and let an authorized CI or Tina Cloud produce the artifacts instead.

## 👀 Want to learn more?

Check out [our documentation](https://docs.astro.build) or jump into our [Discord server](https://astro.build/chat).

## Credit

This theme is based off of the lovely [Bear Blog](https://github.com/HermanMartinus/bearblog/).
