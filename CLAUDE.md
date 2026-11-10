# CLAUDE.md: apnea.fyi (apnea_fyi)

Project context for Claude sessions. Facts below come from earlier work on this project. Items marked **verify** were discussed or generated but not confirmed as applied, so check the repo before relying on them.

## What this is

- A static site built with **Jekyll**, currently live at https://apnea.fyi. The project is being repurposed into the website for the **Emerald City Apnea Challenge**, an organization that hosts freediving competitions.
- Local path: `~/Desktop/projects/apnea_fyi` on macOS (Apple silicon MacBook Air).
- Existing content (from the old personal freediving blog) includes posts on partial pressures, freediving organizations, an incident report and advanced examples, plus an About page. Decide with the owner what to keep, rewrite or remove as the new site takes shape.

## Purpose and audience

The site is for **athletes and volunteers** of the Emerald City Apnea Challenge. It should let them:

- Find information about **upcoming events** (dates, locations, registration and logistics).
- Find **pictures and results from past events**.
- Find the **rules for AIDA freediving competitions**.
- Find the organization's **social media accounts and channels** (Facebook, Instagram, YouTube).

When adding or changing content, keep these four goals in mind. Write for readers who may be new to competition freediving, and keep event information easy to find and keep current. Verify AIDA rules against AIDA's official sources before publishing them, since rules change. Don't paraphrase them from memory.

## Toolchain (local)

| Tool | Version |
|---|---|
| Ruby | 4.0.9, installed with `ruby-install`, selected with `chruby` (`~/.rubies/ruby-4.0.9`) |
| Jekyll | 4.4.1 |
| Bundler | 4.0.24 |
| jekyll-sass-converter | 3.1.0 (Dart Sass via `sass-embedded` 1.108.0) |

The Gemfile originally pinned Jekyll 4.2.2, which fails on Ruby 4.0 (`csv` missing). It was upgraded to `gem "jekyll", "~> 4.4"`. Keep `Gemfile.lock` committed.

### Run locally

```bash
cd ~/Desktop/projects/apnea_fyi
chruby ruby-4.0.9        # if `ruby -v` is not 4.0.9
bundle install
bundle exec jekyll serve --livereload   # http://localhost:4000/
```

- Always use `bundle exec`.
- `_config.yml` and the Gemfile are read only at startup, so restart the server after editing them.
- If `webrick` is reported missing: `bundle add webrick`.
- Clean rebuild: `rm -rf _site .jekyll-cache`.

## Project structure (as discussed)

- `_layouts/default.html` is the main layout (head, header, content, footer). It links `/assets/css/frame.css` if `site.show_frame`, otherwise `/assets/css/main.css`.
- `_includes/`: `menu.html`, `sidebar.html`, and `social-links.html` (**verify**: generated for the footer, reads `site.social` from `_config.yml`).
- `_sass/` partials (indented `.sass` syntax): `basic`, `classes`, `font`, `index`, `layout`, and `social` (**verify**).
- `assets/css/`: `main.sass`, `frame.sass`, `sidebar.sass`. These start with `---` front matter, can contain Liquid (`{% if site.minimal %}`), and begin with `@import index, font, basic, layout, classes` (plus `, social` if applied).
- `assets/images/`: `snorkel_frog.png` (header image). The layout references `favicon.png`, `favicon.ico` and `apple-touch-icon.png` in this folder (**verify** they exist).
- `assets/fontawesome/icons.svg`: Font Awesome sprite used by the menu and footer icons.
- `_config.yml` keys in use include `navigation`, `external` (footer mail and RSS links), `plugins` (including `jekyll-feed`), `social` (**verify**), and `minimal`, `show_frame`, `show_sidebar`, `mathjax`. KaTeX assets live under `assets/katex/`.

## Rules and gotchas

- **Never edit `_site/`.** It is generated and overwritten on every build. `_site/` and `.jekyll-cache/` belong in `.gitignore`. `_site` was once committed and pushed. It was untracked afterward (`git rm -r --cached _site`), so confirm `git ls-files _site | wc -l` prints 0.
- **Stylesheet and partial names must differ.** Dart Sass resolves `@import` against the file's own folder first. The stylesheet `assets/css/main.sass` and the partial `_sass/index.sass` are intentionally named differently. A stylesheet sharing a name with a partial in `_sass/` imports itself and fails with `expected end of rule`.
- The compiled stylesheet is `/assets/css/main.css`. The layout previously linked `index.css` and was fixed. A 404 for `index.css` means a stale link.
- Do not paste shell commands into the Gemfile. It once broke with `bundle update jekyll` appended to a gem line.
- Favicon `<link>` tags go in `<head>`. The layout has historically had no explicit `<head>`/`<body>` wrapper (the browser infers them). A cleaned-up version was proposed (**verify** whether applied). Sass selectors like `body > header` and `body > footer` depend on those being direct children of `<body>`.

## Deployment (open question)

The live page reports `meta generator: Jekyll v3.10.0`. That is the version GitHub Pages' built-in builder uses, not the local 4.4.1. The local Sass setup (Dart Sass, renamed files) may not build on GitHub Pages' older Jekyll. Check the repo's Actions or Pages build status for failures. If builds fail, the clean fix is a GitHub Actions workflow that builds with Jekyll 4.4. Confirm how the site is actually deployed before changing the Gemfile or Sass.

## Known work in progress and ideas

- **Sass deprecation warnings** (build works). Stage 1: replace `lighten()`, `darken()` and `mix()` with `color.adjust()` and `color.mix()` (add `@use "sass:color"`), and replace `/` division with `* 0.01` or `math.div`. Stage 2 (optional): migrate `@import` to `@use`/`@forward`, partials first, since the front-matter stylesheets cannot go through the Sass migrator.
- **Redirect:** `/emeraldcity/` should redirect to the Emerald City Apnea Challenge Linktree (`https://linktr.ee/emeraldcityapneachallenge?utm_source=ig&utm_medium=social&utm_content=link_in_bio`). The no-plugin approach is `emeraldcity.html` with `layout: null`, `permalink: /emeraldcity/`, `sitemap: false`, a meta refresh, and `location.replace` (**verify** it exists). The `fbclid` parameter was dropped on purpose.
- **Footer social links:** Facebook (`https://www.facebook.com/profile.php?id=61584381043663`), Instagram (`https://www.instagram.com/emeraldcityapneachallenge`), YouTube (`https://www.youtube.com/@emeraldcityapneachallenge`). They use inline SVG icons that are gray by default and take the brand colors on hover, in `_includes/social-links.html` with styles in `_sass/social.sass` (**verify**).

## Conventions for edits

- Fix problems in source files (`_layouts`, `_includes`, `_sass`, `assets`, `_config.yml`), not in `_site/`.
- Prefer small, reviewable changes, and commit a working state before refactors such as the Sass migration.
- When something fails, share the first `Error:` block, not the whole stack trace.
