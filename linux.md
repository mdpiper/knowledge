# Linux

Notes from years and years of using it.

There may be some crossover here with my
[bash](./bash.md) notes.


## Get distro info

Use `uname` and/or `lsb_release`:

    $ uname -a
    $ lsb_release -a

On RH systems, also try:

    $ cat /etc/redhat-release


## Get shell

Which shell am I currently using (`bash`, `tsch`, `zsh`, etc.)?
Check with:

    $ echo -0


## Building a package from source

The three canonical steps:

    $ ./configure
    $ make
    $ [sudo] make install

By convention,
packages built from source are installed in **/usr/local**,
whereas packages from a package manager
(like [yum](./yum.md))
are installed in **/usr**.
The `configure` script usually has a `--prefix` option
to change the install location.


## See shared library dependencies

Use `ldd` to get a list of shared libraries that an executable
or a library depends on.
This command is invaluable for determining what dependencies are missing,
and which libraries are being used
(I've seen libraries from default locations,
like **/usr/lib64**,
pulled in before a local library).

Example:

```bash
$ ldd /usr/lib64/libgfortran.so.3
    linux-vdso.so.1 =>  (0x00007fff5cf3d000)
    libm.so.6 => /lib64/libm.so.6 (0x00000034df800000)
    libc.so.6 => /lib64/libc.so.6 (0x00000034de800000)
    /lib64/ld-linux-x86-64.so.2 (0x00000034de400000)
```

Another example:

```bash
$ cd ~/projects/wunderkammer
$ gfortran convert_array_indices.f90
$ ldd a.out
    linux-vdso.so.1 =>  (0x00007fff61377000)
    libgfortran.so.3 => /home/mpiper/anaconda2/lib/libgfortran.so.3 (0x00007f86e0eaa000)
    libm.so.6 => /lib64/libm.so.6 (0x00000034df800000)
    libgcc_s.so.1 => /home/mpiper/anaconda2/lib/libgcc_s.so.1 (0x00007f86e0c93000)
    libquadmath.so.0 => /home/mpiper/anaconda2/lib/libquadmath.so.0 (0x00007f86e0a57000)
    libc.so.6 => /lib64/libc.so.6 (0x00000034de800000)
    /lib64/ld-linux-x86-64.so.2 (0x00000034de400000)
```

See below for how I forced the linker to use
the **libgfortran.so.3** and **libquadmath.so.0** libs
in my Anaconda distro.


## Locate shared libraries

I like Fortran.
I use `gfortran`, provided by the GCC.
I also like Anaconda.
I installed GCC through Anaconda.
However,
when I tried to use `gfortran`,
the linker complained that a library (**libquadmath.so.0**) was missing.
Where is it?
I used `locate` to find it:

```bash
$ locate libquadmath.so.0
    /home/mpiper/anaconda2/lib/libquadmath.so.0
    /home/mpiper/anaconda2/lib/libquadmath.so.0.0.0
    /home/mpiper/anaconda2/pkgs/gcc-4.8.5-7/lib/libquadmath.so.0
    /home/mpiper/anaconda2/pkgs/gcc-4.8.5-7/lib/libquadmath.so.0.0.0
    /var/lib/mock/epel-7-x86_64/root/usr/lib64/libquadmath.so.0
    /var/lib/mock/epel-7-x86_64/root/usr/lib64/libquadmath.so.0.0.0
```

Ah, so it's in my anaconda distro, but why isn't it being found?
I'll fix this next.

## Set shared library dependencies

To force the libquadmath shared library in my Anaconda distribution
to be found,
I used `ldconfig`:

```bash
$ ldconfig -v /home/mpiper/anaconda2/lib
```


## Users and groups

Create a new group with `groupadd`.

    groupadd csdms

Create a new user, setting their default group
and creating their home directory.
Unlock the new user by setting their password.

    useradd mpiper -c "Mark Piper" -g csdms -m
	passwd mpiper

Add the user to the "wheel" group,
making them a sudoer.

    usermod -aG wheel mpiper

Find what groups a user belongs to.

    groups mpiper

Set the primary group of an existing user.

    usermod -g wmt wmtuser

*References*

* [Creating a group](https://www.centos.org/docs/5/html/5.1/Deployment_Guide/s2-groups-add.html) [centos.org]
* [Creating a user](https://www.centos.org/docs/5/html/5.1/Deployment_Guide/s2-users-add.html) [centos.org]
* [Making a sudo user](https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-centos-quickstart) [digitalocean.com]


## Filesystem Hierarchy Standard (FHS)

The FHS specifies the names and roles of the standard directories in a Linux distribution.
The references below give some guidance as to what goes where.

*References*

* [Wikipedia entry](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)
* [Official home, including specification](https://wiki.linuxfoundation.org/lsb/fhs)
* [Some random old RH docs](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-en-4/s1-filesystem-fhs.html)


## GNU Screen

`screen` is a terminal multiplexer.
With it, you can create multiple virtual windows, each with a shell.
It's especially useful for running multiple jobs on a remote host;
jobs are automatically run in the background,
and if the remote connection is lost,
the jobs continue to run.
On reconnection to the host, you can reconnect to the `screen` session.

There are a bunch of control sequences to work with windows
in a `screen` session.
See the references below.

A typical `screen` workflow:

1. Start `screen`
1. Run a job
1. Detach from `screen` with `Ctrl-a + Ctrl-d`
1. Do other stuff
1. Reattach to the session with `screen -r`

*References*

* A helpful [overview](https://wiki.archlinux.org/title/GNU_Screen) from Arch Linux
* The [Screen user's manual](https://www.gnu.org/software/screen/manual/screen.html)
