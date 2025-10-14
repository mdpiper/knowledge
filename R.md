# R

I've wanted to learn R for awhile.
Here's my chance.

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

### References:

* [Writing R Extensions](https://cran.r-project.org/doc/manuals/R-exts.html) (cran.r-project.org)
