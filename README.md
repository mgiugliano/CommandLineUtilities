# CommandLineUtilities

This is a random (growing?) collection of (tiny!) command line utilities, I use everyday. 
Please note that my *bash-fu* is not very strong: be patient with me! Suggestions or improvements are welcome. 

  1. [Votational Nelocity](#votational-nelocity)(a bash script, inspired by Notational Velocity note taking)
  2. [Scratch pad](#scratch-pad) (a bash script, appending text to a monthly "scratch pad" text file)
  3. 

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

