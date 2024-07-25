# CommandLineUtilities

This is a random collection of command line utilities, I use everyday.

## Votational Nelocity

This is heavily inspired by [Notational Velocity](https://en.wikipedia.org/wiki/Notational_Velocity) note-taking software and its underlying concept and philosophy. It is a simple bash script, powered by [fd](https://github.com/sharkdp/fd), [ripgrep](https://github.com/BurntSushi/ripgrep) and of course [fzf](https://github.com/junegunn/fzf). It lives in the terminal and makes it easy for me to achieve the following actions:

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


### How to use it

Invoking `nv` from the command line (or pressing cmd+p if using a shortcut) will open a panel
