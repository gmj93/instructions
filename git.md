# Submodules

## Add submodule

```
git submodule add <link to repository>
<commit, push...>
```

## Update submodule

```
git submodule update --remote --merge
git add <submodule name>
<commit, push...>
```

Reference: https://git-scm.com/book/en/v2/Git-Tools-Submodules

# Create patch of specific file between 2 commits

```
git diff <commit_start> <commit_end> <filename> > output.patch
```
