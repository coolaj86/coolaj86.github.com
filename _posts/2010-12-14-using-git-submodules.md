---
layout: article
uuid: 15C2F83D-3256-4807-A553-609C217F5895
title: Using git submodules
name: using-git-submodules
created_at: 2010-12-14
updated_at: 2010-12-14
categories: git
---

Goal
====

Explain how to use `git` to track and update submodules of a project.

For example, if I use `ahr` ([`AbstractHttpRequest`](http://github.com/coolaj86/abstract-http-request)) in a project,
I want to track it so that I can always use the latest version of `ahr` with my project.

Example:

**Note**: The official-ish submodule tuts that come up first on Google are misleading - they're written in reverse... ish...

**Note**: You should first create your parent module and then add your child modules.

**Note**: You should add your modules by web URL, not paths!

Let's say I'm at home and adding a submodule, then I go to work and I want to pull the changes.
When I go back home, the submodule has been updated again and I want the new updates.

[`node-mediatags`](http://github.com/coolaj86/node-mediatags.git) is dependent on
[`mediatags`](http://github.com/coolaj86/mtags.git).

[Home] Clone `node-mediatags`

    cd ~/
    git clone git@github.com:coolaj86/node-mediatags.git
    cd node-mediatags

[Home] Add `mediatags` as a git submodule

    mkdir -p vendor
    git submodule add git@github.com:coolaj86/mtags.git vendor/mediatags
    git status
    # On branch master
    # Changes to be committed:
    #   (use "git reset HEAD <file>..." to unstage)
    #
    # new file:   .gitmodules
    # new file:   vendor/mediatags
    #
    git commit -m "added mediatags as submodule"
    git push origin master

[LappyX64] Now on a box which has never had this repository

    git clone git@github.com:coolaj86/node-mediatags.git
    git submodule init
    git submodule update

[Work] Now on another box where the repo already exists I'll pull in the changes

    git pull origin master

    remote: Counting objects: 5, done.
    remote: Compressing objects: 100% (3/3), done.
    remote: Total 4 (delta 1), reused 0 (delta 0)
    Unpacking objects: 100% (4/4), done.
    From git@github.com:coolaj86/node-mediatags
     * branch            master     -> FETCH_HEAD
    Updating 40f908f..2898061
    Fast forward
     .gitmodules      |    3 +++
     vendor/mediatags |    1 +
     2 files changed, 4 insertions(+), 0 deletions(-)
     create mode 100644 .gitmodules
     create mode 160000 vendor/mediatags

    ls vendor/mediatags
    # empty
    git submodule init
    git submodule update

[Home] Now I'll update the submodule in its proper repository

    cd ~/mediatags
    git pull origin master
    # edit README.md
    git add README.md
    git commit -m "updated README"
    git push origin master

[Home] Now I'll update the module on which the submodule depends

Here's the counter-inuitive part. `git submodule update` doesn't update the submodule.
Instead, you have to go into the To update a git submodule, you

  * Checkout a branch of the submodule
  * Make changes, commits, pull, merge, as per usual
  * Commit the changes
  * Add the submodule (not its changes), which changes the HEAD in `.gitmodules`

First I update the parent module, then the child

    cd ~/node-mediatags
    git pull origin master
    From github.com:coolaj86/node-mediatags
     * branch            master     -> FETCH_HEAD
    Already up-to-date.
    git submodule update
    # does nothing, even though the module has changed

    cd ~/node-mediatags/vendor/mediatags
    git checkout master
    git pull

**Note**: You must not using a trailing slash '/' after the submodule name

    cd ~/node-mediatags
    git status
    git add vendor/mediatags # DO NOT using a trailing slash '/'
    git commit -m "added latest mediatags"
    git push origin master

[Laptop] now I'll pull the submodule changes from yet another machine

This time the remote changes in the submodule are pulled in as expected.

    cd ~/node-mediatags
    git pull origin master
    remote: Counting objects: 5, done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 3 (delta 1), reused 0 (delta 0)
    Unpacking objects: 100% (3/3), done.
    From github.com:coolaj86/node-mediatags
     * branch            master     -> FETCH_HEAD
    Updating 2898061..6ca455e
    Fast-forward
     vendor/mediatags |    2 +-
     1 files changed, 1 insertions(+), 1 deletions(-)

Resources
====

  * http://chrisjean.com/2009/04/20/git-submodules-adding-using-removing-and-updating/
  * http://book.git-scm.com/5_submodules.html
