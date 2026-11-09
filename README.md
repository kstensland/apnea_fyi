---
title: "About"
permalink: "/about/"
layout: page
---

# apnea_fyi

A static site built with [Jekyll](https://jekyllrb.com/).

## Versions

| Tool | Version |
|---|---|
| Ruby | 4.0.9 |
| Jekyll | 4.4.1 |
| Bundler | 4.0.24 |
| jekyll-sass-converter | 3.1.0 (uses Dart Sass via `sass-embedded`) |

The exact gem versions are locked in `Gemfile.lock`. Commit that file so everyone gets the same versions.

## Prerequisites (macOS)

### 1. Xcode command line tools

```bash
xcode-select --install
```

### 2. Homebrew

Install from [brew.sh](https://brew.sh) if you don't have it.

### 3. Ruby 4.0.9 via chruby

Don't use the Ruby that ships with macOS (2.6.x). It's too old for this project.

```bash
brew install chruby ruby-install
ruby-install ruby 4.0.9
```

Add chruby to your shell config:

```bash
echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
```

Quit and reopen Terminal, then switch to the installed version:

```bash
chruby ruby-4.0.9
ruby -v    # should print ruby 4.0.9
```

To select it automatically in this project, create a `.ruby-version` file. With `auto.sh` loaded, chruby switches whenever you `cd` into the folder:

```bash
echo "4.0.9" > .ruby-version
```

## Setup

```bash
git clone <repo-url> apnea_fyi    # skip if you already have the folder
cd apnea_fyi
gem install bundler
bundle install
```

`bundle install` reads the `Gemfile` and installs Jekyll and all plugins and themes.

## Run the site locally

```bash
bundle exec jekyll serve --livereload
```

Open <http://localhost:4000/>. With `--livereload`, the browser refreshes when you save a file.

Always run Jekyll with `bundle exec` so it uses the gem versions from `Gemfile.lock`.

### Useful commands

| Command | What it does |
|---|---|
| `bundle exec jekyll serve` | Build and serve the site, rebuilding on changes |
| `bundle exec jekyll serve --drafts` | Include posts in `_drafts/` |
| `bundle exec jekyll build` | Build once into `_site/` without serving |
| `bundle exec jekyll doctor` | Check the site for config problems and deprecations |
| `bundle update` | Update gems within the limits set in the `Gemfile` |

## Troubleshooting

**`ruby -v` shows 2.6.x**
The shell is still using the macOS system Ruby. Check that the two `chruby` lines are in `~/.zshrc`, reload with `source ~/.zshrc`, then run `chruby ruby-4.0.9`. Run `chruby` with no arguments to list installed Rubies. The active one is marked with `*`.

**`cannot load such file -- csv` (or `base64`, `bigdecimal`, `logger`)**
This comes from running a Jekyll version older than 4.4 on Ruby 3.4 or newer. Jekyll 4.4 and later install `csv` and `base64` as dependencies. Confirm the `Gemfile` has `gem "jekyll", "~> 4.4"` and run `bundle update jekyll`.

**`cannot load such file -- webrick`**
Newer Ruby versions don't bundle it. Run:

```bash
bundle add webrick
```

**Sass error such as `expected end of rule` pointing at `assets/css/index.sass`**
Dart Sass looks for imports next to the stylesheet before checking `_sass/`. If the main stylesheet shares a name with an imported partial (for example `assets/css/index.sass` containing `@import index`), the file ends up importing itself. Rename either the stylesheet or the partial so the names differ. If you rename the stylesheet, update the `<link>` that references the compiled `.css` file in your layouts or includes.

**`@import is deprecated` warnings**
These are harmless for now. Dart Sass plans to remove `@import`, so you may want to migrate to `@use` at some point.

**Gem install fails with native extension errors**
Make sure the Xcode command line tools are installed (`xcode-select --install`).

**Stale or odd output after config or dependency changes**
Stop the server, delete the generated folder, and start again:

```bash
rm -rf _site .jekyll-cache
bundle exec jekyll serve --livereload
```

## Project notes

- `_config.yml` isn't reloaded by the live server. Restart `jekyll serve` after editing it.
- `_site/` and `.jekyll-cache/` are generated and should be listed in `.gitignore`.
