title: Deploy Hexo on Github
tags:
  - hexo
  - git
categories: []
date: 2018-04-10 22:54:00
---
# Deploy Hexo on Github

## git

https://git-scm.com/download/

## Node.js

http://nodejs.org/download/


## Hexo

### install Hexo

```bash
npm install -g hexo
```

<!-- more -->

### init and start server

```bash
hexo init     # create a new Hexo folder
npm install   # install dependency
hexo g        # generate staic files
hexo s        # start server
```

### set git infomation

```bash
npm install hexo-deployer-git --save
vi _config.yml
-----------_config.yml-----------------
deploy:
  type: git
  repo: git@github.com:yourname.github.io.git
  branch: master
```

### create new post

```bash
hexo new [layout] <title>
```

### deploy your blog to git

```bash
hexo clean
hexo d        # deploy
```

# Manager source code on git

## create a new branch to save code
```bash
git branch hexo		# new branch 'hexo'
git checkout hexo 	# checkout to 'hexo'
```

## add remote origin and push
```bash 
git init
git add .
git commit -m "message"					# commit
git remote add origin git@github.com
git push origin hexo					# push local hexo to remote branch hexo
```