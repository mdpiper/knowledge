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

In a command window,
use *Shift-Enter* to copy
and *Ctrl-v* to paste.

## Build/install with CMake

To access the VS linker,
`cmake` must be run
in a [Developer Command Prompt](https://docs.microsoft.com/en-us/dotnet/framework/tools/developer-command-prompt-for-vs)
(an Anaconda Prompt works, but not a Git Bash shell).

A typical configure-build-install sequence is:
```
mkdir _build && cd _build
cmake .. ^
  -G "NMake Makefiles" ^
  -DCMAKE_INSTALL_PREFIX=%CONDA_PREFIX% ^
  -DCMAKE_BUILD_TYPE=Release
cmake --build . --target install --config Release
```
See also a conda-forge feedstock [bld.bat](https://github.com/conda-forge/ecsimplesnow-feedstock/blob/master/recipe/bld.bat), for example.
