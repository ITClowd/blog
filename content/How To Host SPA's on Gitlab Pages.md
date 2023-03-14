---
title: "How To Host SPAs on Gitlab Pages"
tags:
- "Computer Science"
- "Website"
- "Vuejs"
- "Gitlab Pages"
- "CI/CD"
---

## My old Setup
During the development of my [website](https://vogel.business)  I ran into some issues with my CI/CD-System. I used my own gitlab-runner on my server, where I also host some private services. But it was not reliable at all, and I did not have the time anymore to maintain that system. I originally decided to do it the "complicated" way, because I wanted to learn about more complex deployment scenarios with docker. But since I am in my final semester at DHBW and my free time is very limited, I no longer want to pay for my technical debts. 

So I quickly set up GitLab pages for my website to speed up my development process.

## New Setup using GitLab Pages
So I googled a little bit and composed the following ``gitlab-ci.yml`
```yaml
image: node:16
pages:
  cache:
    paths:
      - node_modules/
  script:
    - yarn install
    - yarn build
    - rm -rf public
    - mkdir public
    - cp -r dist/* public
  artifacts:
    paths:
      - public
  
  only:
    - main
```

It is pretty straight forward:
- First it defines, that node_modules should be cached. This results in faster build times
- Then I define the normal build steps for vue.js which I use for my website and then copy the results (from ``dist/`` to the ``public/`` Folder).
- I define the `public/` Folder as artifact, so GitLab pages finds it
- And I set it to only trigger when I push to the main branch

This worked almost flawlessly. I just needed to add my custom domain to the project and configure it in my DNS settings. You can read about those steps in the [official GitLab Documentation](https://docs.gitlab.com/ee/user/project/pages/custom_domains_ssl_tls_certification/)

## Enabling SPA's
Did you notice the "almost"? I stumbled into one last problem. Every time I wanted to access a subpage, I got an error from GitLab, that no GitLab Page with that name exists.

This is due to the nature of SPA's. They normally have a custom routing system that does not actually load a new file. So whenever a subsite is requested, the index file handles the routing via that system.

So I needed a way to redirect all traffic to the index file, which I could achieve with the following entry in the `public/_redirects` file:
```
/* /index.html 200
```

I found that solution in the [official docs](https://docs.gitlab.com/ee/user/project/pages/redirects.html#rewrite-all-requests-to-a-root-indexhtml) as well.

## What did I gain
- I now have a very reliable system which uses the shared runners from GitLab. 
- I do not have to use my own server for hosting my static site, which frees up some resources for more heavy loads on my server.