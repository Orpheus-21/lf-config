# lf-config

A configuration for the lf terminal file manager. It adds vim keys, image and document previews, trash, archive, and fuzzy search commands, and Nerd Font icons.

## What it does

The lf file manager shows three columns: the parent folder, the current folder, and a preview. This repository replaces the default lf configuration. It changes the key bindings, the commands, the previews, the colors, and the icons.

The main additions are:

- A preview script for text, Markdown, JSON, folders, archives, PDF files, video, audio, and images.
- Trash commands that use `gio`, so you can restore a deleted file.
- Commands to extract and compress archives.
- Fuzzy jump, content search with `rg`, and `zoxide` folder jumps.
- Bulk rename in your editor.

## Requirements

- Linux. The clipboard commands need Wayland.
- lf. The configuration was tested with lf r42.
- A Nerd Font in your terminal. The icons file was made for JetBrainsMono Nerd Font.
- The `EDITOR` variable must hold your editor command.
- For image previews inside the pane, a terminal that supports the kitty graphics protocol, such as Ghostty or kitty. Other terminals get text art from `chafa`.

Each program below is needed for one group of features:

| Feature | Programs |
|---|---|
| Preview | `bat`, `eza`, `magick` (ImageMagick), `chafa`, `bsdtar`, `jq`, `file`, `pdftoppm` and `pdftotext` (poppler), `ffprobe` and `ffmpegthumbnailer` (ffmpeg) |
| Jump and search | `fd`, `fzf`, `rg`, `zoxide`, `bat` |
| Trash | `gio`, `fzf` |
| Archives | `bsdtar`, `tar`, `zip`, `gum` |
| Clipboard | `wl-copy` and `wl-paste` (wl-clipboard) |
| Disks | `udisksctl`, `lsblk`, `findmnt`, `gum` |
| Open and terminal | `xdg-open`, `xdg-terminal-exec` |
| Git | `lazygit` |

## Install

1. Move your current configuration away, if you have one:

   ```sh
   mv ~/.config/lf ~/.config/lf.bak
   ```

2. Clone this repository into the configuration folder. The `source` lines in `lfrc` expect this path.

   ```sh
   git clone https://github.com/Orpheus-21/lf-config.git ~/.config/lf
   ```

3. Write your own paths in `keys`. Three jump keys are commented out and marked `WRITE YOUR PATH`. They are `gn`, `gw`, and `gm`. Remove the leading `#` after you set each path.

4. Start lf:

   ```sh
   lf
   ```

lf 42 writes a deprecation warning to its log for the options `rulerfmt` and `statfmt`. The configuration still loads and lf starts.

## Usage

The key grammar follows vim. Press `?` in lf to list all keys. Press `<f1>` to read the manual.

| Key | Action |
|---|---|
| `j` `k` | Move down and up |
| `gg` `G` | Go to the top and the bottom |
| `J` `K` | Move half a page down and up |
| `H` `L` | Go back and forward in the history |
| `l` or Enter | Open |
| `e` | Edit in `$EDITOR` |
| `i` | Show in a pager |
| `o` | Open with a program that you type |
| `O` | Open with `xdg-open` |
| `<c-t>` | Open a terminal in this folder |
| `<c-p>` | Fuzzy jump to a file or folder under this folder |
| `zz` | Jump with `zoxide`, with a picker |
| `<c-g>` | Search file contents with `rg` and open the editor at the line |
| `<space>` | Select and move down |
| `<c-a>` | Select all |
| `yy` `dd` `pp` | Copy, cut, and paste |
| `pl` | Paste as symbolic links |
| `yp` `yn` `yd` `yc` | Copy the path, the name, the folder, or the contents to the clipboard |
| `pc` | Paste the clipboard into a new file here |
| `r` | Rename |
| `R` | Bulk rename in the editor |
| `a` `A` | Create a file and create a folder |
| `cx` | Toggle the execute bit |
| `x` | Extract an archive |
| `Z` | Compress the selection |
| `dD` or `<delete>` | Move to the trash |
| `dr` | Restore from the trash |
| `dE` | Empty the trash |
| `dX` | Delete forever |
| `.` | Show or hide hidden files |
| `sn` `ss` `st` `se` | Sort by name, size, time, and extension |
| `I` | Show file details |
| `<a-g>` | Open `lazygit` |
| `M` `U` | Mount and unmount a disk |
| `q` | Quit |

The file `keys` holds the full list in its header.

## Configuration

| File | Content |
|---|---|
| `lfrc` | Entry point. It loads `options`, `commands`, and `keys` |
| `options` | All `set` lines, in groups |
| `commands` | All `cmd` definitions |
| `keys` | All `map` lines |
| `colors` | File type colors as ANSI codes. They follow the theme of your terminal |
| `icons` | Nerd Font icons for file types and folder names |
| `preview` | The previewer script |
| `cleaner` | Removes images from the pane before lf draws again |

To remove one group, delete its `source` line in `lfrc`.

## How it works

The file `lfrc` loads `options`, `commands`, and `keys` by path under `~/.config/lf`. The `options` file sets `preview` as the previewer and `cleaner` as the cleaner. lf reads `colors` and `icons` from the same folder.

The `preview` script selects the output from the file name:

- A folder shows one level, listed by `eza`.
- An archive shows its file list, from `bsdtar`.
- A PDF shows a picture of page one and the first 3 pages as text.
- A video shows a thumbnail and the `Duration` and `Stream` lines from `ffprobe`.
- An image in a terminal with `ghostty` or `kitty` in `TERM` is drawn with the kitty graphics protocol. Other terminals get `chafa` text art. If `chafa` is missing, the script prints one line of file information.
- A text file shows through `bat`.

The script saves thumbnails as PNG files in `$XDG_CACHE_HOME/lf`, or in `~/.cache/lf`. The name of each thumbnail comes from the real path and the modification time of the file. A changed file gets a new thumbnail.

The `commands` file holds these actions:

- `open` sends text files to the editor and all other files to `xdg-open`, detached from lf.
- `extract` always writes into its own folder. A tar archive uses `tar --one-top-level`. Other archives use `bsdtar` into a folder that has the name of the archive.
- `compress` asks for a format with `gum`: `tar.zst`, `zip`, `tar.gz`, or `tar.xz`. The selected files must be in the current folder.
- `bulk-rename` writes the names to a temporary file and opens the editor. The command stops if the line count changes.
- The `on-cd` hook runs when you change folder. It sets the window title, adds the folder to `zoxide`, and shows the Git branch in the prompt.

## License

GPL-3.0-or-later. See `LICENSE`.
