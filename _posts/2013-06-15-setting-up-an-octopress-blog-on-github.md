---
layout: post
title: "Setting Up an Octopress Blog on Github"
date: 2013-06-15 18:47:07 +0100
keywords: "blog, github, octopress"
---

A small tutorial how to install and set up [Octopress][octo] blog on [GitHub][gh] with a custom domain.

[octo]:http://octopress.org/

Firstly you need to head over to [GitHub][gh] and create a new repository named `username.github.io`, where `username` is your username (or organization name) on [GitHub][gh].

**NB** If the first part of the repository doesn’t exactly match your username, it won’t work, so make sure to get it right.

[gh]:https://github.com/


## Installing Octopress on your machine

You need to clone [Octopress][octo] repo on your local machine. 

```
git clone git://github.com/imathis/octopress.git octopress
cd octopress
```

Then you can create a separate gemset for your blog. This is optional.

```
rvm gemset create blog
rvm gemset use blog
```
 
Next you will need to install dependencies and default theme.

```
gem install bundler
bundle install

rake install
```


## Deploying to GitHub Pages

Octopress has a configuration task that helps you set all this up. Simply run the following command:

```
rake setup_github_pages
```

and enter the SSH or HTTPS URL from your repository when prompted, eg. `git@github.com:username/username.github.io.git`


**NB** The task above sets a `master` branch in the `_deploy` directory for deployment and switches the active branch from `master` to `source`.


```
rake generate
rake deploy
```


## Day to day usage

In order to create new post:

```
rake new_post["Hello Octopress"]
```

The markdown file will be located in `./source/_posts/`. 

To preview your blog locally, simply run the command below and visit [http://localhost:4000][local]

```
rake preview
```

[local]:http://localhost:4000

Once you are happy with your post you can publish it by running this shorthand:

```
rake gen_deploy
```

This will generate your blog in `_deploy` directory and deploy changes to `master` branch on [GitHub][GH].
Now you can check your `http://username.github.io`.

It is important to know that this will not update the `source` branch. To do that you need to push changes you have made to `source` branch manually:

```
git add .
git commit -m "Added post Hello Octopress"
git push origin source
```


## Setting up custom domain on GitHub

To use your custom domain with [GitHub Pages][gh-pages] site you must create a file named `CNAME` with a single line that specifies your custom domain in the source folder.

```
echo 'domain.com' > source/CNAME
```

Then you need to configure your DNS records with your DNS provider:

* Apex domains (eg. `domain.com`)
    * create A record poiniting to `192.30.252.153`
    * create A record poiniting to `192.30.252.154`
* Subdomains (eg. `www.domain.com` or `blog.domain.com`)
    * create CNAME record that points to `username.github.io`

Once you are done, generate and deploy your blog:

```
rake gen_deploy
``` 

and visit your `http://domain.com`.

Please know that this could take several minuts, depending on your domain provider. 


## References

* [Octopress Website][octo]
* [GitHub Pages][gh-pages]
* [Setting up a custom domain with GitHub Pages][gh-custom]

[gh-pages]:https://pages.github.com/
[gh-custom]:https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages



