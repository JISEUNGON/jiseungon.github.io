---
layout: post
title: "How to Install Jekyll"
date: 2025-07-06
---
# How to install jekyll in mac
```bash
brew install chruby ruby-install
ruby-install ruby 3.4.1
echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zshrc
echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zshrc
echo "chruby ruby-3.4.1" >> ~/.zshrc # run 'chruby' to see actual version
gem install jekyll
```

# Installing bundler
### What is Bundler
- Bundler is tool managements dependencies.

## why use Bundler??
- Ruby-based projects such jekyll and github pages require multiple gems (ruby packages), and Bundler allows you to install and manage all of those dependecies automatically
- Benefit is creating a reproducible environment by unifying the gem version for each project

# How to install bundler
```bash
gem install bundler
```

# How to set jekyll project
## First step
```bash
mkdir docs
cd docs
git checkout --orphan gh-pages # this branch is called gh-pages does not create history or contents
git rm -rf . # removes the contents from your default branch from the working directory

jekyll new --skip-bundle . # creates a jekyll site in the current directory
```

## Second step 
### Edit Gemfile
```bash
Add "#" to the beggining of the line that starts with "gem "jekyll"" to comment out this line
Remove "#" to the beggining of the line that starts with `gem "github-pages"` to comment out this line
```

## Thrid step
```bash
bundle install
Add `Gemfile.lock` into .gitignore file
```

## Fourth step
- Optinally, make any necessary edits to the `_config.yml` file. This is required for relative paths when the repository is hosted in a subdirectory.
```bash
# _config.yml
baseurl # the subpath of you site
url # the base hostname & protocol for your site
domain # if you want to force HTTPS, specify the domain without the http at the start
```

## Notice
- To make it appear in the text, Put it in the _posts folder
- The front-meter is essential at the top
```bash
---
layout: post
title: "How to Install Jekyll"
date: 2025-07-06
---
```

# How to start jekyll in local
```bash
bundle exec jekyll serve
```
