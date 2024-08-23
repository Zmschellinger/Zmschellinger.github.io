# Building a web portfolio with Github Pages

I first got this idea after wondering around Github and running accross [fmash.16's](https://fmash16.github.io/content/posts/ssg5_site.html) webpage. After seeing this I immediatly started working on my own personal portfolio/blog. This is the process for how I did it. 

# 1. Create a Github account
If you dont already, make sure you have a Github account
# 2. Create a repository
Create a repository name xxxx.github.io, where xxxx is your Github username. 
# 3. Create _config.yml
Create a new file named _config.yml. From here you can choose what theme you would like for your website and give it a title. Shout out to [b2a3e8/jekyll-theme-console](https://github.com/b2a3e8/jekyll-theme-console) for the awesome theme they created. 

This is what my _config.yml looks like.

>title: 'Zachs Portfolio'
>description: "This is Zachary Schellinger's web portfolio!"
>remote_theme: b2a3e8/jekyll-theme-console
>
>header_pages: 
>  - index.md
>  - contact.md
>  - whoami.md
>  - projects.md
>  - writeups.md
>  - archive.md
>
># Build settings
>markdown: kramdown
>style: dark
>listen_for_clients_preferred_style: true

#4 Create webpages and link them to your home page
As you may have noticed, my _config.yml file contains a header_pages section that included the name of each webpage file. These files are coded in markdown but it is possible to code them in HTML as well. Once you are here its just rinse and repeat! This is what my projects page looks like just to show how I coded in the links. This [list](https://www.markdownguide.org/basic-syntax/#lists-1) of markdown basics was immensely helpful when creating these pages. 

>---
>title: /projects
>layout: page
>permalink: /projects
>---
>
># Projects
>
>This is where I keep the projects I am currently working on.
>1. '[My web protfolio](/webportfolio.md)
>2. '[Malware game](/malwaregame.md)
