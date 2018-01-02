## Guidelines for Contributing

Airsonic development is a community project, and contributions are welcomed. Here are a few guidelines you should follow before submitting:

  1. **Styling** The documentation follows some styling guidelines to fit with the website.
    - Follow the markdown guidelines. For more details see [this guide](https://guides.github.com/features/mastering-markdown/).
    - Do not use `<h1>` or `<h2>` headers ( `# h1` or `## h2` headers).
    - Never manually start a new line in the middle of a sentence.
    - More spacing, more empty lines !! The documentation needs to be readable !
    - Read existing guides to find some inspiration.

  2. **Testing** The documentation is browsed with the website, so we recommand to test any new guides within the website using [jekyll](https://jekyllrb.com/) locally.
    - Clone the website repo `git clone https://github.com/airsonic/airsonic.github.io airsonic`.
    - Change directory into the cloned repo `cd airsonic`.
    - Clone the documentation submodule`git clone https://github.com/airsonic/documentation pages/docs`.
    - Run `jekyll serve --watch`.
    - Add your changes.
    - Check if everything is fine at `localhost:4040`.

  3.  **License Acceptance** All contributions must be licensed under [GNU GPLv3](https://github.com/airsonic/documentation/blob/master/LICENSE.txt) to be accepted. Use [`git commit --signoff`](https://gitirc.eu/git-commit.html) to acknowledge this.

  4.  **Be bold!** Without contributions, this project will vanish.

  5.  **Stay relevant** Issues or commentary that is off-topic or tangential to Airsonic development is subject to moderation. Questions should be focused on improving documentation to solve a problem. Visit [Reddit](https://www.reddit.com/r/airsonic) or [IRC](http://webchat.freenode.net?channels=%23airsonic) for community discussion.
