# Git Cheatsheet

## First config

```bash
ssh-keygen -t rsa -C "misostack.com@gmail.com"
git config --global user.name "misostack"
git config --global user.email "misostack.com@gmail.com"
```

## Delete Multiple Local Branches

```bash
git branch | grep ‘your_string_here’
```

**Delete it remotely too.**

```bash
git branch | grep ‘your_string_here’ | xargs git push origin — delete
```

**Example**

```bash
git branch -D `git branch | grep -vE 'master|develop|release/*'`
```

## Disabled Push

```bash
git remote set-url --push origin DISABLE # important, you can not push directly on origin repo
```

## Tags

```bash
# create new tag
git tag tagName
git push --tags
# delete local tag
git tag -d 12345
# delete remote tag '12345' (eg, GitHub version too)
git push origin :refs/tags/12345
# alternative approach
git push --delete origin tagName
```

git tag -d tagName

## How to fix some strange errors related to git

> cannot lock ref 'refs/remotes/origin

Root cause : ![image](https://user-images.githubusercontent.com/31009750/209046539-03796e22-fd1e-459c-a415-b512c3540296.png)

**Answered**

```bash
git fetch --prune
```
