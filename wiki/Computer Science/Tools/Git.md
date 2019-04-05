Find all commits that are no longer reachable.
This will find deleted branches, dropped stacks, etc.

```sh
git log --graph --oneline --decorate $( git fsck --no-reflog | awk '/dangling commit/ {print $3}' )
```
