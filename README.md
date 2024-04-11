# Notes
- Get the starting template from [mm-github-pages-starter](https://github.com/mmistakes/mm-github-pages-starter)
- run `bundle install` to get the necessary gem files.
- run `bundle add webrick` to fix [an issue on Ruby 3](https://github.com/jekyll/jekyll/issues/8523)
- Local development: run `PAGES_REPO_NWO=[user_name]/[repo_name] bundle exec jekyll serve (--livereload)`
- Add dark mode preference support (from browser/OS) [with this solution.](https://github.com/mmistakes/minimal-mistakes/discussions/2033#discussioncomment-460914)
- Personalize the left panel: required some basic html elements like `<br>`/`<del />` for the ~~joke~~ extended bio and a look at some [Font awesome icons](https://fontawesome.com/v4/icons/).
- Add favicon using e.g. [favicon.io](https://favicon.io/) and following [this instruction](https://github.com/mmistakes/minimal-mistakes/issues/949#issuecomment-293873689). 
- merge categories/tags pages to Posts: adjust nav, use home layout for main page, add navigation links between the views.

## TODO

- customize index page
- write a post about this

## useful links

- [Font awesome icons](https://fontawesome.com/v6/icons/)
- [Emoji cheat sheet](https://github.com/ikatyang/emoji-cheat-sheet/blob/master/README.md)