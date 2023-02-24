---
title: "Using local Google Fonts in Hugo"
tags:
- "Computer Science"
- "Hugo"
- "Quartz"
---

As I was customizing this blog, I wanted to change the fonts to fit the style of my [Website](https://vogel.business).  I used three different Fonts there:
- Red Hat Mono
- Red Hat Display
- DM Serif Display

To comply to local law I needed to host the Google Fonts by myself, so I needed to do this again for this blog. Google explains how you can do this in this [blog post](https://fonts.google.com/knowledge/using_type/self_hosting_web_fonts). But you basically just download the font that is referenced in the stylesheet and change that reference to a local path.

## Problem
So I downloaded the fonts and put them in the asset folder, like I would normally do in frameworks like [vue.js](https://vuejs.org/). But after I pushed my changes to github pages the fonts where not available in the specified path.

## Solution
Because hugo uses a webpacker the paths from the development environment do not exist anymore but I noticed that files from the `static/` folder seem to be in the root directory of compiled version of the project. 

So I moved my fonts into `static/fonts` and referenced the fonts with `fonts/<FONT_NAME>`

## Example
To reference the *DM Serif Display* font for example you need to add the following to the `assets/styles/custom.scss`:
```scss
/* latin */
@font-face {
	font-family: 'DM Serif Display';
	font-style: normal;
	font-weight: 400;
	font-display: swap;
	src: url(/fonts/-nFnOHM81r4j6k0gjAW3mujVU2B2G_Bx0g.woff2) format('woff2');
	unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215, U+FEFF, U+FFFD;
}

:root {
	--font-body: "Roboto Serif";
	--font-header: "DM Serif Display";
	--font-mono: "Red Hat Mono"
}
```

>[!warning] As you can see the relative path does not correspond to the folder structure of your development environment! The path changes after it is compiled by the webpacker
![[static_fonts_folderstructure.png]]