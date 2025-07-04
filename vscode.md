# VS Code

In December 2020, I took the plunge and switched from Emacs in the terminal to [Visual Studio Code](https://code.visualstudio.com/).

I have a separate repository, [mdpiper/vscode](https://github.com/mdpiper/vscode), with files for working with VS Code.

## Keybindings

Notes on some keybindings I've made,
and others that I need but I keep forgetting.

| Keybinding | Description |
| --- | --- |
| `C-c u` | Change to uppercase |
| `C-c l` | Change to lowercase |
| `C-Tab` | Presents list of open tabs |
| `M-Cmd-Larrow`, `M-Cmd-Rarrow` | Cycle through open tabs |
| `Cmd-P` | Presents list of recent tab history |
| `Cmd-Shift-.` | Displays a definition (class, function) list, which can be arrowed through |
| `Tab` | Shifts highlighted code block one indentation level to the right |
| `Shift-Tab` | Shifts block one level left |
| `M-q` | Word wrap, Emacs style (through the ReWrap extension) |


### Emacs keybindings

I'm using the [Emacs Friendly Keymap](https://marketplace.visualstudio.com/items?itemName=lfs.vscode-emacs-friendly) extension,
which is currently (Dec 2022) maintained.

I updated ***solaria*** to macOS 12.1,
and the `Space` key stopped working with the `C-Space` chord.
I think it's OS-related because the chord
doesn't even work in Emacs in the terminal
(although the alternate `C-Shift-2` does),
and the VS Code keyboard logger doesn't see the `Space` key
in the chord.
The `Space` key alone does work.

Here's the original keybinding:
```
{
  "key": "ctrl+space",
  "command": "emacs.enterMarkMode",
  "when": "editorTextFocus"
}
```
I changed it to:
```
{
  "key": "ctrl+cmd+space",
  "command": "emacs.enterMarkMode",
  "when": "editorTextFocus"
}
```
which works, and doesn't have any conflicts.

## Markdown preview styles

The default Markdown preview in VS Code is bland.
To make it look nicer, I use the
[Markdown Preview GitHub Styles](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-preview-github-styles) extension.

You can also [define your own styles](https://code.visualstudio.com/Docs/languages/markdown#_using-your-own-css).
I've deployed my [mdpiper/vscode](https://github.com/mdpiper/vscode) repository through Pages,
which exposes my [custom CSS](https://mdpiper.github.io/vscode/css/markdown-preview.css).
I link to this style sheet (by its URL) in my VS Code settings,
where it's layered on top of the styles set by the Markdown Preview extension.
