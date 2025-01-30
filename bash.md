# Bash

Despite many years of using Unix/Linux,
I don't remember more than the basics
of shell scripting with
[Bash](http://www.gnu.org/software/bash/).
I hope this document will bolster my memory.

Note that there's some crossover with my
[Linux](./linux.md) notes.


## Documentation and examples

Primary:

* [BashGuide](http://mywiki.wooledge.org/BashGuide) [wooledge.org]
* [List of Bash online-tutorials](http://wiki.bash-hackers.org/scripting/tutoriallist) [bash-hackers.org]
* [Bash Guide for Beginners](http://www.tldp.org/LDP/Bash-Beginners-Guide/html/)
  [tldp.org]
* [Advanced Bash Scripting Guide](http://www.tldp.org/LDP/abs/html/) [tldp.org]


## Get exit status of previous command

Helpful for diagnosing errors.

    $ ls
    $ echo $?
    0

A `0` value traditionally indicates success; anything else is failure.

I've also used this to exit a script after a test:

	#!/usr/bin/env bash
	test -d $target
	exit $?


## Grouping commands

More than one command can be entered on a line with a semicolon `;`:

	$ mkdir build ; cd build

Using the `&&` operator checks that the first command succeeds before
executing the second:

	$ mkdir build && cd build


## Capturing console output

Redirect standard output to a file with `>`:
```
$ hydrotrend > run.log
```

Redirect standard error with `2>`
```
$ hydrotrend 2> run.log
```

Redirect both standard error and standard output with `&>`
```
$ hydrotrend &> run.log
```

Redirect stdout and stderr to separate files with `1>` and `2>`
```
$ hydrotrend 1> hydrotrend.stdout 2> hydrotrend.stderr
```


## Copying a directory

When using `cp` to copy a directory, a trailing `/` will copy the
directory contents, but not the directory itself; e.g.:

	$ cp ~/incoming . 	# copies directory + contents

versus

    $ cp ~/incoming/ .	# copies only contents

This has vexed me for years!


## Recursively create subdirectories

Recursively create subdirectories with the `-p` flag:

	$ mkdir -p mpiper/examples/datastructures
	$ mkdir -p foo/{src,lib,doc,etc}

Don't include spaces between the comma-delimited subdirectories!


## Diff with color

The standard bash `diff` command doesn't have a color option,
at least with the older versions of bash I use; e.g., on ***beach***.
Use `git diff` instead:

    $ git diff bmi_ilamb.yaml bmi_ilamb.yaml.0

This command will work even outside of a git repo.
(Tested on ***beach***.)


## Exclude from directory listing

Say I want to exclude all files and and directories that begin with "step"
from a directory listing.

On Linux:

	$ ls --ignore="step*"

On Mac:

	$ ls | grep -iv "step"


## Getting output from a command

This is my new favorite thing:

    $ export FC=$(which gfortran)
	$ echo $FC
	/usr/bin/gfortran


## Grepping with grep

Recursively search subdirectories for a pattern:

	$ grep -i -r "python" .

Don't forget the path specification at the end.

Grep for multiple strings with an OR operator:

    $ conda list -n dev | grep -E 'cython|compiler'

The `-E` flag is for extended grep.
It means we don't have to escape the `|` operator.

Include only files that match a glob:

    $ grep -r --include=*.ncl ILAMB_PARA_SETUP .

Grep for a hidden file by escaping the period.
For example,
to search for `.wmt` in all files in the **opt** directory:

    $ grep -r "\.wmt" opt

Exclude directories:

    $ grep -r --exclude-dir={convert,relationships} --include="*\.ncl" \"nee\" .

I am the grep master. Don't mess with me.


## Recursive chmod

See [http://chrisgilligan.com/wordpress/chmod-recursive-files-only-directories-type/](http://chrisgilligan.com/wordpress/chmod-recursive-files-only-directories-type/).


## Substitution with sed

By way of example,
in all **.cca** files,
locate the line with a path to a **.la** file
and change the extension to **.so**:

	$ sed -i 's@\.la@.so@' %{buildroot}%{_datadir}/cca/*.cca

Further,
by setting `-i`,
do the substitution in place
(overwriting the original contents of the files).

Globally
replace "%{buildroot}"
in all **.la** files:

	$ sed -i 's@%{buildroot}@@g' %{buildroot}%{lib32dir}/*.la

Note that the `s` command always needs three delimiters,
and they can be any character
that doesn't appear in the file.
I use `@`;
the characters `\` and `|` are often used, as well.

If the character "c" appears at the start of a line,
replace it with the character "!":

    $ sed -i 's@^c@!@' cgnslib_f.h

Note that on macOS,
the `-i` flag needs an extension for a backup file.
E.g.,

    $ sed -i.orig 's@^c@!@' cgnslib_f.h

produces

```
cgnslib_f.h       # modified file
cgnslib_f.h.orig  # original file, backed up
```

## Extraction with sed

Extract the value on the 12th line of the file **HYDROASCII.QS**:

	$ sed -n '12p' HYDROASCII.QS > results.tmp


## Extraction with grep and cut

I needed to extract a value from a tab-delimited table in a text file.

I used `grep` to locate the header `Qsbar` for the value I wanted.
The `-A` option gives _n_ lines
(in this case n = 1)
after the match.
This is the return:

	Ep	Outlet	Qbar	Qsbar	Qsmean/outlet	Qpeak	TEbas. TEres.
	1	0(100%)	78.86	32.01			2911.9	0.0   0.0

I want the value `32.01`.
I used `tail` to get the last line returned from `grep`,
and then `cut` to extract the fourth field
(tab-delimited is the default for the `-f` option)
from the line.
The command and result:

	$ grep -A 1 Qsbar HYDRO.LOG | tail -1 | cut -f 4
	32.01

OMG am I good.


## Remove a fixed-width column from a text file

Use `colrm`, which I swear I'd never seen before.

For example, to remove characters 9-18 in each row
of the file **foo.txt**:

	$ cat foo.txt | colrm 9 18 > foo_new.txt


## Tests and conditionals

I keep coming back to this page:
[http://mywiki.wooledge.org/BashGuide/TestsAndConditionals](http://mywiki.wooledge.org/BashGuide/TestsAndConditionals).


## Copying files with rsync

I can't believe I haven't used `rsync` before.
It uses a syntax similar to `rcp` and `scp`.
For example,
to copy the compiled WMT module from my development machine
to my home directory on the webserver, use:

	$ rsync -avz war mapi8888@river:~

`rsync` only copies the differences between the source and target directories.
Further, with the `-z` flag,
the directory contents are compressed when transferring.


## HTTP POST request with curl

Use `curl` to send an HTTP POST request.

For example,
the WMT API web page
[https://csdms.colorado.edu/wmt/api-dev/run/update](https://csdms.colorado.edu/wmt/api-dev/run/update)
can be used to post a message about a model run,
given its id.
The equivalent command in `curl` is:

    $ curl https://csdms.colorado.edu/wmt/api-dev/run/update -d uuid=faa3888f-f38b-4a3c-9191-6ec23a757f47 -d status=running -d message=rly fast

The `-d` (or `--data`) option
is used to string together the data for the form.
The above data would yield the post chunk
`uuid=faa3888f-f38b-4a3c-9191-6ec23a757f47&status=running&message=rly%20fast`.

## Pretty-print a path

From [commandlinefu.com](http://www.commandlinefu.com/commands/view/13697/pretty-print-path-separate-path-per-line):

    $ tr : '\n' <<< $PATH

Works for any path variable.
I made this into a personal library script, `showpath`.

## Modules

Show what kernel modules are currently being used:

    $ module list

Show what modules are available:

    $ module avail

Load a module:

    $ module load python/2.7

On ***beach***, I've included this command in my **.bashrc**:

    source /usr/local/modules/init/bash

to access CSDMS modules.


## Operate on results returned by find

Use the `-exec` flag to perform operations on the results returned
from `find`.

For example, get a long directory listing on the results of searching
files named **wmt.yaml**:

    $ find . -name wmt.yaml -exec ls -l {} \;

Here, `{}` is a result from `find`,
and `\;` terminates the operation.

Here's what I actually wanted to do:

    $ sudo find . -name wmt.yaml -exec chmod g+w {} \;

And it worked!


## Delete files returned by find

Use the `delete` flag:

    $ find . -iname *.tmpl -delete


## Traps

Traps are like a CATCH in IDL;
they execute a command when a signal is caught.
For example,
if the following `trap` statement is in a script:

    trap "rm -rf working" EXIT
	
then, when the script exits (successfully or not),
the command `rm -rf working` is executed.

Reference: http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_12_02.html.


## Reset hash table

Bash maintains a table of recently used commands and their locations.
The `hash` command lists it:

```
$ hash
hits    command
   7    /usr/bin/git
   1    /bin/rm
   1    /usr/bin/sudo
   5    /bin/ls
   1    /Applications/anaconda2/bin/clear
```

Bash hashes this table to find a command
instead than searching the path.

If I install software that changes the location of a command,
I want bash to find it in its new location,
not the location given in the table.
Reset the hash table with

    $ hash -r


## Recursive sort of disk usage

What directories are using the most space?
Find out with
```
$ du -h | sort -h
```

To descend only one level in the filesystem, use
```
$ du -h -d1 | sort -h
```

For only the top ten:
```
$ du -h -d1 | sort -h | tail
```

So many pipes.

## Keep a job alive after logout

To keep a job alive after logging out of a machine
(or to prevent getting timed out of a JupyterHub),
use the `nohup` command.

For example, running the [piperformance](https://github.com/mdpiper/piperformance) script:

```bash
nohup python piperformance.py
```

By default, output is appended to the file **nohup.out**.

Override the default output file through redirection (also capturing stderr and backgrounding the job):

```bash
nohup python piperformance.py &> output.log &
```
