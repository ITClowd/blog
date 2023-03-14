---
title: "Publishing Obsidian via Github Actions"
date: 2023-02-21
authors: 
  - Moritz Vogel
tags: 
  - "Computer Science"
  - "Obsidian"
  - "Github Actions"
  - "CI/CD"
  - "Quartz"
---

## Background Story
A few months ago, I started my own ["Second Brain"](https://knowledge.vogel.business) (for which I use [Obsidian](https://obsidian.md)). I really liked that I can structure my learnings and summaries from my lectures in a way I feel most comfortable with.  Just a few weeks after I started using Obsidian, I thought: “wouldn't it be great if I could access my Vault remotely from any device?” So I looked at my options:

### 1. Using Obsidian Sync
I saw, that Obsidian is offering a subscription Service for syncing Obsidian vaults to all of your devices. But while that would be a fairly easy way to achieve my desired result, I would need Obsidian installed on every device I want to look at my knowledge base.

### 2. Using Obsidian publish
On the same page, I saw that Obsidian is offering a publishing service as well. But for me as a software engineer it seemed like wasted money because I was sure it could be achieved with resources that are either free or already available to me.

### 3. Using Quartz
Since I decided I didn't want to spend any money on this, I started using git as version control for my knowledge base. This made Obsidian Sync obsolete because I could sync my files between all my machines without any problems. I did however have some difficulties with git on my phone, so I thought that I could use CI/CD to publish my Vault to a website, so I could view it from anywhere.


### Implementation Rules
Luckily I found a repo which achieved mostly what I want: [Quartz](https://github.com/jackyzha0/quartz) (it is based on Hugo, which I wanted to check out anyway)

Before I started using Quartz, I made a few rules on how I want my vault deployed:
1. **Fully automated**
   I do not want to have to compile any markdown files to HTML files. I just want to commit my changes from Obsidian and want the changes to be automatically deployed to the webpage
2. **Compatible with my current vault**
   I do not want to rebuild my current vault to fit a specific format. 

## Setup
So how is my current setup?
I followed the setup instructions from Quartz, which are already pretty good, but I had a few Problems:
- I wanted to use git submodules to import the topics from my vault which I think are worth sharing with the public. The deployment process did not work with submodules from scratch, but a small adjustment in the `.github/workflows/deploy.yaml` did the trick:
```yaml
  ...
  jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0
          submodules: recursive
  ...
```

- However, I still have the problem, that some features like “Last changed by:" don't work if the files are in a submodule (feel free to contact me if you have a solution for that!)
- My current vault does not have `title` attributes in the YAML-frontmatter, so I need a way to automatically add this to the files.
- I do not tag my files in the YAML-frontmatter so hugo does not recognize them as such.

I did not solve these problems yet, but I found some interesting projects which partly address these Problems

I changed some of these problems by hand to test out the rest of the setup. So I pushed my manual changes, and voilà: I had a GitHub page ready to go.
I added a custom domain and quickly personalized hugo and now my knowledge base is available to the public under https://knowledge.vogel.business.

This blog is made in the same way, just a different vault! (Read the story: [[How this blog was born]])

## Future plans
- I want to take a look at a [modified version of obsidian-export](https://github.com/brandonkboswell/obsidian-export/tree/title_frontmatter) which can add the filename as title attribute to the YAML-frontmatter. However, the author descriped a process which needs to run on the local machine before pushing, so I want to automate that process with GitHub Actions
- I need to restructure my vault a little bit: in the future I include title and tags in the frontmatter and I want to remove the first headline since most renderers use the filename as title.