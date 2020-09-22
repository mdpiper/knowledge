# Conda

We use `conda` at CSDMS for all our Python needs.

## Install and uninstall packages

    conda install ipython
    conda uninstall ipython

Install from a channel:

    conda install pymt -c conda-forge

Install from a label in a channel:

    conda install pymt -c conda-forge/label/dev

## List installed packages

    conda list

Export the list of packages for future use:

    conda list --export > requirements.txt


## Search for packages to install

Search is only performed on current OS.
Fragments of package names can be used.

    conda search cca

Search channels:

    conda search pymt -c conda-forge

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
    $ which python
	/home/mpiper/anaconda/envs/py27/bin/python

Deactivate the environment with

    $ source deactivate
	$ python --version
	Python 3.5.4 :: Anaconda, Inc.
    $ which python
	/home/mpiper/anaconda/bin/python

List all available environments

    conda env list

I can install packages into an activated environment with `conda`.

Remove an environment, including all installed packages, with

    conda remove -n py27 --all

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

Remove all built packages from a local conda distribution:

    conda build purge
	conda build purge-all

### Example

Install a local package into a local `conda` install:

    cd ~/z  # This is where I have a conda distro
    conda build /Users/mpiper/projects/csdms-stack/conda-recipes/bmi-java
	conda install --use-local bmi-java

Build and install a package using a channel to handle dependencies:

    cd ~/z  # This is where I have a conda distro
    conda build /Users/mpiper/projects/csdms-stack/conda-recipes/cca-babel -c csdms
	conda install --use-local cca-babel -c csdms

## Conda compilers

Instead of relying on the OS to supply compilers,
conda has its own:

Linux:

* gcc_linux-64
* gxx_linux-64
* gfortran_linux-64

macOS:

* clang_osx-64
* clangxx_osx-64
* gfortran_osx-64

They can be conda installed.

I installed MacOSX10.9.sdk to **/opt**.
Use it in **conda_build_config.yaml** with:

```yaml
CONDA_BUILD_SYSROOT:
  - /opt/MacOSX10.9.sdk        # [osx]
```

Use these compilers in conda build with jinja2 templates:

```yaml
requirements:
  build:
    - {{ compiler(‘c’) }}
```

**References**

* https://www.anaconda.com/utilizing-the-new-compilers-in-anaconda-distribution-5/
* https://conda.io/projects/conda-build/en/latest/source/resources/compiler-tools.html
* https://conda.io/projects/conda-build/en/latest/source/resources/variants.html#conda-build-variant-config-files
