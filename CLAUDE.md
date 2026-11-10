# CLAUDE.md: apnea.fyi (apnea_fyi)

Project context for Claude sessions. Keep this file current when the structure, deployment, or event format changes.

## What this is

- A static site built with **Jekyll**, live at https://apnea.fyi. It is the website for the **Emerald City Apnea Challenge (ECAC)**, an organization that hosts AIDA freediving competitions in the Pacific Northwest.
- Local path: `~/Desktop/projects/apnea_fyi` on macOS (Apple silicon MacBook Air). GitHub repo: `kstensland/apnea_fyi`.
- The old personal freediving blog content still lives in `_posts/` and `_pages/` (partial pressures, freediving organizations, an incident report, advanced examples, welcome posts). Decide with the owner whether to keep, rewrite, or remove it. It is not linked from the navigation.

## Purpose and audience

The site is for **athletes and volunteers** of ECAC. It should let them:

- Find **upcoming events** (dates, locations, registration, map).
- Find **photos and results from past events**.
- Find the **rules for AIDA freediving competitions**.
- Find the organization's **social media** (Facebook, Instagram, YouTube) and contact email.

Write for readers new to competition freediving. Verify AIDA rules against AIDA's official sources before publishing them, since rules change. Do not paraphrase them from memory.

## Pages and navigation

| Page | Source | URL |
|---|---|---|
| Home | `index.html` includes `_includes/home.html` | `/` |
| About (the organization) | `about.md` | `/about/` |
| Events list (upcoming and past) | `events.html` | `/events/` |
| Upcoming event pages | `_events/*.md`, layout `event` | `/events/<name>/` |
| Past event pages | `_past_events/*.md`, layout `past_event` | `/events/past/<name>/` |
| Linktree redirect | `emeraldcity.html` (no layout, meta refresh) | `/emeraldcity/` |

- The header nav is set in `_config.yml` under `navigation`: Home, About (`about.md`), Events (`/events/`). A nav item with a `url` links there. An item with a `file` looks up that page by file name.
- `README.md` is developer documentation. It is excluded from the site (see `exclude` in `_config.yml`). Do not put organization content there.
- `CLAUDE.md` is excluded from the site too.

## Home page

`_includes/home.html` renders the home page. It has no event list:

- A full-width **hero**: `assets/images/home_page_hero.jpg`, with the overlay heading "Community Hosted Freediving Competitions and Events in the PNW" and a "Register for an upcoming event" button that links to `/events/`.
- The hero sits outside the content `<article>`, so it reaches the window edges.
- Hero height steps at fixed widths in `_sass/hero.sass` (11em, 16em, 22em, 28em, 34em). The image is cropped with `object-fit: cover`, centered.
- The heading uses **Raleway** at weight 300, loaded from Google Fonts in `_layouts/default.html`.
- The image's alt text describes the photo (a freediver in open water with the Seattle skyline behind). Update it if the photo changes.

## Events

Events are Jekyll **collections**. Each event is one Markdown file, and the collection is set in `_config.yml`.

### Upcoming events (`_events/`)

- Collection `events`, layout `event`, URL `/events/<file-name>/`.
- Listed in the "Events" section of `/events/`, sorted by `event_date` ascending.
- Front matter:

```yaml
---
layout: event
title: "Pool Competition"
event_date: 2027-02-07
event_end_date: 2027-02-08     # optional, for multi-day events
location: "Snohomish Aquatic Center"
map_query: "Snohomish Aquatic Center, Snohomish, WA"
registration_url: "https://ecac.wufoo.com/forms/..."
---
```

- Shows the date, location, a **Register** button (from `registration_url`), and the map.

### Past events (`_past_events/`)

- Collection `past_events`, layout `past_event`, URL `/events/past/<file-name>/`.
- Listed in the "Past events" section of `/events/`, newest first. Upcoming and past events never share a list.
- Same front matter as upcoming events, **without** `registration_url`. There is no Register button.
- Adds `drive_url` (the Google Drive photo folder) and a `results` list:

```yaml
---
layout: past_event
title: "Sept 2026 Depth Competition"
event_date: 2026-09-05
event_end_date: 2026-09-06
location: "Seacrest Park Cove 1"
map_query: "Seacrest Park Cove 1, Seattle, WA"
drive_url: "https://drive.google.com/drive/folders/..."
results:
  - image: "/assets/images/sept_2026_depth_results_sheet.jpg"
    label: "September 5th results"
    alt: "Results table for the Saturday, September 5, 2026 Depth Competition"
  - image: "/assets/images/sept_2026_depth_whiteboard.jpg"
    label: "September 6th"
    alt: "Whiteboard listing the ECAC Depth Results from the September 6, 2026 competition"
---
```

- Each result is a **thumbnail** with a caption. Results sit side by side on wide screens and stack on phones.
- Thumbnails are fixed at 15rem wide and 3:4 tall (`aspect-ratio: 0.75`), cropped with `object-fit: cover`. The modal shows the full image.
- Clicking a thumbnail opens that result in a native `<dialog>` modal. Clicking the image inside the modal toggles full-width zoom. Close with the × button, a click on the dark backdrop, or Escape.
- Each result gets its own dialog (`results-dialog-1`, `results-dialog-2`, ...). The JS in `_layouts/past_event.html` wires them up.

### Dates

- **Use `event_date`, not `date`.** Jekyll hides any document whose `date` is in the future, and both kinds of event are often in the future. `event_date` is not special to Jekyll.
- `_includes/event_date_text.html` formats the date. It shows "May 22 & 23, 2027" for multi-day events and "February 7, 2027" for single-day events.

### Maps

- `_includes/event_map.html` renders the map. Pass it `query` and `label`.
- It shows a "Loading map…" placeholder with an "Open in Google Maps" link. The Google embed iframe is hidden until it loads, then the placeholder is removed.
- Do not add `loading="lazy"` to the iframe. Browsers skip lazy-loading hidden frames, so the map would never load.
- `map_query` is a Google Maps search string. Check the pin on the built page, since a search can land on the wrong place. Precise coordinates are the most reliable fix.

### Adding an event

1. Create `_events/<name>.md` (upcoming) or `_past_events/<name>.md` (past) with the front matter above. The file name becomes the URL slug.
2. For past events, add result images to `assets/images/`:
   - **PDF results:** render page 1 to JPG at about 216 dpi, for example with `pypdfium2` (`page.render(scale=3)`, saved as JPEG quality 90). This gives about 1836 × 2376 px.
   - **Photos (for example a whiteboard):** apply `ImageOps.exif_transpose` so the rotation is right, then resize to 1800 px on the longest side (JPEG quality 88).
   - Use a clear `label` and `alt` for each result.
3. Restart `jekyll serve` (see below) and check the event page, the events list, the map pin, and the modal.

### Testing events in a headless browser

Chrome is installed at `/Applications/Google Chrome.app`. For a quick visual check:

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --no-sandbox --disable-gpu \
  --hide-scrollbars --window-size=1280,900 --virtual-time-budget=4000 \
  --screenshot=/path/to/out.png http://localhost:8000/events/
```

Serve a built copy (`bundle exec jekyll build -d <scratch dir>`) with `python3 -m http.server`. `--dump-dom` shows the DOM after scripts run. A test script that sets `document.title` is a simple way to read results back.

## Toolchain (local)

| Tool | Version |
|---|---|
| Ruby | 4.0.9, installed with `ruby-install`, selected with `chruby` (`~/.rubies/ruby-4.0.9`) |
| Jekyll | 4.4.1 |
| Bundler | 4.0.24 |
| jekyll-sass-converter | 3.1.0 (Dart Sass via `sass-embedded`) |

The Gemfile pins `gem "jekyll", "~> 4.4"`. Keep `Gemfile.lock` committed.

### Run locally

```bash
cd ~/Desktop/projects/apnea_fyi
chruby ruby-4.0.9        # if `ruby -v` is not 4.0.9
bundle install
bundle exec jekyll serve --livereload   # http://localhost:4000/
```

- Always use `bundle exec`.
- `_config.yml` and the Gemfile are read only at startup. **After editing either, restart the server** (Ctrl+C, then run it again). A stale server keeps serving old config, such as an old nav.
- `jekyll serve` rewrites `_site/` every time a source file changes. To keep `_site/` untouched while you work, build into a scratch folder instead: `bundle exec jekyll build -d <scratch dir>`.
- If `webrick` is reported missing: `bundle add webrick`.
- Clean rebuild: `rm -rf _site .jekyll-cache`.

## Project structure

- `_layouts/`
  - `default.html`: head (stylesheet, favicons, Google Fonts), header (logo `ecac_black.png`, the `.header_title` text, nav), content, and footer (description, social links, and the mail link in a shared `.footer-links` row).
  - `page.html`, `post.html`: for the old blog content.
  - `event.html`: upcoming event pages.
  - `past_event.html`: past event pages, including the results thumbnails and modal JS.
- `_includes/`
  - `home.html`: home page hero.
  - `event_date_text.html`: formatted event date.
  - `event_map.html`: map embed with placeholder.
  - `social-links.html`: footer social icons (from `site.social`).
  - `menu.html`: nav and footer link lists.
  - `sidebar.html`, `archive.html`, `ecac_home.html`, `embed.html`, `meta.html`: older, mostly unused.
- `_events/`, `_past_events/`: event collections (see Events).
- `_sass/`: partials, all in indented `.sass` syntax.
  - `index.sass`: color and typography variables (`$dark`, `$light`, `$link-color`, `$heading-weight`) and the `reduce()` function.
  - `font.sass`: PT Sans (local `assets/fonts/`).
  - `basic.sass`, `layout.sass`, `classes.sass`: base styles, header, `.icon`, `.header_icon`, `.header_title`.
  - `social.sass`: footer social icons and the `.footer-links` row.
  - `events.sass`: event cards, event details, map placeholder, results grid, and the modal.
  - `hero.sass`: home hero, overlay text, and stepped heights.
- `assets/css/`: `main.sass` (the live stylesheet), `frame.sass` (unused, `show_frame: false`), `sidebar.sass` (unused, `show_sidebar: false`). Each starts with `---` front matter, then `@use "index" as *;` and `@use` for each partial.
- `assets/images/`: logos (`ecac_black.png` in the header, `ecac_white.png`), `snorkel_frog.png` (old header image), favicons, `home_page_hero.jpg`, event result images, and placeholder SVGs (`hero_placeholder.svg`, `results_placeholder.svg`).
- `assets/fontawesome/icons.svg`: Font Awesome sprite used by the menu and footer.
- `assets/katex/`: KaTeX assets (math, off by default).
- `_config.yml`: `navigation`, `external` (footer mail link), `social`, `collections` (`events`, `past_events`), `exclude` (`README.md`, `CLAUDE.md`), `plugins` (`jekyll-feed`), and the layout flags `minimal` (dark header), `show_frame`, `show_sidebar`, `show_excerpts`, `mathjax`.

### Styling notes

- Stylesheets use `@use "index" as *;` and `@use "sass:color";` for `color.adjust()` and `color.mix()`. Use `math.div()` or `* 0.01` instead of `/` for division.
- Only `main.sass` is served. Put new partials in `_sass/` and add a `@use` line to `main.sass` and `frame.sass`.
- Avoid `aspect-ratio: 3 / 4` in Sass (`/` is treated as division). Write `aspect-ratio: 0.75`.
- The footer and header rules rely on `body > header`, `body > article`, and `body > footer` being direct children of `<body>`.

## Rules and gotchas

- **Never edit `_site/` by hand.** It is generated. `_site/` and `.jekyll-cache/` belong in `.gitignore`. Confirm `git ls-files _site | wc -l` prints 0.
- **Stylesheet and partial names must differ.** `assets/css/main.sass` and `_sass/index.sass` are named differently on purpose. A stylesheet with the same name as a partial imports itself.
- The compiled stylesheet is `/assets/css/main.css`.
- Do not paste shell commands into the Gemfile.
- Browsers can cache `main.css` for up to 10 minutes after a deploy (`max-age=600`). If the live site looks wrong after a push, try a hard refresh (Cmd+Shift+R) before debugging.
- Dates for events: use `event_date` (see Events).

## Hosting and deployment

The site is hosted on **GitHub Pages** at **apnea.fyi** (custom domain from the `CNAME` file, HTTPS enforced).

- Deployment uses the GitHub Actions workflow in `.github/workflows/pages.yml`. It runs on every push to `main` (and on manual dispatch). It builds with `bundle exec jekyll build` on Ruby 3.4 using the Gemfile and `Gemfile.lock`, uploads `_site/`, and deploys it with `actions/deploy-pages`.
- Pages' **source is "GitHub Actions"** (Settings → Pages → Build and deployment). The old built-in "pages build and deployment" job should no longer run. If it does (`gh run list` shows `pages-build-deployment`), check the Pages source setting.
- The old built-in build used GitHub's Jekyll 3.10 and Ruby Sass, which could not compile `@use` or `color.*`. That is why the switch was needed.
- Check deploys with `gh run list --limit 5`, and read failures with `gh run view <id> --log-failed`.
- Action versions were flagged for Node.js 20 deprecation. Update `actions/*` versions when GitHub asks.

## Known work and ideas

- **Sass:** the Stage 1 deprecation fixes (`color.adjust`, `color.mix`, `math.div`) and Stage 2 (`@use` instead of `@import`) are both done.
- **Second past event:** the owner has not yet provided details for a second September 2026 event. Ask for them before adding one.
- **Results sheets:** the Sept and Nov 2026 past events have their results. New past events can follow the steps under "Adding an event".
- **Map pins:** the Seacrest Park Cove 1 pin has not been confirmed. Check it on the page and send coordinates if it is off.
- **Footer social links:** Facebook (`https://www.facebook.com/profile.php?id=61584381043663`), Instagram (`https://www.instagram.com/emeraldcityapneachallenge`), YouTube (`https://www.youtube.com/@emeraldcityapneachallenge`). They are gray by default and take brand colors on hover.
- **Linktree:** `/emeraldcity/` redirects to the ECAC Linktree (`https://linktr.ee/emeraldcityapneachallenge`). The `fbclid` parameter was dropped on purpose.
- **Old blog content:** posts in `_posts/` are not linked from the navigation. Decide whether to keep them.

## Conventions for edits

- Fix problems in source files (`_layouts`, `_includes`, `_sass`, `assets`, `_config.yml`, `_events`, `_past_events`), not in `_site/`.
- Prefer small, reviewable changes.
- When something fails, share the first `Error:` block, not the whole stack trace.
- The owner reviews changes in the browser. Build and check pages before reporting them as done.
