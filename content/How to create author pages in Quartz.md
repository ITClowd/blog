---
title: "How to create author pages in Quartz"
tags:
  - "Quartz"
authors:
  - "Moritz Vogel"
---
## Why author pages
As I am still building this blog, I wanted to add author pages, so a reader always can look who is writing the blog articles if I ever wanted to allow others to write articles on my blog. 

## My search process
As Quartz is based on hugo, that's where I started looking on how to do it on the [offical hugo documentation](https://gohugo.io/content-management/taxonomies/). But it just showed errors when I tried to do it exactly how it is explained there. So I experimented with a few solutions from blogs I found. (Mainly this one on [Netlify](https://www.netlify.com/blog/2018/07/24/hugo-tips-how-to-create-author-pages/))

## The Solution
So how does it work?

### 1. Folder Structure
As first step, you need to create the author pages as .md files. Therefore, you need to create the following folder structure:
![[media/author_pages_folderstructure.png]]
>[!info] 
>Note, that the actual .md page is call `_index.md` with an underscore and the folder name is "slugified" (Instead of Moritz Vogel its moritz-vogel)
### 2. Create Taxonomy
In the `config.toml` you need to define the “author” taxonomy:
```toml
[taxonomies]
author = "authors"
tag = "tags"
category = "categories"
```

You need to define the default taxonomies “tag” and “category” if you plan on using them.

### 3. Create And Adjust Page Templates
In order to show the pages properly, I needed to add the following files:
under `layouts/authors/list.html` I added:
```html
<!DOCTYPE html>
<html lang="{{ .Lang }}">
{{ partial "head.html" . }}

<body>
{{partial "search.html" .}}
<div class="singlePage">
    {{partial "header.html" .}}  

    <img src="{{ .Params.photo }}" alt=""/>
    <h1>{{ .Params.name }}</h1>
    
    {{ .Content }}
   
    
    <h3>Articles</h3>
    <ul>
    {{ range .Data.Pages }}
        <li><a href="{{ .Permalink }}">{{ .Title }}</a></li>
    {{ end }}
    </ul>
</div>
</body>
</html>
```
Which is the page for one single author. It is available under [blog.vogel.business/authors/moritz-vogel](https://blog.vogel.business/authors/moritz-vogel)
I also added an overview page of all authors:
```html
<!DOCTYPE html>
<html lang="{{ .Lang }}">
{{ partial "head.html" . }}

<body>
{{partial "search.html" .}}
<div class="singlePage">
    <!-- Begin actual content -->
    {{partial "header.html" .}}
    <h1>Authors</h1>
    <ul>
    {{ range .Data.Pages }}
      <li><a href="{{ .Permalink }}">{{ .Params.name }}</a></li>
    {{ end }}
    </ul>
</div>
</body>
</html>
```
which is available under [blog.vogel.business/authors/](https://blog.vogel.business/authors/)

You also need to show all the authors in the article, so I added this to `layouts/_default/single.html`
```html
{{- range .Params.authors }}
	{{- with $.Site.GetPage "taxonomyTerm" (printf "authors/%s" (urlize .)) }}
		<a href="{{ .Permalink }}">{{ .Params.name }}</a>
	{{ end }}
{{ end }}
```

### 4. Add Authors to Pages
Now all you need to do is adding the author to the pages in the YAML-Frontmatter:
```YAML
---
title: "How to create author pages in Quartz"
authors:
  - "Moritz Vogel"
---
```
