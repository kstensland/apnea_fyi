# CLAUDE.md: emeraldcity.apnea.fyi (apnea_fyi)

Project context for Claude sessions. Keep this file current when the structure, deployment, or event format changes.

## What this is

- A static site built with **Jekyll**, live at https://emeraldcity.apnea.fyi. It is the website for the **Emerald City Apnea Challenge (ECAC)**, an organization that hosts AIDA freediving competitions in the Pacific Northwest.
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
| Linktree redirect | `linktree.html`, `ecac_linktree.html` (no layout, meta refresh; body in `_includes/linktree_redirect.html`) | `/linktree`, `/emeraldcity/linktree` |
| Old blog post list (noindex) | `emeraldcity.html` | `/emeraldcity/` |

- The header nav is set in `_config.yml` under `navigation`: Home (`/#home`), About (`/#about`), Events (`/#events`). Each jumps to a page of the home page carousel. A nav item with a `url` links there. An item with a `file` looks up that page by file name.
- `/about/` and `/events/` still exist as standalone pages for old links. Their content comes from the same includes as the home page sections (`_includes/about_content.html`, `_includes/event_lists.html`), so edit it there.
- `README.md` is developer documentation. It is excluded from the site (see `exclude` in `_config.yml`). Do not put organization content there.
- `CLAUDE.md` is excluded from the site too.

## SEO

- `_includes/seo.html` (included from the head of `_layouts/default.html`) writes the `<title>`, meta description, canonical URL, robots, Open Graph and Twitter tags, and JSON-LD: `SportsOrganization` + `WebSite` on the home page, `SportsEvent` + `BreadcrumbList` on every event page.
- Titles: home is `site.title | site.tagline`; other pages are `<page title> | Emerald City Apnea Challenge`. Front matter `seo_title` replaces the whole title, `description` sets the meta description (aim for about 150 characters), `image` overrides the share image.
- Event pages get a generated one-sentence summary from `_includes/event_description.html`. It is shown under the event title and used as the meta description. An event's `description` front matter replaces it. Event `city` (e.g. `"Snohomish, WA"`) goes into that sentence.
- Give event titles their month and year ("February 2027 Pool Competition") so each page has a unique title in search results.
- `noindex: true` adds `<meta name="robots" content="noindex">`; pair it with `sitemap: false`. Used on 404, archive, `/emeraldcity/` and the theme demo post.
- `jekyll-sitemap` writes `/sitemap.xml`; `robots.txt` points to it. Site URL is `url` in `_config.yml`.
- Default share image: `assets/images/og_image.jpg` (1200 × 630, cropped from the hero). The hero itself is served as `home_page_hero_1200.jpg` / `home_page_hero_2400.jpg` (the 5 MB original is kept but unused). If the hero photo changes, regenerate all three, e.g. with `sips -Z 2400 -s formatOptions 82`.
- After deploying, submit `https://emeraldcity.apnea.fyi/sitemap.xml` in Google Search Console and check event pages with Google's Rich Results Test.

## Home page

`_includes/home.html` renders the home page as a **vertical carousel**: Home (hero), About and Events are each a full-screen page, and scrolling snaps from one page to the next:

- A full-width **hero carousel** of slides from `_data/hero_slides.yml` (image, optional `srcset`, width, height, alt, text, and optional `position` for the crop). Slide 1 is `home_page_hero_2400.jpg` (with a 1200 px `srcset` version) and the heading "Community-Hosted Freediving Competitions in Seattle and the PNW", which is the page's `<h1>`. The other slides' text is a `<p class="hero-heading">`. Slide 2 is `hero_boat_2000.jpg` (with a 1200 px version, crop `position: "50% 35%"` to keep faces in view): two smiling freedivers on the side of a boat with a volunteer and judges behind them, under "Friendly environment that welcomes new and experienced freediving athletes." Slide 3 is `hero_crew_2000.jpg` (with a 1200 px version, crop `position: "50% 22%"` so the "VOLUNTEER" and "SAFETY" shirts stay in view): the competition crew on a boat with two divers in ECAC shirts, under "Don't want to compete? Come be a volunteer!" The slide 2 and 3 originals are only 2000 px wide; replace them with larger copies if they turn up. Add, remove or reorder slides in the data file.
- The carousel is a sideways scroll-snap track (`.hero-track`) with arrows (hidden below 40em), a short line per slide (`.hero-dots`), swipe and left/right keys. The current slide's line fills from left to right as a countdown, and the slide advances when the fill finishes (the length is `--hero-interval`, 7s, in `hero.sass`; the JS waits on that animation, so the two can't drift apart). Auto-advance stops for good once the visitor uses a control, pauses on hover or focus or when the hero is off screen, and is off with reduced motion; the current line is then shown full. Headless Chrome with `--virtual-time-budget` never finishes the fill animation, so test the auto-advance in a visible browser. The JS is the second script in `home.html`; styles are in `_sass/hero.sass`.
- Below the carousel, a "Register for an upcoming event" button links to `/#events` for every slide.
- The hero is not inside an `<article>`, so it reaches the window edges.
- Hero height steps at fixed widths in `_sass/hero.sass` (11em, 16em, 22em, 28em, 34em). The image is cropped with `object-fit: cover`, centered.
- The heading uses **Raleway** at weight 300, loaded from Google Fonts in `_layouts/default.html`.
- The pages are `<section class="home-page">` elements with ids `home`, `about`, `events` inside `.home-pages`. Styles are at the end of `_sass/hero.sass`. Snapping (`scroll-snap-type: y mandatory`), the sticky header and smooth scrolling apply only on pages containing `.home-pages` (`html:has(.home-pages)`).
- Each page is `100svh` minus the header height. A small script in `home.html` sets `--header-height` and highlights the carousel dots (`.home-dots`) on the right edge.
- Each slide's `alt` describes its photo (slide 1: a freediver in open water with the Seattle skyline behind). Update it when a photo changes.

## Events

Events are Jekyll **collections**. Each event is one Markdown file, and the collection is set in `_config.yml`.

### Upcoming events (`_events/`)

- Collection `events`, layout `event`, URL `/events/<file-name>/`.
- Listed under "Upcoming Events" on the home page and `/events/` (from `_includes/event_lists.html`), sorted by `event_date` ascending.
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
- Listed under "Past events" on the home page and `/events/`, newest first. Upcoming and past events never share a list.
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

- Optional `aida_results` (a file name in `_data/aida_results/`, without `.yml`) shows the **Official AIDA Results** after the results thumbnails: a tab for each competition day plus an **Event Ranking** tab with Discipline and Gender filters, like AIDA's own page. The disciplines come from the data file (depth: CWT, CNF, FIM, CWTB; pool: DYN, DNF, STA, DYNB), and each Overall row's `scores` list follows that order. Diver names link to their AIDA athlete profiles. Rendered by `_includes/aida_results.html`.
  - The data is copied by hand from the AIDA event page (`/Events/EventResults-<id>`, one table per day tab) and ranking page (`/Events/EventRanking-<id>`, a form posted with `discipline` = 3 CWT, 4 CNF, 5 FIM, 12 CWTB or `overall`, and `gender` = 0 male or 1 female). Follow `_data/aida_results/sept_2026_depth.yml` (depth, two days) or `nov_2026_pool.yml` (pool, one day) for the format. AIDA can correct results later, so re-check before relying on it.
- Optional `sponsors` (a list of `name`, `image`, and optional `url`) shows a **Sponsors** section at the end of the page, after the map: logos on light cards (so black logos stay readable on the dark background), five across on desktop and two on phones. Sponsor logos are transparent PNGs in `assets/images/sponsor_*.png`. The Sept 2026 ones were cut from the Sept 5 results sheet and the Nov 2026 ones from the Nov 1 sheet, with white turned to transparency (white inside letters too). Two logos keep their white because it is part of the artwork: the Lighthouse Freediving square icon (white waves) and the Lighthouse Diving Center dive flag (white stripe). SPITSEA and Jana Strain appear on both sheets; the files hold the sharper Nov versions and both events use them.
- Optional `youtube_id` (the ID from a YouTube link, e.g. `lBtrWxJAnho`) embeds the video under a "Competition Livestream" heading, just before the map, with a "Watch on YouTube" link below. The video's owner must allow embedding (YouTube Studio → the video → Details → Show more → Allow embedding), or the player shows "Video unavailable".
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

The Gemfile pins `gem "jekyll", "~> 4.4"` and the plugins `jekyll-feed` and `jekyll-sitemap`. `Gemfile.lock` is currently in `.gitignore`, so CI resolves gems fresh.

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
  - `default.html`: head (stylesheet, favicons, Google Fonts), header (logo `ecac_black.png`, the `.header_title` text, nav), content, and footer (social links and the mail link in one centered `.footer-links` row; `site.description` is no longer shown there).
  - `page.html`, `post.html`: for the old blog content.
  - `event.html`: upcoming event pages.
  - `past_event.html`: past event pages, including the results thumbnails and modal JS.
- `_includes/`
  - `home.html`: home page carousel and hero.
  - `seo.html`: head metadata and structured data (see SEO).
  - `event_description.html`: one-sentence event summary.
  - `about_content.html`, `event_lists.html`: shared by the home page and `/about/`, `/events/`.
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
- `_config.yml`: `navigation`, `external` (footer mail link), `social`, `collections` (`events`, `past_events`), `exclude` (`README.md`, `CLAUDE.md`), `plugins` (`jekyll-feed`, `jekyll-sitemap`), `url`, `tagline`, `description`, `logo`, `og_image`, and the layout flags `minimal` (dark header), `show_frame`, `show_sidebar`, `show_excerpts`, `mathjax`.

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

The site is hosted on **GitHub Pages** at **emeraldcity.apnea.fyi** (custom domain set in Settings → Pages; the `CNAME` file matches it; HTTPS enforced). It moved from the bare `apnea.fyi` in November 2026. DNS: a CNAME record `emeraldcity` → `kstensland.github.io` at Namecheap. The wildcard `*` record is to be removed once the move is done.

- Deployment uses the GitHub Actions workflow in `.github/workflows/pages.yml`. It runs on every push to `main` (and on manual dispatch). It builds with `bundle exec jekyll build` on Ruby 3.4 using the Gemfile and `Gemfile.lock`, uploads `_site/`, and deploys it with `actions/deploy-pages`.
- Pages' **source is "GitHub Actions"** (Settings → Pages → Build and deployment). The old built-in "pages build and deployment" job should no longer run. If it does (`gh run list` shows `pages-build-deployment`), check the Pages source setting.
- The old built-in build used GitHub's Jekyll 3.10 and Ruby Sass, which could not compile `@use` or `color.*`. That is why the switch was needed.
- Check deploys with `gh run list --limit 5`, and read failures with `gh run view <id> --log-failed`.
- Action versions were flagged for Node.js 20 deprecation. Update `actions/*` versions when GitHub asks.

## Known work and ideas

- **Sass:** the Stage 1 deprecation fixes (`color.adjust`, `color.mix`, `math.div`) and Stage 2 (`@use` instead of `@import`) are both done.
- **Second past event:** the owner has not yet provided details for a second September 2026 event. Ask for them before adding one.
- **Results sheets:** the Sept and Nov 2026 events show the Official AIDA Results tables instead of their results sheet images. The `results` images were removed from both pages on purpose, but the image files stay in `assets/images/` (the sponsor logos were cut from those sheets). New past events can follow the steps under "Adding an event".
- **Map pins:** the Seacrest Park Cove 1 pin has not been confirmed. Check it on the page and send coordinates if it is off.
- **Footer social links:** Facebook (`https://www.facebook.com/profile.php?id=61584381043663`), Instagram (`https://www.instagram.com/emeraldcityapneachallenge`), YouTube (`https://www.youtube.com/@emeraldcityapneachallenge`). They are gray by default and take brand colors on hover.
- **Linktree:** `/linktree` and the older `/emeraldcity/linktree` redirect to the ECAC Linktree (`https://linktr.ee/emeraldcityapneachallenge`). The `fbclid` parameter was dropped on purpose.
- **Old blog content:** posts in `_posts/` are not linked from the navigation. Decide whether to keep them.

## Conventions for edits

- Fix problems in source files (`_layouts`, `_includes`, `_sass`, `assets`, `_config.yml`, `_events`, `_past_events`), not in `_site/`.
- Prefer small, reviewable changes.
- When something fails, share the first `Error:` block, not the whole stack trace.
- The owner reviews changes in the browser. Build and check pages before reporting them as done.
