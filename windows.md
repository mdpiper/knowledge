# Windows

Notes on using Windows, especially at the command prompt with Powershell.

## Cmd

Show the value of an environment variable:
```cmd
> echo %USERNAME%
Mark
```

Show the value of an environment variable:
```cmd
> set USERNAME
USERNAME=Mark
```

The `set` command also matches a prefix:
```cmd
> set CONDA_
CONDA_DEFAULT_ENV=bmic
CONDA_EXE=C:\Users\Mark\Anaconda3\Scripts\conda.exe
CONDA_PREFIX=C:\Users\Mark\Anaconda3\envs\bmic
CONDA_PROMPT_MODIFIER=(bmic)
CONDA_PYTHON_EXE=C:\Users\Mark\Anaconda3\python.exe
CONDA_SHLVL=1
```

It's also used to actually set an environment variable:
```cmd
> set "BMIF_VERSION=2.0"
> set BMIF
BMIF_VERSION=2.0
> echo %BMIF_VERSION%
2.0
```

Show the current path with `set`:
```cmd
> set PATH
```

Prepend the path with directory `C:\Miniconda`:
```cmd
> set PATH=C:\Miniconda;%PATH%
```

Display all environment variables:
```cmd
> env
```

## Pwsh

Show the value of an environment variable:
```pwsh
> echo $env:USERNAME
Mark
```

Append to an environment variable:
```pwsh
$env:PATH += ";C:\Users\Mark\bin"
```

Display all environment variables:
```pwsh
ls env:
```

## Commands

Get help on a command with the `/?` keyword:
```
> set /?
```

A list of other commands is given in the table below.

| Command | Bash equivalent | Description |
| ------- | --------------- | ----------- |
| dir | ls | Directory listing |
| where | which | Locate file and return path |
| mkdir | mkdir | Make a directory |
| rmdir | rmdir | Delete a directory |
| head | head | Display first 10 lines of file |
| tail | tail | Display last 10 lines of file |
| more | more | Pager |
| less | less | Pager |


## Comments

Comments are set with the double colon `::`
```
> :: This is a comment
```

This only works at the beginning of a line.

To set a comment within a line,
prefix it with an ampersand
```
> dir  &:: This is a comment
```


## Keyboard shortcuts

| Shortcut | Description |
| -------- | ----------- |
| Alt-LShift-PrtSc | toggle high contrast |
| Alt-Tab | cycle through open windows |
| Ctrl-c (or Shift-Enter) | copy |
| Ctrl-v | paste |
| Fn-F7 | reverse search through history |
| Windows-e | open Windows Explorer |
| Windows-r | open Run dialog |
