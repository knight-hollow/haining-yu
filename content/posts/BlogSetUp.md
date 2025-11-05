+++ 
draft = false
date = 2025-10-27T11:07:25+02:00
title = "Build and Deploy a Blog"
description = ""
slug = ""
authors = ["Haining"]
tags = ["Hugo", "Hugo-Coder", "GitHub Pages", "Static Site Deployment", "Tutorial"]
categories = []
externalLink = ""
series = []
+++

## Building a Hugo Blog with the Hugo-Coder Theme and Hosting it on GitHub Pages

This is a **complete and detailed step-by-step guide** on how to create, configure, and deploy a **Hugo blog using the Hugo-Coder theme**, and host it automatically on **GitHub Pages** with **GitHub Actions**.  
You’ll learn everything from initializing your project to publishing your first post online.


### Install Hugo Extended

Make sure to install the **Extended** version (it supports SCSS and advanced themes like Hugo-Coder).

#### macOS
```bash
brew install hugo
```

Check installation:

```bash
hugo version
```
It should show something like:

```
hugo v0.xx.x+extended ...
```

###  Create a New Hugo Site
```bash
hugo new site MyBlog
cd MyBlog
```

This generates a default Hugo directory structure: 
<div style="text-align: center;">  
<img src="/haining-yu/images/myblog.png" alt="Hugo directory structure" width="400">
</div> 

### Initialize Git and Add the Hugo-Coder Theme
Initialize Git in your project:
```bash
git init
```

Add Hugo-Coder as a git submodule (so you can update it easily later):
```bash
git submodule add https://github.com/luizdepra/hugo-coder themes/hugo-coder
```
You should now have:
```
themes/hugo-coder/
.gitmodules
```

###  Copy and Configure ExampleSite
The Hugo-Coder theme includes an example configuration.
Copy its example site contents to your main directory:
```bash
cp themes/hugo-coder/exampleSite/* -rf ./
```
This provides you with ready-made layouts, menu items, and config settings.
<div style="text-align: center;">  
<img src="/haining-yu/images/example.png" alt="ExampleSite" width="400">
</div> 

### Create Your First Post
Generate a new Markdown file for your first post:
```bash
hugo new posts/first-post.md
```
Open the file content/posts/first-post.md and set:
```markdown
---
title: "First Post"
date: 2025-11-05T20:00:00+01:00
draft: false
---
Virtual reality (VR) is a simulated experience that employs 3D near-eye displays...
```
(Make sure draft is set to false, otherwise it won’t appear in the published site.)

### Edit Configuration File (config.toml)
Open the root config.toml and edit key fields.

Example:
```toml
baseURL = "https://AIThoughtLab.github.io/myblog/"
languageCode = "en-us"
title = "myblog"
theme = "hugo-coder"

# Add your params (from exampleSite)
[params]
author = "Haining Yu"
description = "A blog about AI, immersive media, and ideas."
keywords = "AI, Machine Learning, Hugo, Static Site"
```
You can further customize menus, social links, and footer as needed.

### Preview Your Site Locally
Before deploying, preview the site:
```bash
hugo server
```
Open your browser at:
👉 http://localhost:1313

You should see the ThinkingAI Blog with the Hugo-Coder layout.

### Add GitHub Actions Workflow for Deployment
Now we’ll automate the deployment to GitHub Pages.

Create the following structure:
```
.github/workflows/gh-pages.yml
```
Paste this workflow content:
```yaml
name: github pages

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```
Commit the workflow:
```bash
git add .
git commit -m "Add GitHub Actions workflow for Pages"
```

### Push to GitHub and Enable Pages
1. Create a new repository on GitHub, e.g. ThinkingAI.
2. Connect your local project:
```bash
git branch -M main
git remote add origin https://github.com/AIThoughtLab/ThinkingAI.git
git push -u origin main
```
GitHub Actions will automatically start building your site under the Actions tab.

### Enable GitHub Pages
After a successful Action run:
1. Go to Settings → Pages
2. Under “Build and Deployment”:
3. Click Save
Your site will go live in a few minutes at:
https://AIThoughtLab.github.io/myblog/

### Verify the Deployment
Visit your URL in a browser.
You should see your site rendered with Hugo-Coder’s clean layout, including your first post.

You can also monitor deployment logs at:
Actions → github pages → View workflow runs

### Add New Posts and Maintain
Whenever you want to publish new content:
```bash
hugo new posts/my-second-post.md
```
Edit the post, set draft: false, then push:
```bash
git add .
git commit -m "Add new post"
git push
```
GitHub Actions will automatically rebuild and redeploy your updated blog.