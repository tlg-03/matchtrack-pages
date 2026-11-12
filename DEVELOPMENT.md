# Working on the MatchTrack pages

The way of working (worktrees, checkpoints, hooks) is the shared standard in `tlg-03/workspace`
(`STANDARD.md`); `make help` lists the commands. This file covers what is particular to this repo.

## Current state

Last updated: 12 Nov 2026.

- **In progress:** none. The live site serves `main` (`make publish` checks it).
- **Recent decisions:** 8 Aug 2026: the repo is public, because GitHub Pages on a free account needs that, so it holds only the pages and the files for working on them, never app source. 28 Oct 2026: MatchTrack has its own Terms of Use (`terms.html`) in place of Apple's Standard EULA, with the same text in App Store Connect's custom licence agreement field. 12 Nov 2026: the repo joins the shared workspace standard, and pages go live from `main` with `make publish`, which checks the live pages match `main` and every other file answers 404 (see [Changing the pages](#changing-the-pages)).
- **Waiting on the owner:** none.
- **Next:** none.

## What this is

The privacy policy, support page and Terms of Use for MatchTrack (iPad and Mac), served by GitHub
Pages from `main`:

```
https://tlg-03.github.io/matchtrack-pages/privacy.html   the Privacy Policy URL, and the paywall's Privacy link
https://tlg-03.github.io/matchtrack-pages/terms.html     the Terms of Use: the paywall's Terms link, the store descriptions and the support page
https://tlg-03.github.io/matchtrack-pages/support.html   the Support URL
https://tlg-03.github.io/matchtrack-pages/               the landing page (index.html)
```

App Review opens these and checks they resolve and match the App Store Connect listing exactly.
This repo is the only source of the pages: edit them here and nowhere else.

The repo is public (GitHub Pages on a free account needs that), so nothing but the pages and the
files for working on them belongs here.

## Never rename this repo

Its name is part of every URL. Renaming it breaks the URLs filed with App Store Connect and built
into the app's paywall, a 404 on a link a reviewer opens. The same goes for the file names.

## Pages that must agree with something else

- **`terms.html`** and App Store Connect's custom licence agreement field carry the same text.
  Change both together.
- **`privacy.html`** makes claims the app's code has to keep true (that it collects nothing beyond
  what the page says). A change to what MatchTrack sends anywhere is a change to this page too.
  Update its "Last updated" line with any change of substance.

## Changing the pages

```
make wt-new NAME=fix-wording    from the main checkout
                                edit the page in the worktree
make view                       preview at http://localhost:8731/index.html (caching off)
make test                       then commit
make wt-done                    tests, fast-forwards main, pushes and verifies it on GitHub
make publish                    from the main checkout: waits until the live site serves main
make wt-rm NAME=fix-wording     from the main checkout
```

The push in `make wt-done` starts the GitHub Pages build. `make publish` runs the tests again,
pushes `main` if it is ahead of GitHub (it should not be after `wt-done`), checks that GitHub holds
that commit, then waits up to 10 minutes until every live `.html`, `.css` and `.js` file is byte for
byte the committed one; that part is `workspace/bin/publish matchtrack-pages`, which also runs on
its own. Last, `tests/check_pages.py --live` asks the live site for every other tracked file, and for
the `.html` page each Markdown file would become (`README.html`, `DEVELOPMENT.html`), and expects a
404 for each.

`index.html` is a landing page of its own, not a copy of `support.html`.

## What GitHub serves

GitHub Pages builds `main` with Jekyll (the legacy build, from the root). The pages have no front
matter, so Jekyll copies them unchanged. `_config.yml` keeps everything else off the site: it
excludes `Makefile`, `README.md`, `DEVELOPMENT.md` and `tests/`, and Jekyll skips names starting
with `.` or `_` (`.mailmap`, `.gitignore`, `_config.yml`), except `.htaccess` and the posts in a
`_posts` folder. An exclude entry hides every path that starts with it, so `tests` would hide a
`tests.html` too. Do not add a `.nojekyll` file: it would serve every file, these notes included.

A new file for working on the site goes into that exclude list; `make test` refuses one that would
be published. A new file the site needs that is not `.html`, `.css` or `.js` (an image, say) must
not be excluded: add its type to `SERVED_TYPES` in `tests/check_pages.py` and to the types
`workspace/bin/publish` compares with the live site, which today are only those three.

## Tests

`make test` runs `tests/check_pages.py`: every page starts with `<!doctype html>` and closes every
element explicitly, in order (void elements such as `<meta>` and `<br>` have no closing tag); every
file GitHub Pages would publish is a page (`.html`, `.css` or `.js`); and every page is published,
since `make publish` looks for each one on the live site. It reads tracked files and new ones that
are not ignored, and follows Jekyll's rules closely, not exactly: the live check in `make publish`
is the proof.

## Local data

- **In the repo:** none (`LOCAL_DATA` is empty).
- **Outside the repo:** none.

## Commits

Neutral, present-tense messages, as `STANDARD.md` sets out (the hooks enforce it). Stage explicit
paths.
