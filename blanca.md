# Blanca

The summit and blanca clusters are administered by
CU's Research Computing (RC) facility.

User Guide: https://www.rc.colorado.edu/support/user-guide


## Login

To login, use your CU IdentiKey:

    ssh <username>@login.rc.colorado.edu

The password prompt is interesting.
Use your IdentiKey password,
but prefix it with `duo:`,
like this:

    duo:<password>

This will trigger a notification on the Duo app.
Confirm your login on the app,
and you'll be logged in.

To login to our nodes on blanca, use:

    ssh <username>@blogin01.rc.colorado.edu

and follow the same instructions for entering a password as above.

Note that I can login without a CU VPN connection.


## Compile nodes

Note that in addition to login and compute nodes,
summit has compile nodes.
When logged in to summit, access a compile node with:

    ssh compilenode

Note that blanca doesn't have compile nodes.


## Modules

Summit uses modules. Check for available modules with

    module avail

Load a more recent version of git (v2.4.2) than the default

    module load git

See how to load a more recent Python with

    module spider python/2.7.10

*References:*

* https://www.rc.colorado.edu/support/user-guide/modules.html


## File transfer

Summit and blanca only allow `scp` and `sftp`, not `rsync`.
Further,
it appears that the file transfer can only be initiated from
from the remote machine,
not summit or blanca.
For example, to transfer the file readme.md from a location on beach:

    scp readme.md mapi8461@login.rc.colorado.edu:~/wmt/_testing

You'll have to enter your password + Duo as above,

*References:*

* https://www.rc.colorado.edu/support/user-guide/file-transfer.html


## Job control

Slurm!

*References:*

* [Batch queuing and job scheduling](https://www.rc.colorado.edu/support/user-guide/batch-queueing.html)
* [Interactive jobs](https://www.rc.colorado.edu/support/user-guide/batch-queueing.html#interactive_jobs)


## Installing local software

I install software I build from source into 
**/projects/mapi8461/local**.


### Emacs

I built Emacs 25.3 from source.
Here's the `configure` command:

```bash
./configure --prefix=/projects/mapi8461/local --with-x-toolkit=no --with-xpm=no --with-png=no --with-gif=no --with-tiff=no LDFLAGS='-L/projects/mapi8461/local/lib'
```

The required `ncurses` library wasn't installed on blanca,
so I built it from source;
hence the `LDFLAGS` argument to `configure`.

**Alternate**

RC also built an Emacs for me. Use

    module load singularity
    singularity exec /projects/mapi8461/local/bin/emacs.img emacs

They also created a module on the rhel7 nodes

    module load emacs
