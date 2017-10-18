# Conda

We use `conda` at CSDMS for all our Python needs.

## Install, uninstall, and list packages

    conda install ipython
    conda uninstall ipython
    conda list

## Search for packages to install

Fragments of package names can be used.

    conda search cca

## Using environments

Multiple Python versions can coexist in an Anaconda installation
by setting up environments for each.
For example,
I have an Anaconda installation with Python 3.5.
I can create a Python 2.7 environment with

    conda create -n py27 python=2.7

where "py27" is the name I gave the environment.

Activate the environment with

    $ source activate py27
	$ python --version
	Python 2.7.14 :: Anaconda, Inc.

Deactivate the environment with

    $ source deactivate
	$ python --version
	Python 3.5.4 :: Anaconda, Inc.

List all available environments

    conda env list

I can install packages into an activated environment with `conda`.

Remove an environment, including all installed packages, with

    conda remove --n py27 --all

**Reference**

* https://conda.io/docs/user-guide/tasks/manage-environments.html


## Conda packaging

(Perhaps this should be a separate note?)

See http://conda.pydata.org/docs/building/recipe.html,
and, from there,
http://conda.pydata.org/docs/build_tutorials/pkgs2.html.

The `conda-build` package is required:

    conda install conda-build

Two files are used: **build.sh** and **meta.yaml**.
For a standard Python package,
the contents of **build.sh** are typically just
`python setup.py install`.
Information on the **meta.yaml** file
can be found [here](http://conda.pydata.org/docs/building/meta-yaml.html).
I also found good information at
http://conda-test.pydata.org/docs/build.html,
including more detailed info on the **meta.yaml** file
and patching.

If a package has already been uploaded to PyPI,
use `conda skeleton` to create the **build.sh** and **meta.yaml** files;
e.g.:

    conda skeleton pypi basic-modeling-interface


### Example

Install a local package into a local `conda` install:

    cd ~/z  # This is where I have a conda distro
    conda build /Users/mpiper/projects/csdms-stack/conda-recipes/bmi-java
	conda install --use-local bmi-java

Build and install a package using a channel to handle dependencies:

    cd ~/z  # This is where I have a conda distro
    conda build /Users/mpiper/projects/csdms-stack/conda-recipes/cca-babel -c csdms
	conda install --use-local cca-babel -c csdms

