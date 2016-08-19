# Subversion #
Use HEAD to refer to the latest revision number in the repository, and BASE to
refer to the working copy.  Use ^ to refer to the repository's root directory.
(This last introduced in SVN 1.6.)

## List Changed Files ##
```
svn diff -rX:Y --summarize
```
List files added/changed/deleted between the listed revisions.  Files are listed
one per line, with standard change markers preceding (e.g. 'M' for "modified").

## List Tags ##
```
svn ls -v ^/tags
```
Lists all tags in the repository, sorted alphabetically.  (TODO: verify sort)
The -v option includes the revision number and the username of the revision
author.

Remember that tags are merely copies of the trunk which are copied and never
modified again...but this is merely by convention, probably not enforced by the
server.

## Revision Info ##
```
svn log -v -rX ^/
```
Lists files with `-v`.  Revision number X specified with `-r`.  Path `^/`
indicates the root level of the repository, regardless of local command
invocation environment.
