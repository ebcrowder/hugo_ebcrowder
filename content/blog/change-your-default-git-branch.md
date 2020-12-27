---
date: "2020-06-21"
title: "Change your local default git branch"
tags: ["git"]
draft: false
---

# Background

GitHub [announced](https://news.yahoo.com/github-abandons-master-slave-terms-124111791.html) that it will be retiring racist terms from its platform. This includes the use of `master` to refer to default branches.

Want to change all of those current projects that you have to use a different default branch? Fortunately, `git` makes the process of changing default branches very easy.

# How

Let's say that you want to use `main` as your new default branch.

In your project directory, do the following:

```bash
git branch -m master main
```

```bash
git push -u origin main
```

The first command will create a branch called `main` and copy `master` to it. This means that your `git` log is preserved in its entirety. The second command pushes the `main` branch to your designated `remote`, whether it be GitHub, GitLab, Sourcehut, local `git` server, etc.

That is it!
