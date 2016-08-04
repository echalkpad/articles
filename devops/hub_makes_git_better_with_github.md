# hub makes git better with GitHub

[Original URL](https://hub.github.com/)

> hub is a command-line wrapper for git that makes you better at GitHub. $ brew install hub $ git clone <https://github.com/github/hub.git> && cd hub $ script/build -o ~/bin/hub $ alias git=hub $...

**hub** is a command-line wrapper for git that makes you _better at GitHub_.

```
$ brew install hub



$ git clone https://github.com/github/hub.git && cd hub

$ script/build -o ~/bin/hub


$ alias git=hub

$ git version
git version 2.1.4
hub version 2.2.0 
```

## As a contributor to open-source

Whether you are beginner or an experienced contributor to open-source, hub makes it easier to _fetch repositories_, _navigate project pages_, _fork repos_ and even _submit pull requests_, all from the command-line.

```
$ git clone dotfiles
→ git clone git://github.com/YOUR_USER/dotfiles.git


$ git clone github/hub
→ git clone git://github.com/github/hub.git


$ git browse -- issues
→ open https://github.com/github/hub/issues


$ git browse mojombo/jekyll wiki
→ open https://github.com/mojombo/jekyll/wiki


$ git clone github/hub
$ cd hub

$ git checkout -b feature
 ( making changes ... )
$ git commit -m "done with feature"


$ git fork
→ (forking repo on GitHub...)
→ git remote add YOUR_USER git://github.com/YOUR_USER/hub.git


$ git push YOUR_USER feature

$ git pull-request
→ (opens a text editor for your pull request message)
```

## As an open-source maintainer

Maintaining a project is easier when you can _easily fetch from other forks_, _review pull requests_ and _cherry-pick URLs_. You can even _create a new repo_ for your next thing.

```
$ git fetch mislav,cehoffman
→ git remote add mislav git://github.com/mislav/hub.git
→ git remote add cehoffman git://github.com/cehoffman/hub.git
→ git fetch --multiple mislav cehoffman


$ git checkout https://github.com/github/hub/pull/134
→ (creates a new branch with the contents of the pull request)


$ git am -3 https://github.com/github/hub/pull/134


$ git cherry-pick https://github.com/xoebus/hub/commit/177eeb8
→ git remote add xoebus git://github.com/xoebus/hub.git
→ git fetch xoebus
→ git cherry-pick 177eeb8


$ git am https://github.com/xoebus/hub/commit/177eeb8


$ git compare v0.9..v1.0


$ git compare -u feature | pbcopy


$ git init
$ git add . && git commit -m "It begins."
$ git create -d "My new thing"
→ (creates a new project on GitHub with the name of current directory)
$ git push origin master
```

## Using GitHub for work

Save time at work by opening _pull requests for code reviews_ and pushing to _multiple remotes at once_. Even _GitHub Enterprise_ is supported.

```
$ git config --global --add hub.host my.example.org


$ git push origin feature
$ git pull-request -F prepared-message.md | pbcopy
→ (URL ready for pasting in a chat room)


$ git push production,staging
```
