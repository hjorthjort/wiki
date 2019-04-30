Find all commits that are no longer reachable.
This will find deleted branches, dropped stacks, etc.

```sh
git log --graph --oneline --decorate $( git fsck --no-reflog | awk '/dangling commit/ {print $3}' )
```

How does it work?
`git fsck` checks connection and validity of the objects.
`--no-reflog` means include even commits that aren't referenced by the reflog.
The reflog is a log of references, i.e., where tags, stashes and branch heads pointed previously

```sh
git fsck --no-reflog
```
