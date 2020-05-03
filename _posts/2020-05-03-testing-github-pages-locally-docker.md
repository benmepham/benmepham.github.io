---
title: "Testing a GitHub Pages site with the Minimal Mistakes theme locally using Docker"
categories:
  - blog
---

I wanted to test this site locally so I don't have to push to GitHub every time I want to modify or change part of my site. I also want to be able to try new things out without breaking the live site. I didn't want to have to mess around installing Jekyll and Ruby, so instead, I decided I would like to be able to run the site inside a Docker container. 

After researching this, I chose Bret Fisher's [jekyll-serve](https://hub.docker.com/r/bretfisher/jekyll-serve) image as it worked out of the box with the [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes) theme I am using. 

Just create a docker-compose.yml file in the directory of your GitHub Pages site:

```yaml
version: '2.4'

services:
  jekyll:
    image: bretfisher/jekyll-serve
    volumes:
      - .:/site
    ports:
      - '80:4000'
```

Then type: `docker-compose up` and you will have your GitHub Pages site running locally. Simple!

When you save or create a file in your site directory the Docker container will regenerate your site (apart from _config.yml), if you don't want this to happen (for example for README.md) you can edit your _config.yml file and specify files to be excluded:

```yaml
exclude:
  - docker-compose.yml
  - README.md
  - site.code-workspace
```

Also, I use Visual Studio Code, which I usually have set to Auto Save my code. However, when editing my site, I have this turned off, otherwise, the Docker container will regenerate the site after every small change. 

Thanks for reading, I hope this has been useful to you.