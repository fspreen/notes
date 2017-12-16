# Git Submodules #

Submodules are a way to include another repository in a subdirectory of a parent
repository.  They are somewhat tricky to work with, so I'm writing this handy
reference to help me use them for Vim plugins in my dotfiles repository.

There are three important concepts to remember:

* Submodules must be "set up" by the parent repository before use in a
  workspace.
* Submodule Git settings are stored in the parent repository's `.git/` folder,
  rather than in the submodule's folder.
* A submodule's `HEAD` matches the parent repo's expectation, and is often
  detached.  Changing the `HEAD` changes the parent repository.

## Quick Reference ##

### Workspace Setup ###
Cloning a repository with submodules can be done in different ways, depending on
whether configuration changes need to be made for each step.  In short:
```
git clone --recursive $URL
```

A recursive clone sets up everything from a standing start.  It assumes that the
upstream URLs in `.gitmodules` are reachable and that default configuration
choices are acceptable.
```
git clone $URL
cd $DIR
git submodule init
git submodule update
```

The `clone` step retrieves the parent repository as expected.  The `init` step
sets up the submodule configuration, and the `update` step populates the
submodule directories.  The `init` and `update` steps can be combined with the
command `git submodule update --init` if needed.  The multi-step approach is
best if configurations need to be changed, e.g. changing `.gitmodules` to use a
different URL.

### Checking Status ###
The command `git submodule status` will display the commit and a `git describe`
tag for each submodule.  The commit ID will be prefixed with `-` if the
submodule has not been initialized and `+` if there are uncommitted changes.
The prefix `U` indicates a merge conflict.

### Adding a Submodule ###
Move to the directory and execute:
```
git submodule add $URL
```

Note that this will create a new folder similar to a basic clone operation.
Alternatively, add the submodule to a different folder:
```
git submodule add $URL path/to/rename
```

The path will also function as the submodule's name unless the `--name` option
is used.  Names specified should be usable directory names, due to the internals
of the `.git/` folder.

Remember to commit the `.gitmodules` file and the new submodule after adding.

### Updating a Submodule ###
Moving the `HEAD` pointer in a submodule in any way will be treated as a change
by the parent repository.  This will be reflected in `git status` messages as
well as items that can be staged, committed, and pushed.

The `HEAD` pointer can be reset or brought up-to-date with the parent
repository's expectations with the command `git submodule update` which by
default performs the `--checkout` action.

The `HEAD` can also be moved to the commit matching the submodule's upstream
`origin/master` with the command:
```
git submodule update --remote
```

The branch name can be changed with a configuration option; consult the manual
for details.

* [ ] What submodule command moves the local `master` branch to `origin/master`?

### Changing a URL ###
If a submodule's repository changes its upstream URL, these changes should be
recorded in the parent repository and workspace.  First, modify the
`.gitmodules` file with the new information.  Then synchronize the change with
the Git configuration:
```
git submodule sync
```

This will also update the appropriate remotes in the local copy of the
submodule's repository.  Remember to commit the change to `.gitmodules` when
complete.

## Internals and Theory ##
The parent repository needs a minimal amount of information about each
submodule.  The necessary information is:

* upstream URL
* commit ID desired by the parent
* directory path to hold the submodule in the parent
* name of submodule (default: same as path)

In a minimal context (e.g. on a server like GitHub), the upstream URL, path, and
name are stored in the `.gitmodules` file in the parent repository.  This file
is subject to revision control the same as `.gitignore` or any other file.  The
commit ID is stored elsewhere in the Git internals.

* [ ] Where and how is the commit ID stored?

A submodule must be initialized, which copies settings from `.gitmodules` into
the appropriate Git files for use.  Some of these settings are copied to
`.git/config` in the parent repository.  When the submodule is fully set up, the
submodule's Git settings (branches, remotes, etc.) are stored under
`.git/modules/` in the parent repository.  Rather than having `.git/` folders of
their own, the submodules have files containing pointers back to the parent
repository.

If the `.gitmodules` file is changed, the alterations should be resynchronized
to the Git internal structures.  This is done using `git submodule sync`.

Altering the `HEAD` in a submodule by any means will appear in the parent
repository as a potential change to be committed.  Because of this, submodules
can be expected to have a detached `HEAD` as part of normal operations.

When invoking `git submodule update` the submodule's `HEAD` will be set to the
commit recorded in the parent repository.  Git data structures for the submodule
will be retrieved from upstream as necessary, using the URLs set in
`.git/config` from the `.gitmodules` file.
