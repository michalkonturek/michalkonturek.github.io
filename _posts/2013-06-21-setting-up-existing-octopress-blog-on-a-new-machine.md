---
layout: post
title: "Setting Up Existing Octopress Blog on a New Machine"
date: 2013-06-21 16:19:40 +0100
description: "A small tutorial how to set up existing Octopress blog on a new machine."
keywords: "blog, github, git, octopress"
---

As any good blogging framework allows users to compose and publish posts from any computer, so does the [Octopress][octo]. However, the process of setting up [Octopress][octo] on a second machine is a bit more complex.

<!--more-->

[octo]:http://octopress.org/

Firstly, you need to clone `source` branch from your blog repo:

```
git clone -b source https://github.com/USERNAME/USERNAME.github.io DOMAIN.com
```

Then you need to create `_deploy` folder:

```
cd DOMAIN.com
mkdir _deploy
```

and initialize git with a remote pointing to `master` branch of your blog repo:

```
cd _deploy
git init
git remote add -t master -f origin https://github.com/USERNAME/USERNAME.github.io
```

**NB** Make sure you pull all changes from `master` branch,

```
git pull origin master
```

otherwise you will end up with this error:

```
! [rejected]        master -> master (non-fast-forward)
```

Finally, you can install all dependencies:

```
bundle install
```

and start blogging.


## Things to remember

If you use multiple computers for blogging, please make sure to pull 
all changes of your [Octopress][octo] blog source,

```
cd domain.com
git pull origin source

```

and your generated blog:

```
cd _deploy
git pull origin master
```


## References

* [Octopress Issue 755][gh-issue-1]
* [Octopress Issue 334][gh-issue-2]

[gh-issue-1]:https://github.com/imathis/octopress/issues/755
[gh-issue-2]:https://github.com/imathis/octopress/issues/334

