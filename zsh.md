# Zsh

I'm moving from bash to zsh.
Here are my notes.

## Dotfiles

I have my customized

1. `.zshenv` (for setting environment variables),
1. `.zprofile` (for executing commands at login),
1. `.zshrc` (for configuring interactive shells),
1. `.zprompts` (see below)

files in my [dotfiles repo](https://github.com/mdpiper/dotfiles).

*Resources:*

* A great description of zsh from the [Arch Linux docs](https://wiki.archlinux.org/title/zsh)

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
* [Zsh prompts that don't suck](https://sureshjoshi.com/development/zsh-prompts-that-dont-suck), with a nice, step-by-step explanation of building a prompt
* An [xterm 256 color](https://upload.wikimedia.org/wikipedia/commons/1/15/Xterm_256color_chart.svg) chart
* ohmyzsh [themes](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes), which I think is too much


## Paths

Set up PATH in `.zshenv`.

*Resources:*

* An explanation on [configuring the zsh path](https://wiki.archlinux.org/title/Zsh#Configuring_$PATH) from the Arch Linux docs
* This [gist](https://gist.github.com/Linerre/f11ad4a6a934dcf01ee8415c9457e7b2)
was helpful for decoding mysterious path ordering on macOS.

