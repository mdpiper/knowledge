# Windows

Notes on using Windows, especially at the command prompt.

## Commands

The `set` command behaves like `echo`:
```
> set CONDA_PREFIX
CONDA_PREFIX=C:\Users\Mark\Anaconda3\envs\bmic
```
It also matches a prefix:
```
> set CONDA_
CONDA_DEFAULT_ENV=bmic
CONDA_EXE=C:\Users\Mark\Anaconda3\Scripts\conda.exe
CONDA_PREFIX=C:\Users\Mark\Anaconda3\envs\bmic
CONDA_PROMPT_MODIFIER=(bmic)
CONDA_PYTHON_EXE=C:\Users\Mark\Anaconda3\python.exe
CONDA_SHLVL=1
```
It's also used to actually set an environment variable:
```
> set "BMIF_VERSION=2.0"
```


A list of other commands is given in the table below.

| Command | Bash equivalent | Description |
| ------- | --------------- | ----------- |
| dir | ls | Directory listing |

## Keyboard shortcuts

| Shortcut | Description |
| -------- | ----------- |
| Alt-Tab | cycle through open windows |
| Ctrl-c (or Shift-Enter) | copy |
| Ctrl-v | paste |
| Fn-F7 | reverse search through history |
| Windows-e | open Windows Explorer |
| Windows-r | open Run dialog |



## Build/install with CMake

See my [cmake](./cmake.md) notes.
