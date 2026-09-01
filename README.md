# CommandLineUtilities

This is a random (growing?) collection of (tiny!) command line utilities, I use everyday. 
Please note that my *bash-fu* is not very strong: be patient with me! Suggestions or improvements are welcome. 

  1. [Votational Nelocity](#votational-nelocity)(a bash script, inspired by Notational Velocity note taking)
  2. [Scratch pad](#scratch-pad) (a bash script, appending text to a monthly "scratch pad" text file)
  3. [Blog post manager](#blog-post-manager) (a bash script to draft, tag, and publish posts on a Pandoc-based blog)

## Votational Nelocity

This script is heavily inspired by [Notational Velocity](https://en.wikipedia.org/wiki/Notational_Velocity) note-taking software and its underlying concept and philosophy. It is a simple bash script, powered by [fd](https://github.com/sharkdp/fd), [ripgrep](https://github.com/BurntSushi/ripgrep) and of course [fzf](https://github.com/junegunn/fzf). It lives in the terminal and makes it easy for me to achieve the following actions:

- find and edit a text file in my "notes" folder, fuzzy searching it by filename and/or by content;
- find and open a text file in Obsidian;
- create a new text file, simply starting to type to populate first its filename and then its content;

The former and latter actions are what made Notational Velocity extremely popular as a low-friction note taking app, a few years ago. While I love obsidian, I found myself launching Sublime editor to jot down some ideas or information and use it as a scratch pad. Moreover, launching Obsidian adds a few seconds and I grew impatient when using it to capture thoughts and quick bites of information. Finally, when searching for a given note I enjoyed using [Omnisearch](https://github.com/scambier/obsidian-omnisearch) within Obsidian but frequently I grew unhappy in firing up Obsidian.

All in all, I have been looking for very quickly searching/creating notes and thought of using the terminal (and NeoVim) to do that. I built a first prototype of a bash shell script, `nv`, and when using [Kitty](https://sw.kovidgoyal.net/kitty/), I have associated a simple shortcut (e.g. cmd+p) to launch the script in a new panel:

```bash
map cmd+p launch --env PATH=PATH --env EDITOR=EDITOR --env NOTES=NOTES /opt/homebrew/bin/bash -lc path/to/nv
```

Note how two environment vars ($EDITOR and $NOTES) must be passed to the new shell that launches the script, as indicated above.

$EDITOR is currently not yet used by nv (with [NeoVim](https://neovim.io) used as a default), while $NOTES is. Before passing $NOTES to the new shell, it must be first set (e.g. in .bashrc) to the full path of the folder containing text files notes (with extension *.md). In my case, I use the vault of [Obsidian](https://obsidian.md) and added

```bash
# In my .bashrc

export NOTES="/Users/MYUSERNAME/Library/Mobile Documents/iCloud~md~obsidian/Documents/notes"
```

Launching every few hours, automatically, the nv_support script makes possible to have (as symbolic links) all markdown files outside the $NOTES folder. The ripgrep's option for following symbolic links has been activated.
In this way, any notes or document in (e.g.) project folders not explicitly planned or saved as one entry in the $NOTES folder.

### How to use it (tested under macOs)

Invoking `nv` from the command line (or pressing cmd+p if using a shortcut) will open the fzf interface. Every time the search field is updated a new search is launched, combining filenames and text content of the files contained in $NOTES and its subfolders. Using Control-j and Control-k one can select across multiple hits in the upper part of the window, while having the preview (with syntax highlighting) in the lower part of the window.

![demo1](img/demo1.gif)

Pressing Control-A launches the $EDITOR (currently nvim only) creating a file in $NOTES with the filename given by the search string (with suffix .md) and also with this string added as a title inside the text file.

![demo2](img/demo2.gif)

Pressing Control-O (not shown) launches Obsidian and opens the selected note in it.


### Requirements: fzf, rg, fd, bat, nvim

- brew install fzf
- brew install rg
- brew install fd
- brew install bat
- brew install nvim

---------------

## Scratch pad

This script has been inspired by gotbletu's [work](https://github.com/gotbletu/fzf-nova/blob/main/_notekami%2C--.notes.cheatsheet.snippets.manager) for handling literally a scratch pad for jotting down temporary information, quick unorganised thoughts, things to copy-paste at a later stage, links, etc. 

Using [Kitty](https://sw.kovidgoyal.net/kitty/), I have associated a simple shortcut (e.g. cmd+s) to launch the script in a new panel:

```bash
map cmd+s launch --env PATH=PATH --env EDITOR=EDITOR --env NOTES=NOTES /opt/homebrew/bin/bash -lc ~/.bin/scratch
```

The specific location of the "scratch" file(s) is by default in `$EDITOR/scratch` and each time the script is launched it opens/creates a file uniquely named by year and month. Moreover, every time it is invoked, a timestamp is added and the cursor is placed at the very bottom of the file with nvim already in insert mode. 

![demo3](img/demo3.gif)

---------------

## Blog post manager

I run a small [Pandoc](https://pandoc.org/)-based blog (not Jekyll/Hugo — a plain `build.sh` that converts Markdown posts to HTML and publishes to GitHub Pages). `blog` is the script I use day to day to draft, tag, and publish posts to it without leaving the terminal.

```
blog                   Show help + status (default, no args)
blog new [title]       Draft a new post in content/posts/
blog edit [query]      Fuzzy-pick a post (fzf), open in $EDITOR, then tag it
blog tags [query]      Fuzzy-pick a post, then just do the tag step
blog thumbnail [query] Fuzzy-pick a post and fetch a thumbnail for it
                       from Wikimedia Commons (thumb, image)
blog delete [query]    Fuzzy-pick a post and delete it (rm, remove) --
                       pushes the removal live too, if it was published
blog status            List posts/images not yet committed/pushed (check, pending)
blog push               Build, commit, and push pending posts+images (publish)
blog help                Show this help only
```

`blog edit`/`blog tags`/`blog delete` fuzzy-find a post by filename or title (fzf, with a `bat`-rendered markdown preview) — every entry is tagged with its status (`untracked`, `modified`, `unpushed`, `published`) so it's clear which posts are already live before you touch one.

`blog edit` opens the pick in `$EDITOR`, then helps with tags: [apfel](https://apfel.franzai.com) — Apple Intelligence's on-device model, from the command line — reads the post plus the blog's existing tag vocabulary and prints a quick hint, then you land on one editable prompt pre-filled with that hint (or the post's current tags): press Enter to accept it as-is, or edit the line first. No fuzzy finder for the tags themselves — they aren't files, there's nothing meaningful to preview, and a plain editable line makes "accept the model's proposal" a plain Enter instead of a multi-step picker.

`blog delete` removes the post locally right away (`git rm`, not `git rm --cached` — the file is gone from disk immediately, whether or not you ever push); if the post was already live, it separately asks before pushing that removal out to the real site.

`blog status`/`blog push` track `static/images/` alongside `content/posts/`, not just posts — a `thumbnail:` fetched via `blog thumbnail` is only half-published if the image file itself never gets committed, so both commands catch and stage it right along with the post. `blog push` builds the site locally first (so a broken post never gets committed), proposes a commit message from the post title(s) — or "Update images" if only images changed — and asks for confirmation before it actually pushes. Editing an already-published post's `.md` file directly — through `blog edit` or by hand — flips its status from `published` to `modified`; the next `blog push` picks it up and re-syncs the live copy.

`blog thumbnail` searches [Wikimedia Commons](https://commons.wikimedia.org/) (free, no API key, CC-licensed, restricted to actual images — not the PDFs/audio/video that also live in its File namespace) using the post's title as the default search term — editable before searching, same pre-filled-prompt pattern as tags. It shows up to 8 candidates with license, artist, and a Commons URL to preview in a browser first; nothing is ever auto-picked. CC0/Public-Domain results are sorted first, since those need no attribution. Picking one downloads it into `static/images/{slug}-thumb.{ext}`, sets `thumbnail:` in the post's front matter (which the card-grid generator in `build.sh` already looks for — no wiring needed), and records title/source/license/artist as an HTML comment that `build.sh` renders as a small, muted, visible credit line on the post page — real attribution, not just a hidden comment. If you pick a CC-BY/CC-BY-SA image instead, it still warns you: the credit only shows on the post page, not next to the thumbnail itself on the blog list, so a CC0/Public-Domain pick remains the zero-obligation choice.

### Assumptions about the target repo

This isn't a generic blogging-platform CLI — it assumes a specific (if simple) layout:

- `content/posts/*.md`, each with `---` front matter (`title`, `date`, `tags:` as a comma-separated list) `---`;
- a `build.sh` at the repo root that builds the site (`blog push` runs it before committing);
- posts published via `git push` (to GitHub Pages, in my case).

If your blog looks roughly like that, it should work as-is; otherwise it's meant more as a starting point to adapt than a drop-in tool.

### Configuration (env vars)

- `BLOG_REPO` — **required**. Path to your blog's working copy.
- `BLOG_SITE_URL` — optional, cosmetic only (e.g. `https://blog.example.com`), shown in a couple of messages.
- `BLOG_TAG_SCHEMA` — optional, path to the `apfel` JSON schema for tag output (default: `~/.bin/blog-tags.schema.json`, included in this repo as `blog-tags.schema.json`).

### Requirements: fzf, bat, apfel, gh, python3

- `brew install fzf`
- `brew install bat` (used for the post preview in `blog edit`/`blog tags`; falls back to `cat` if missing)
- `brew install apfel` (needs macOS 26+ and Apple Intelligence enabled; tagging hints are skipped gracefully without it)
- `brew install gh` (optional — used to watch the GitHub Actions deploy after `blog push`; skipped gracefully without it)
- `python3` (used for the front-matter/JSON handling; ships with macOS)

