# CLAUDE.md

Guidance for AI assistants working in this repository.

## What this project is

A single-page marketing **landing page** for a "mini-SaaS IA", built with
**Next.js 14 (Pages Router)** + React 18. It was scaffolded by a generator
called **NLP Factory** (see the commit history: every file landed as a
separate `chore: add <file> from NLP Factory` commit).

The whole app is one static-rendered route. There is no backend, no API
route, no database, no auth, and no client-side state. All page copy is
hardcoded as module-level constants in `pages/index.jsx`.

**All user-facing copy is in French.** Keep it that way when editing or
adding sections — do not silently translate to English.

## Layout

```
package.json        deps + the only three scripts (dev / build / start)
next.config.mjs     Next config — strict mode on, eslint & TS checks disabled at build
pages/
  _app.jsx          root App component; sole job is importing globals.css
  index.jsx         the entire landing page (hero, features, steps, CTA, footer)
styles/
  globals.css       every style in the project — plain CSS, no framework
README.md           just the project name
```

There is no `public/`, no `components/`, no `app/` directory, no test
directory, and no lockfile committed.

## Commands

```bash
npm install     # no lockfile is committed, so this resolves fresh each time
npm run dev     # dev server on http://localhost:3000
npm run build   # production build
npm run start   # serve the production build (requires a prior build)
```

Verified toolchain in this environment: Node v22, npm 10.

There is **no lint script, no test script, and no CI workflow** (`.github/`
does not exist). "Does it build and does the page look right" is the only
available check — run `npm run build` after non-trivial changes.

## Conventions

**File format.** `.jsx` for React (not `.tsx` — the project is plain
JavaScript), `.mjs` for the Next config. Double quotes, 2-space indent,
semicolons. Match the existing style; there is no Prettier/ESLint config to
enforce it for you.

**Content lives in data arrays.** `pages/index.jsx` defines its copy as
constants at the top of the file:

- `PROJECT_NAME`, `PROJECT_TAGLINE`, `PROJECT_PITCH`, `IDEA_BRIEF` — scalars
  injected by the generator
- `features` and `steps` — arrays of objects rendered with `.map()`, keyed on
  `feature.title` / `step.label`

To add or change a feature or step, edit the array, don't hand-write more
JSX. Keep the `key` prop derived from a stable field.

**Styling is global CSS with semantic class names.** No CSS Modules, no
Tailwind, no styled-components, no inline `style` props. New markup gets a
class name in the existing vocabulary (`.card`, `.hero-*`, `.feature-card`,
`.step-card`, `.cta-*`, `.section-heading`, `.eyebrow`, `.btn-primary`,
`.metric-pill`) and a matching rule appended to `styles/globals.css`.

**Design system**, as encoded in `globals.css` — stay inside it:

- Dark theme only. Page background is a radial gradient from `#1e293b` to
  `#000`; body text `#e5e7eb`, muted text `#9ca3af`, accent `#a5b4fc`.
- Content is a stack of `.card` panels inside `.grid-shell` (max-width
  1120px), each with a `1.5rem` radius, a translucent slate background, a
  `rgba(148,163,184,0.3)` border and a large soft shadow.
- The single CTA style is `.btn-primary` — a pill with a green→cyan gradient
  and a glow. Don't introduce a second button treatment.
- Mobile-first: single-column by default, with `min-width: 800px` /
  `900px` media queries widening the feature grid, steps grid and hero.
  `.cta-card` is the one exception, using a `max-width: 720px` query to
  collapse.

**Section structure.** Each block is a `<section className="card">` with an
optional `<header className="section-heading">` containing an `.eyebrow`
kicker, an `<h2 className="section-title">`, and an optional
`.section-subtitle`. Follow that shape for new sections. Section boundaries
are marked with `{/* HERO */}`-style comments — keep them.

**Page metadata** goes through `next/head` in the `<Head>` block at the top
of `Home`, not into `_app.jsx`.

## Things worth knowing before you change something

- `next.config.mjs` sets `eslint.ignoreDuringBuilds` and
  `typescript.ignoreBuildErrors` to `true`. A green `npm run build` therefore
  proves very little about correctness — read your diff carefully.
- `reactStrictMode` is on, so effects would double-invoke in dev if any were
  added. There are none today.
- `PROJECT_NAME` is a truncated UUID fragment (`"a92b933d-9cda-4188-b7e0-"`),
  the same generator id as the repo name and the `name` field in
  `package.json`. It renders in the `<title>`, the hero `<h1>` and the footer.
  It looks like a placeholder because it is one — if asked to "set the
  product name", that constant is the thing to change.
- `IDEA_BRIEF` holds the raw prompt the generator was given
  (`"test studio tiktok dance"`) and is displayed verbatim in the hero
  preview panel inside a `<pre>`. Whitespace in that JSX block is
  significant.
- Apostrophes in French copy inside JSX text must be escaped as `&apos;`
  where the existing code does so (see the CTA note); the file is
  inconsistent about this, so match the surrounding line.

## Git workflow

Default branch is `main`. Work on a feature branch and push with
`git push -u origin <branch>`. Commit messages in the existing history use
Conventional Commit prefixes (`chore:`). Only open a pull request when
explicitly asked.
