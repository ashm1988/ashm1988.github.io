---
layout: post
title: Jekyll Documentation
date: 2022-10-21 23:00:00 +1100
categories: [HomeLab,Jekyll]
tags: [documentation,jekyll]
---

[TechnoTim Video](https://www.youtube.com/watch?v=F8iOU1ci19Q)

## Installation 
- Install the prerequisites [Jekyll prereq site](https://jekyllrb.com/docs/installation/)
- Follow the installation guide for the [chirpy theme](https://github.com/cotes2020/jekyll-theme-chirpy).

## Starting the local server 
```bash
bundle exec jekyll s
```

## Creating a new post

> Now using jekyll compose to create files quicker, see [jekyll-compose section](#creating-new-post-with-jekyll-compose)

- Create a new file in the `_posts` directory with the `YYYY-MM-DD-TITLE.EXTENSION` format. 
- add the below title detail
```markdown
---
layout: post
title: Jekyll Documentation
date: 2022-10-21 23:00:00 +1100
categories: [HomeLab,Jekyll]
tags: [documentation,jekyll]
---
```

## Install Jekyll-Compose
[https://github.com/jekyll/jekyll-compose](https://github.com/jekyll/jekyll-compose)  
- Add the below to the Gemfile
```
gem 'jekyll-compose', group: [:jekyll_plugins]
```
- Run `bundle` in the route directory of the site

## Creating new post with Jekyll-Compose

- To create new post file, run 
```powershell
bundle exec jekyll post "My New Post"
```

# Troubleshooting
Error: The process '/opt/hostedtoolcache/Ruby/3.1.2/x64/bin/bundle' failed with exit code 16  
Resolution: Update bundle lock file [found here](https://stackoverflow.com/ questions/72331753/ruby-and-rails-github-action-exit-code-16)
```powershell
bundle lock --add-platform x86_64-linux
```
Error: The process '/opt/hostedtoolcache/Ruby/3.2.0/x64/bin/bundle' failed with exit code 5  
Resolution: Limit the ruby version to 3.1 (https://github.com/cotes2020/jekyll-theme-chirpy/issues/811)
```powershell
ashm1988.github.io\.github\workflows\pages-deploy.yml
      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.1 # reads from a '.ruby-version' or '.tools-version' file if 'ruby-version' is omitted
```