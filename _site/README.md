# Posting to Seattle Feminist JS

This is a [Github Pages Site](https://pages.github.com/), built with Jekyll.

## Working Locally
Work on your post locally so you can preview your changes before you push them up.

1. Requires [Ruby](https://www.ruby-lang.org/en/documentation/installation/)
2. Download the Repo
    `git clone https://github.com/SeattleFeministJS/seattlefeministjs.github.io.git`

3. Install bower and npm dependencies
    `npm install`
    `bower install`

3. Install jekyll
  `gem install jekyll`

4. Preview in browser
  `jekyll serve`

  Open your browser to [localhost:4000](http://localhost:4000)

## Add A Post

1. Running `jekyll serve` will auto-generate your site when a file is updated

2. Create a new file in the `_posts` directory
    - Posts are created in [Markdown](https://guides.github.com/features/mastering-markdown/)
    - Follow the naming convention `YYYY-MM-DD-name-of-post.md`
    - Include this at the top of your post (edited with appropriate data) - see existing posts for details

      ---
      layout: post
      title:  "My Post Title"
      date:   2016-04-21 11:56:05 -0700
      categories: meetup
      author: pattyok

      ---

      Add yourself to the _config.yml file to get your full name on the post (follow example there)

3. [Preview your updates](http://localhost:4000)

4. Once satisfied with the changes push your changes up, if you don't have direct write access, make a pull request.
