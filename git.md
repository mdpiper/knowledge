# Git

Github maintains a `git` [cheatsheet](https://help.github.com/articles/git-cheatsheet)[PDF].

Here is a
[note](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
on style for `git` commit messages.

Github recommends using `git >= 1.7.20`.

## Configuration

Tell `git` about me:

	$ git config --global user.name 'Mark Piper'

I did this when I set up my Github account.
To check:

	$ git config --get user.name
	Mark Piper
	$ git config --get user.email
	mark.piper@colorado.edu

Set the editor (if different from default in shell):

	$ git config --global core.editor emacs

Why does Github repeatedly ask for my username and password?
Because I'm using an [HTTPS remote URL](https://help.github.com/articles/why-is-git-always-asking-for-my-password).
I have the option to set up [password caching](https://help.github.com/articles/set-up-git#password-caching), but I should really be using [ssh keys](https://help.github.com/articles/connecting-to-github-with-ssh/).

## Clone

To clone a repository to my computer, use either the `https` protocol:

	$ git clone https://github.com/mdpiper/wmt.git

which requires credentials on each push, or use the `git` protocol:

	$ git clone git://github.com/mdpiper/wmt.git wmt-mpiper	

which doesn't allow push (i.e., it's read-only), or the `git@github.com` protocol:

        $ git clone git@github.com:mdpiper/wmt.git wmt-mpiper

which uses ssh keys for authentication.

A directory is created locally with the name of the clone.
This is the _working directory_.
Specifying the name of the clone (as in the second example) is optional.


## Status

Check the status of files in the working directory:

	$ cd wmt-mpiper
	$ git status -s
	M  README.md


## Commit

Commit changes from the working directory:

	$ git commit -a
	[master 7b69df9] Update README.md text
	1 file changed, 9 insertions(+), 4 deletions(-)
	rewrite README.md (99%)

Can also commit individual files:

	$ git commit README.md -m "Update README file"

Note that, unlike subversion, this isn't enough
-- I still need to push the commits back to
the remote repository.


## Back out a change before commit

For an individual file:

	$ git checkout -- ../bmi/vars.py

For everything, from the working directory, call:

	$ git checkout -- .


## Delete the last unpushed commit

Delete the most recent commit:

	git reset --hard HEAD~1

Delete the most recent commit, but without destroying the work you've done:

	git reset --soft HEAD~1


## Revert a commit

Revert an earlier commit by specifying its id:

    git revert f04f02b

Note that this makes a new commit.


## View commit log

From the working directory, call:

	$ git log origin/master..HEAD

Here's a sample result:

	commit 344a44c3be20d130b80a280201f0d3e47cc05d30
	Author: Mark Piper <mark.piper@colorado.edu>
	Date:   Wed Aug 20 11:13:05 2014 -0600

		Allow tagged versions of source to be downloaded and built
		This commit partially addresses #1. Still need to:
		* add versioning to patches
		* make patches for hydrotrend 3.0.2 and cem 0.2

	commit 1d8a9fc4d4bbaa34e304d26582844a790df13e38
	Author: Mark Piper <mark.piper@colorado.edu>
	Date:   Wed Aug 20 10:51:00 2014 -0600

		Convert local variables to instance variables
		Helps readability of code.

Simpler: just the last three commits, please:

	$ git log -3


## Get id of latest commit

Get the SHA of the latest commit in the current branch with:

    $ git rev-parse HEAD
	23a35f01c8f416f93ddd7b79f4620647ae936750

or, better, the abbreviated SHA:

    $ git rev-parse --short HEAD
	23a35f0


## Remotes

What remote repository am I working on?

	$ git remote
	origin
	$ git remote -v
	origin	https://github.com/mdpiper/wmt.git (fetch)
	origin	https://github.com/mdpiper/wmt.git (push)

Add the upstream repo to the list of remotes for the local repo:

    $ git remote add upstream https://github.com/csdms/wmt.git

now:

	$ git remote -v
	origin	https://github.com/mdpiper/wmt.git (fetch)
	origin	https://github.com/mdpiper/wmt.git (push)
	upstream	https://github.com/csdms/wmt.git (fetch)
	upstream	https://github.com/csdms/wmt.git (push)


## Push

Publish commits back to the remote:

	$ git push origin master
	Username for 'https://github.com': mdpiper
	Password for 'https://mdpiper@github.com':
	Counting objects: 5, done.
	Delta compression using up to 2 threads.
	Compressing objects: 100% (3/3), done.
	Writing objects: 100% (3/3), 570 bytes | 0 bytes/s, done.
	Total 3 (delta 1), reused 0 (delta 0)
	To https://github.com/mdpiper/GWTandHTTP.git
		072d32f..7b69df9  master -> master

This appears to be the same as "Sync" in the Github app.

On ***siwenna***,
I have authentication issues.
For example:

	$ git push
	error: The requested URL returned error: 403 Forbidden while accessing https://github.com/csdms-contrib/storm/info/refs
	fatal: HTTP request failed

The solution:

	$ git remote set-url origin https://mdpiper@github.com/csdms-contrib/storm.git

Not only does this work,
but now I only need to enter my password,
not my username and password.

On ***beach***,
when not using X,
I get a GTK warning:

    $ git push

    (gnome-ssh-askpass:14794): Gtk-WARNING **: cannot open display:

Fix this with:

    $ unset SSH_ASKPASS

**References:**

* [http://stackoverflow.com/a/11368701/1563298](http://stackoverflow.com/a/11368701/1563298) [stackoverflow.com]
* [http://stackoverflow.com/a/16104473/1563298](http://stackoverflow.com/a/16104473/1563298) [stackoverflow.com]


## Branch

See what branches are available:

	$ git branch
	* master
	  styling-and-appearance
  
Switch between branches:

	$ git checkout styling-and-appearance
	Switched to branch 'styling-and-appearance'
	$ git branch
	  master
	* styling-and-appearance
	
Let's say I've made some changes,
and I'd like them to be committed to a new branch.
Start by creating the new branch:

    $ git branch -b add-topoflow
	M	scripts/install
	Switched to a new branch 'add-topoflow'

Add the changed files to the new branch:

    $ git add scripts/install

Then commit the changes:

    $ git commit -am "Update install script for TopoFlow repo"

Push the new branch, with its changes, back to the remote,
creating a new branch on the remote:

    $ git push -u origin add-topoflow

Count the number of commits the branch is ahead of master:

    $ git rev-list master.. --count
	1

See also **Rebase** below.


## Change the name of a branch

From within the branch, use:

    $ git branch -m <newname>

The "m" is for move.


## Diff a file across branches

I want to see changes that were made to a file in a branch.
I can compare it with the version in the master branch with:

    $ git diff mdpiper/prep-for-componentization master -- parameters.yaml


## Stash changes

This is neat.
I can stash changes I've made on a branch with:

    git stash

then retrieve them later with:

    git stash pop


## Fetch a specific file or directory from a repo

1. `cd` to the root directory of the repo
1. `git fetch`
1. `git checkout HEAD path/to/dir/or/file`

**Reference:**

* [http://stackoverflow.com/a/4048993/1563298](http://stackoverflow.com/a/4048993/1563298)


## Fetch a specific branch from a repo

Let's say I've just cloned a repo; e.g., **wmt-client**.
Only the master branch is fetched, by default.
To get another branch, say `update-parameter-table`:

1. `cd` to the root directory of the repo
1. `git fetch`
1. `git checkout update-parameter-table`

Note that after the fetch,
the name of the other branch still won't be shown.


## Tag a release

Make sure all version strings match
(e.g., in package definition, in conda recipe, in docs),
then commit (don't `[ci skip]`!)
and push changes.

Create a tag for the release that starts with "v"; e.g.:

    git tag v0.2.1 -m 'Optionally add a message'

Push the tag to the remote:

    git push --tags

Note that Travis will start a build based on the new tag.

To delete a tag, both locally and at the origin, use:

    git tag --delete v0.1
	git push --delete origin v0.1.1

I never make mistakes.


## Keep a feature branch in sync with the master branch

I have a repository,
[csdms/component_metadata](https://github.com/csdms/component_metadata),
that has `master` and `hydrology` branches.
I want changes to `master` to be synced
to `hydrology` when appropriate.
Here's my workflow.

1. Start in the `master` branch.

        git checkout master

1. Make changes to files and commit.

1. Push changes to both origin (mdpiper) and upstream (csdms) repositories.

        git push origin master
		git push upstream master

1. Switch to the `hydrology` branch.

        git checkout hydrology

1. Fetch and merge from origin/master.

        git fetch
		git merge origin/master

1. Push changes to origin and upstream repositories.

        git push origin hydrology
		git push upstream hydrology

I could also push only to origin,
then pull request into the upstream repository,
but this is convenient for quick changes.


## Rebase

I've been persisting a few branches on `mdpiper/wmt` (e.g.,
`jso-data-types`, `data-transfer`, etc.) instead of deleting them
after a merge. However, I noticed that they were falling behind the
master branch because of messages applied by Github during the
merge. The answer is to rebase the branch:

	$ git checkout jso-data-types
	$ git rebase master
	$ git push

And now the branch is in sync with the master.

Another case is I've been working on a branch, `foo`,
which is ahead of `master`.
I then commit some changes to `master`.
Use `git rebase` to pull the changes from `master` into `foo`:

    $ git checkout foo
	$ git rebase master

This
[article](http://mettadore.com/analysis/a-simple-git-rebase-workflow-explained/)
helpfully explains branching and rebasing.


## Orphan branch

In the
[csdms-contrib/storm](https://github.com/csdms-contrib/storm)
repo,
I had code which I was no longer using
-- the original Fortran version of **storm**
and my "faithful" C translation
-- so I put these code lines into _orphan branches_.
They now have no parents
and are the root of a new history totally disconnected
from the other branches and commits.

Here are the steps that I used to create the orphan branch
I named `reference/original-fortran`:

	git clone https://github.com/csdms-contrib/storm.git
	git checkout --orphan reference/original-fortran
	git rm -rf .   	# Yes, I deleted everything.
	...
	git add --all   # Then I added back what was needed.
	git commit -am "Import original Fortran source"
	git push origin reference/original-fortran

**Reference:**

* This is for GitHub Pages, but it's exactly what I needed:
  [https://help.github.com/articles/creating-project-pages-manually/](https://help.github.com/articles/creating-project-pages-manually/)


## Submodules

After cloning a repository that includes submodules,
run:

    $ git submodule init
	$ git submodule update

To pull in changes,
change to the submodule directory and run:

    $ git pull

then change out of the submodule directory and run:

    $ git commit

to lock in changes.

Add a new submodule to a repository with:

    $ git submodule add https://github.com/csdms/bmi-fortran

This will clone the repo and update the **.gitmodules** file.

Moving a submodule is a [pain](http://stackoverflow.com/a/6310246/1563298). [stackoverflow.com]

**Reference:**

* [https://git-scm.com/book/en/v2/Git-Tools-Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)


## Move pushed commits to a new branch

Several commits
that should have undergone review in a pull request
had been pushed into the `master` branch of a repository.
Here's how I extracted them into a new topic branch:

    $ git clone git@github.com:permamodel/permamodel.git
	$ git branch wk1984/add-ku-model-geo
	$ git reset --hard 494e9a8  # this is where I want HEAD
	$ git checkout wk1984/add-ku-model-geo

Then I pushed both branches:

    $ git push origin wk1984/add-ku-model-geo
	$ git checkout master
	$ git push origin master --force

The master branch is now back at the point in history before
the errant commits,
and the topic branch `wk1984/add-ku-model-geo`
can be made into a pull request.


## Pull request

I wanted to merge `csdms/wmt` into my forked version `mdpiper/wmt`,
which is way out of date.

Start by entering the `mdpiper/wmt` working directory and adding
csdms/wmt as a remote:

	$ git remote add csdms-wmt https://github.com/csdms/wmt.git
	$ git remote
	csdms-wmt
	origin

Next, try to pull in the `csdms/wmt` version:

	$ git pull csdms-wmt master

This failed because of several conflicts with out-of-date code.
For example, here's one of the messages:

	Auto-merging gui/src/edu/colorado/csdms/wmt/client/WMT.java
	CONFLICT (content): Merge conflict in gui/src/edu/colorado/csdms/wmt/client/WMT.java

I solved this by checking out the `csdms/wmt` version of the file, then
committing it. For example:

	$ cd gui/src/edu/colorado/csdms/wmt/client
	$ git checkout --theirs WMT.java
	$ git add WMT.java

[This](http://stackoverflow.com/a/3407920) StackOverflow thread helped me.

Once all conflicts had been resolved, commit the changes:

	$ git commit -m "Using csdms versions of files"
	[master 327ad0e] Using csdms versions of files

And check the pull result:

	$ git pull csdms-wmt master
	From https://github.com/csdms/wmt
	 * branch            master     -> FETCH_HEAD
	Already up-to-date.

Yay!

Last, I pushed the changes back to `mdpiper/wmt`:

	$ git push origin master
	Counting objects: 4976, done.
	Delta compression using up to 2 threads.
	Compressing objects: 100% (921/921), done.
	Writing objects: 100% (4859/4859), 551.91 KiB | 0 bytes/s, done.
	Total 4859 (delta 2658), reused 4784 (delta 2593)
	To https://github.com/mdpiper/wmt.git
		219d6b1..327ad0e  master -> master

Also see [this](https://help.github.com/articles/syncing-a-fork)
Github help article, "Syncing a fork".


## Resolve conflicts

This is an expanded take on the process described
in the **Pull request** section above.

    $ git checkout master  # get the latest master branch
	$ git pull
	$ git checkout my-branch  # where I've made changes that conflict
	$ git merge master
	$ emacs foo.py  # manually edit to fix conflict
	$ git add foo.py
	$ git commit
	$ git push
	$ git checkout master
	$ git branch -D my-branch  # delete feature branch locally
	$ git push origin :my-branch  # and on GitHub


## Merge a fork with the master, I

See these Github help articles:

 1. [Forking a repository](https://help.github.com/articles/fork-a-repo)
 2. [Using pull requests](https://help.github.com/articles/using-pull-requests) (didn't do much with this)
 3. [Merging a pull request](https://help.github.com/articles/merging-a-pull-request) (pulls changes from fork back to origin)

### Example

I forked `csdms/wmt` to `mdpiper/wmt`. I then made changes to my fork,
committed them, and pushed them back to the fork's master. In a
separate directory, I checked out the master branch `csdms/wmt`. I
changed to this directory and pulled from my fork with:

	$ cd ~/projects/wmt-csdms
	$ git pull https://github.com/mdpiper/wmt.git master

There were no merge conflicts, so I pushed the result back to `csdms/wmt`:

	$ git push origin master

And everything looks good on Github (although it didn't look the same
as what Eric did; I'll check with him).

**Update**: Although this works, it's not technically the correct way
  to do this. (I shouldn't have my own version of the upstream
  repository `csdms/wmt` to merge into.) Better information follows.


## Merge a fork with the master, II

Use the
[workflow](http://docs.astropy.org/en/stable/development/workflow/development_workflow.html)
provided by the AstroPy people.

1. Fork the repository.
2. Clone the fork to the local machine.

		$ git clone https://github.com/mdpiper/wmt.git
		
3. Add the upstream remote.

		$ cd wmt
		$ git remote add upstream https://github.com/csdms/wmt.git
		$ git remote -v

4. Fetch the upstream changes.

		$ git fetch upstream

5. Make a feature branch.

		$ git branch my-new-feature upstream/master
		$ git checkout my-new-feature
		$ git push origin my-new-feature
		$ git push --set-upstream origin my-new-feature

6. Make changes, test, and push back to the forked repository.

		$ git add my_new_file
		$ git commit -m 'NF - some message'
		$ git push

7. Go the the forked repository page on Github, switch to the branch
   with the changes, and click the "Pull request" button. This lets me
   merge my changes to the upstream repository. (Don't forget to click
   the "Merge" button before closing.)

	Alternately, you can use the command line.

	a. Check out a new branch and test changes.

		$ git checkout -b mdpiper-open-model-behavior master
		$ git pull https://github.com/mdpiper/wmt.git open-model-behavior

	b. Merge the changes and update on GitHub.

		$ git checkout master
		$ git merge --no-ff mdpiper-open-model-behavior
		$ git push origin master

8. The upstream and forked repositories are now out of sync, with the
   fork lagging. Pull from the upstream repo again and push to the
   fork.

		$ git checkout master
		$ git pull https://github.com/csdms/wmt.git master
		$ git push

	After this, the repositories should be in sync.
	
9. Delete the feature branch.

		$ git checkout master                 # change to another branch
		$ git branch -D my-unwanted-branch    # delete branch locally
		$ git push origin :my-unwanted-branch # delete on Github

These steps have worked successfully for me.


## Get repo stats

Get stats on the changes to a repo since a specified commit.
For example, in `wmt-selector`:

```
$ git diff --stat 06ff4cdd0a581dd23f9a36e251709602c8f088dc
 .gitignore |   2 +
 cover.css  | 207 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 csdms.ico  | Bin 0 -> 1150 bytes
 index.html | 100 ++++++++++++++++++++++++++++++++++
 4 files changed, 309 insertions(+)
```

## Break out a subdirectory into a new repository

I want to split a subpath into a new repository. E.g., I have:

	wmt
		.git/
		gui/
		server/

and I want:

	wmt
		.git/
		server/
		
	wmt-client (renamed from gui)
		.git/

There's a Github [help article](https://help.github.com/articles/splitting-a-subpath-out-into-a-new-repository) and a Stackoverflow [thread](http://stackoverflow.com/questions/359424/detach-subdirectory-into-separate-git-repository).


## Split a repository

I had a project, **pbs**,
containing server and executor code,
that I wanted to split into two repositories,
**pbs-server** and **pbs-executor**.
This isn't as simple as the case above
because the code was combined into a single Python package.

Here are the steps I used.

1. Clone **pbs** and create a new branch.

        git clone git@github.com:permamodel/pbs.git
	    cd pbs
		git branch mdpiper/tear-out-executor-code
		git checkout mdpiper/tear-out-executor-code

1. Refactor **pbs**, removing all executor code, leaving only server
   code. (This may be difficult and time-consuming.)

1. Push the branch back to origin.

        git push origin mdpiper/tear-out-executor-code

1. Create a new, empty, repository on GitHub for **pbs-server** and
   clone it.

        cd -
        git clone git@github.com:permamodel/pbs-server.git
		cd pbs-server

1. Set the `upstream` repository for **pbs-server** to **pbs**.

        git remote add upstream https://github.com/permamodel/pbs

1. Pull the feature branch from **pbs** into **pbs-server**.

        git pull upstream mdpiper/tear-out-executor-code

1. Remove the remote (optional) and push the changes to origin master
   on **pbs-server**.

        git remote rm upstream
        git push origin master

This takes care of the server-side code.
Now, for the executor code.

1. Return to the local **pbs** and checkout the master branch.

        cd -
		cd pbs
		git checkout master

    Recall that the master branch contains the original contents of **pbs**.

1. Create a new branch.

		git branch mdpiper/tear-out-server-code
		git checkout mdpiper/tear-out-server-code

1. Refactor **pbs**, removing all server code, leaving only executor
   code. (This may be difficult and time-consuming.)

1. Push the branch back to origin and PR it into the master branch.

        git push origin mdpiper/tear-out-server-code

1. Last, rename **pbs** to **pbs-executor** on GitHub..

The only possible issue
(and maybe this is a good thing)
is that the commit history up to the split
is preserved in both repositories.

**Reference:**

* http://gbayer.com/development/moving-files-from-one-git-repository-to-another-preserving-history/


## Migrate a repository from SVN to GitHub

The steps necessary to migrate a Subversion repository,
including all history and tags,
to GitHub:

     mkdir storm && cd storm
     svn2git -v https://csdms.colorado.edu/svn/storm
     git remote add origin https://github.com/csdms-contrib/storm.git
     git pull
     git branch --set-upstream-to=origin/master master
     git pull
     git push origin master
     git push —all
     git push —tags

The example is for [storm](https://github.com/csdms-contrib/storm),
which had an existing repo on GitHub.


## Move an issue across repos

This is a GitHub, not a git, thing, but nevertheless, use
[https://github-issue-mover.appspot.com/](https://github-issue-mover.appspot.com/).
