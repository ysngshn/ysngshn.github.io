---
title: "Journey to a homepage from scratch"
excerpt_separator: "<!--more-->"
categories:
  - Programming
tags:
  - coding
  - web-dev
  - Jekyll
toc: true
toc_sticky: true
---

Hffff, finally this website is up. What a journey!

To commemorate this moment, I decided to write this post and share some personal experiences on setting up this homepage. Hopefully, my perspective as a PhD student/researcher who only has basic knowledge about HTML/CSS/Javascript can help those in a similar situation.

<!--more-->

(If you have no idea about the whole thing, [this introductory article][ghp-intro] can be an excellent read.)

## Why this personal webpage

For me, this webpage serves two purposes:

- Provide some information about me and my contact for potential collaborators/employers.
- Provide a place to write some posts to introduce my research/projects and share some thoughts.

## The making of a homepage

Making this homepage has been an interesting project for me with many new things to discover.

### Programming prerequisites

Before considering setting up a personal webpage, I think it is helpful to be somewhat familiar with the following programming/formatting languages:

- **HTML** and **Markdown**: these are needed for creating content. 
  - Markdown provides convenient text formatting and is an easy learn. read through some simple guide [like this one][md-tutorial] and you should be fine.
  - HTML is a full-fledged language for describing the structure of websites and it provides a basis for CSS and Javascript to act on. There are many free tutorials online and a weekend should be more than enough to obtain a fair grasp.
- **CSS** and **Javascript**: these are for customizing the website. 
  - CSS is a language to specify the style (e.g. font, color, size, background, ...) of different website elements.
  - Javascript is a scripting language for the website to achieve advanced and dynamic features.

  In general, it is probably good enough to have some basic notions first, then learn on the fly when you work on the website. A complete mastery of them is probably overkill for setting up a simple webpage with [Jekyll][jekyll].

### Getting started with Jekyll

[Github pages][ghp] allows for free hosting of personal websites which is awesome. And they recommend [Jekyll][jekyll], a static website generator which fits the purpose nicely.

I find the official [step-by-step tutorial][jekyll-tutorial] excellent for understanding how the Jekyll framework works.

### The Minimal Mistakes theme

To quickly set up a personal website with good styles and features, it is a good idea to start from an established template. There are numerous [Jekyll themes][jekyll-themes] available. I decided to go with the [Minimal Mistakes][mm] (MM) theme because:

- It offers a clean interface and many useful features
- It has [awesome documentation][mm-doc]
- It is the most starred Jekyll theme repo on Github at this time, so I expect fewer bugs and better community support

### Coding things up

Now we are finally ready to implement the personal webpage! Here are the things I did to set up the website:

- Set things up on Github:
  - Create an empty `<username>.github.io` repo
  - Create a `dev` branch for updates without affecting the running website hosted from the `main` branch
  - Fork the [Minimal Mistakes][mm-src] Jekyll theme to make sure it won't go away or get modified unexpectedly
- Prepare my local machine for Jekyll development:
  - Install Jekyll following [the official guide][jekyll-install]
  - Prepare an editor that can work on Jekyll conveniently (checkout e.g. the [awesome-jekyll-editors][jekyll-editors] repo)
- Build a starting website with the MM theme
  - Clone the `<username>.github.io` repo, switch to `dev` branch
  - Copy the content of [mm-github-pages-starter][mm-starter]
  - Set up with the [remote theme method][mm-remote] and use the previously forked repo for remote theme
  - Adjust and fill up the relevant entries in the template `_config.yml`
  - Run `bundle install` to get the necessary gem files.
  - Run `bundle add webrick` to fix [an issue on Ruby 3][ghi-ruby3]
  - Run `PAGES_REPO_NWO=<username>/<username>.github.io bundle exec jekyll serve` to view the generated website locally 

Now we should be able to see a website locally with the MM theme. In the following, I will introduce some customizations I have made to my personal website.

### Some tweaks I have made

There are several things I would like to change from the default template. Luckily they are not too hard with some web search and trial-and-error.

#### - Reorganize the pages

Instead of the default layout, I have decided to simplify the webpage interface into three parts:

- A welcome page with a welcome message and a brief introduction of myself.
- A "CV" tab that summarizes my professional experiences.
- A "Blog" tab for the blog posts.

I have also simplified the footer since social network links are already provided in the sidebar.

The goal is to avoid clutter so visitors can quickly find relevant information. All these are achieved by customizing specifications in the MM theme and coding some HTML within the Jekyll framework.

#### - Support dark theme

The default MM theme does not support the dark theme, although it offers several skins including a dark skin. I would like my personal website to use the dark skin when visitors wish for it, and stick with the default skin otherwise.

Since major modern browsers and operating systems support setting the light/dark theme preference. I decided to simply follow this preference when specified. Adding a toggle and cookie to store this same user preference seems overkill.

Fortunately, I can easily achieve my preferred approach [with this solution][ghi-dark].

#### - Upgrade Font Awesome

[Font Awesome][fa] provides icons for websites. The default MM theme uses the legacy V5 collection which is not up-to-date. Especially, it does not contain the new X (twitter) logo. Therefore I decided to bump the version from V5 to V6.

This is trivially achieved by replacing the two occurrences of `fontawesome-free@5` to `fontawesome-free@6` in the custom `_includes/head.html` file. I wasn't sure if this would work, luckily it does! :partying_face:

#### - Add favicon and logo

Jekyll was nagging about missing `/favicon.ico` when rendering the pages. This serves as a logo for the website. Sure, why not add one? Let's do it! 

I made mine with the help of [favicon.io][favicon-io]. I put the generated logos in `assets/favicon/` and followed [this instruction][ghi-favicon] to add them to the website.

### Deployment on Github

After some coding and content filling (check out [this cheat sheet][emoji-cheat] for emoji shortcodes :grin:), my website is finally ready for deployment. Simply pushing everything to the repo `<username>.github.io` and voilà! The website is up and running.

Until next time :wink:

[//]: # (Link references:)
[emoji-cheat]: https://github.com/ikatyang/emoji-cheat-sheet/
[fa]: https://fontawesome.com/
[favicon-io]: https://favicon.io/
[ghi-dark]: https://github.com/mmistakes/minimal-mistakes/discussions/2033#discussioncomment-460914
[ghi-favicon]: https://github.com/mmistakes/minimal-mistakes/issues/949#issuecomment-293873689
[ghi-ruby3]: https://github.com/jekyll/jekyll/issues/8523
[ghp]: https://pages.github.com/
[ghp-intro]: https://jmcglone.com/guides/github-pages/
[jekyll]: https://jekyllrb.com/
[jekyll-editors]: https://github.com/planetjekyll/awesome-jekyll-editors
[jekyll-install]: https://jekyllrb.com/docs/installation/
[jekyll-themes]: https://github.com/topics/jekyll-theme
[jekyll-tutorial]: https://jekyllrb.com/docs/step-by-step/01-setup/
[md-tutorial]: https://www.markdownguide.org/basic-syntax/
[mm]: https://mmistakes.github.io/minimal-mistakes/
[mm-doc]: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/
[mm-remote]: https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#remote-theme-method
[mm-src]: https://github.com/mmistakes/minimal-mistakes
[mm-starter]: https://github.com/mmistakes/mm-github-pages-starter
