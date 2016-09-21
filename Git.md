# Git #
## Summary Log ##
Create a short log/summary using the first line from each commit message:
```
git shortlog
```
Since this covers the entire history back to the initial commit, it may be
necessary to specify a range:
```
git shortlog old_branch..HEAD
```
This specifies all commits that are ancestors of `HEAD` but not of `old_branch`.
This basically shakes out to "changes since old_branch" for a largely linear
history.  The `HEAD` can be shortened to `@` or omitted entirely, since it is
the default value for either side of the `..` operator.

## File Changes ##
Similar to above, files changed can be listed with:
```
git diff --name-only old_branch..HEAD
```
File names together with status (e.g. D for deleted) can be listed with:
```
git diff --name-status old_branch..HEAD
```
