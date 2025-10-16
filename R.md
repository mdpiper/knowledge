# R

I've wanted to learn R for awhile.
Here's my chance.
I have a [repository of basic examples](https://github.com/mdpiper/r-examples)
which I'm using for several of the notes below.

## Packaging

I have code in the file `accumulator.R` that I'd like to share
(and simplify use for myself).
Like Python, package the code.

Start a package with `package.skeleton()`.
```R
package.skeleton(name="accumulator", code_file="accumulator.R")
```
This creates a directory tree:
```console
accumulator
├── DESCRIPTION
├── man
│   ├── accumulator-package.Rd
│   └── Accumulator.Rd
├── NAMESPACE
├── R
│   └── accumulator.R
└── Read-and-delete-me
```
The source code file is copied into the directory tree.
A list of next steps to complete the package is given in
`accumulator/Read-and-delete-me`.
In particular, `DESCRIPTION` and `NAMESPACE` need to be filled out.

*Note*: The `package.skeleton()` is base R.
There's also the `create_package` function 
from the [usethis](https://usethis.r-lib.org/reference/create_package.html) library.
It integrates with RStudio.

### Check

For a comprehensive check of the new package, run (from a shell prompt)
```bash
R CMD check accumulator
```
Output is printed to the console and to a log directory, `accumulator.Rcheck`.
I iterated over this command, fixing the problems it noted.

### Install

Install the new package into the current R distribution so it can be imported.
```bash
R CMD install accumulator
```

In an R session, load the library.
```R
> library(accumulator)
Loading required package: R6
> Accumulator
<Accumulator> object generator
  Public:
    total: 0
    add: function (x = 1) 
    clone: function (deep = FALSE) 
  Parent env: <environment: namespace:accumulator>
  Locked objects: TRUE
  Locked class: FALSE
  Portable: TRUE
```

### Build

Build the package into a tarball with:
```bash
R CMD build accumulator
```

### References:

* [Writing R Extensions](https://cran.r-project.org/doc/manuals/R-exts.html) (cran.r-project.org)
* The [R Packages](https://r-pkgs.org/) book (r-pkgs.org) *(Written assuming use of RStudio.)*
* I looked at the directory structures of the [ggplot2](https://github.com/tidyverse/ggplot2) and [plotly.R](https://github.com/plotly/plotly.R) repositories (github.com)

## Change the R prompt

I wanted to change the default prompt from `>` to `R>`.
Do so locally with:
```R
options(prompt="R> ")
```

Place this command in `$HOME/.Rprofile` to make it stick.

## Reference copying

R behaves like Python in that references are copied.
Assigning one variable to another causes both to point to the same data,
unless instructed otherwise.
```R
> x = Accumulator$new()
> y = x
> y$add(5)
> c(x$total, y$total)
[1] 5 5
```
Although we didn't add to `x`,
both `y` and `x` point to the same data,
so updating one updates the other.

Override this behavior with the `$clone` method.
```R
> x = Accumulator$new()
> y = x$clone()
> y$add(5)
> c(x$total, y$total)
[1] 0 5
```
