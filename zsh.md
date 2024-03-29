# Zsh

I'm trying to move from bash to zsh.
Here are my notes.

## Dotfiles

I have sample 
`.zshrc`,
`.zshenv`,
`.zprompts`, and
`.zprofile`
files in my [dotfiles repo](https://github.com/mdpiper/dotfiles).


## Prompts

I had waaay too much fun with zsh prompts.

Use the `prompt` command.

I created a few [custom prompts](https://github.com/mdpiper/dotfiles/blob/main/zprompts)
and placed them in my `.zprompts` file,
which, if present, is loaded by my `.zshrc` file.
My favorite right now is
```zsh
prompt ithaca
```

*Resources:*

* A description of [zsh prompts](https://wiki.archlinux.org/title/zsh#Prompts) from the Arch Linux docs
* [Expansion of prompt sequences](https://man.archlinux.org/man/zshmisc.1#EXPANSION_OF_PROMPT_SEQUENCES), also from the Arch Linux docs
* An [xterm 256 color](https://upload.wikimedia.org/wikipedia/commons/1/15/Xterm_256color_chart.svg) chart
* ohmyzsh [themes](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)


## Paths

This [gist](https://gist.github.com/Linerre/f11ad4a6a934dcf01ee8415c9457e7b2)
was helpful for decoding mysterious path ordering on macOS.

I ended up moving the code to set path from `.zshenv` to `.zprofile`.
