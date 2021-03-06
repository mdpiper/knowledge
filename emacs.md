# Emacs

Notes on using Emacs. Gained from years of experience!

## Package management

I'm trying *el-get* for emacs package management.

[https://github.com/dimitri/el-get](https://github.com/dimitri/el-get)

See this page for commands. The packages are installed in **~/emacs.d/**.

I also looked at
[MELPA](http://ergoemacs.org/emacs/emacs_package_system.html), but I
think I like *el-get* better.

I've installed:

* `auto-complete`
* `popup`
* `markdown-mode`
* `exec-path-from-shell` (execute `M-x exec-path-from-shell-copy-env PATH`)
* `python-environment`
* `jedi`
* `yaml-mode`
* `cmake-mode`
* `json-mode`
* `git-commit-mode`

## Meta key

Sometimes the meta key `M` isn't mapped properly.
Usually, you can fall back on using the `Esc` key instead.

## List current keybindings

List all the current keybindings in an Emacs session with:

    M-x describe-bindings

## Commenting code

To comment a highlighted region, use:

	M-x comment-region

Uncomment with:

	M-x uncomment-region

Works in Python. 

Better -- this also works for comment and uncomment:

	M-;

## Formatting code

To format a line, use `C-M-q` or `Esc-q`.

To format a region, use `C-M-\`. (Mapping issues on MacOS.)

To format an entire buffer,
I included a function in my **.emacs**,
bound to `C-c f`.
See https://www.emacswiki.org/emacs/ReformatBuffer.

## Moving between open buffers

Cycle through open buffers with:

    C-x [left or right arrow key]

Select an open buffer with:

    C-x b

## Using multiple windows

Make a pair of windows, side-by-side, with:

    C-x 3

Switch between these windows with:

    C-x o

## Goto location in file

Go to the beginning of a file:

    M-<

the end:

    M->

and a particular line number:

    M-g g <number>


## Return to previous location in file

At the desired line,
set a mark and deactivate it:

    C-Space C-Space

Go somewhere else.

Return to the marked location with:

    C-u C-Space


## Search and replace

Perform an interactive search and replace with

    M-% <search term> RET <replace term>

And you can copy-paste into the minibuffer.

Do a global replace with

    M-x replace-string RET <current> RET <new> RET

## Autocomplete local variables

Autocomplete local Python variables with:

	M-\

## Refresh file in buffer

If a file in a buffer has changed on disk,
refresh it with

	M-x revert-buffer

(I've mapped this to a keybinding, `C-c r`)

Alternately, use

    C-x C-v RET

## Show full filepath in minibuffer

The full filepath is stored in a variable, `buffer-file-name`.
I added a function, `show-file-name`,
to my **.emacs**.
Call it with

    M-x show-file-name

## Untabify a file

Use

    M-x untabify

Note that a tab can always be forced with `C-q TAB`.

## Indenting code

This may only work in `python-mode`,
but shift to the left with

    C-c <

and shift to the right with

    C-c >

In `bibtex-mode`, to get the proper BibTeX format, type

    C-c C-q

while within a bibliography entry.
The long form of this command is

    M-x bibtex-fill-entry


## Transpose characters

Transpose characters with `C-t`.
The character under the cursor is swapped
with the previous character.

(I used this all the time in vi,
and I happened upon it in Emacs!)


## Apply .emacs after editing

Use

    M-x eval-buffer

which evaluates the code in a buffer.

## Evaluate a variable

Use `M-:`; e.g.,

    M-: buffer-file-name

## Printing

I've had the best luck with output to B/W PostScript.

1. Create PostScript code from the current buffer.

        M-x ps-spool-buffer

   The PS code is created in a new buffer.
1. Save the PS buffer to a new file (e.g., **foo.ps**).
1. Run `ps2pdf` on the PS file.

        $ ps2pdf foo.ps

   This makes a new file, **foo.ps.pdf**.


See also [PostScript Hardcopy](https://www.gnu.org/software/emacs/manual/html_node/emacs/PostScript.html). [gnu.org]

## Access menubar in console mode

Starting emacs with `-nw` displays the menubar in a terminal.
Access this menu with:

	M-x menu-bar-open

On a Mac,
this sequence is bound to `Fn-F10`
(tested on laptop and desktop).

## Formatting JSON

Highlight region and apply:

	M-x json-reformat-region

## JavaScript mode

Start JavaScript mode with:

	M-x js-mode

This is also helpful for working with JSON (indenting, no validation).
However,
I also installed `json-mode`,
which should start automatically when a JSON file is loaded.

## Python IDE

I'd like to use emacs as my Python IDE. Use this page:

[http://www.jesshamrick.com/2012/09/18/emacs-as-a-python-ide/](http://www.jesshamrick.com/2012/09/18/emacs-as-a-python-ide/)

for ideas, but use *el-get* for package management.

## Colors

Emacs has its own color palette.
I like "AntiqueWhite1" in the foreground
and "Gray5" in the background.


## Building from source

Use the standard GNU process.

    ./configure --prefix=$HOME/local
    make
    make install

If libraries aren't present,
e.g., for image formats or X,
disable them in `configure`;
e.g., `--without-gif`.
(Building on ***siwenna***,
I disabled gif, tiff, and jpeg.)

Be sure to add **$HOME/local/bin** to your path.
