# A tidy, linear Git history

[Original URL](http://www.bitsnbites.eu/?p=221)

> One of the things that is overlooked in many Git based projects is the value of a linear commit history. In fact, many tools and guidelines discourage Git workflows that aim for a linear history. I...

One of the things that is overlooked in many Git based projects is the value of a linear commit history. In fact, many tools and guidelines discourage Git workflows that aim for a linear history. I find this sad, since a tidy history is very valuable, and there is a straight forward workflow that ensures a linear history.

## Linear vs non-linear history

A linear history is simply a Git history in which all commits come after one another. I.e. you will not find any merges of branches with independent commit histories.

[![1 - nonlinear-vs-linear](http://www.bitsnbites.eu/wp-content/uploads/2015/12/1-nonlinear-vs-linear.png)](http://www.bitsnbites.eu/wp-content/uploads/2015/12/1-nonlinear-vs-linear.png)

## Why do you want a linear history?

Besides being tidy and logical, a linear history comes in handy when:

- Looking at the history. A non-linear history can be very hard to follow – sometimes to the point that the history is just incomprehensible.
- Backtracking changes. For instance: "Did feature A get introduced before or after bugfix B?".
- Tracking down bugs. Git has a very neat function called [Git bisect](http://git-scm.com/docs/git-bisect), which can be used to quickly find which commit introduced a bug or regression. However, with a non-linear history, Git bisect becomes hard or even impossible to use.
- Reverting changes. Say that you found a commit that caused a regression, or you want to remove a feature that was not supposed to go out in a specific release. With some luck (if your code has not changed too much) you can simply revert the unwanted commit(s) using [Git revert](http://git-scm.com/docs/git-revert). However, if you have a non-linear history, perhaps with lots of cross-branch merges, this will be significantly harder.

There are probably another handful of situations in which a linear history is very valuable, depending on how you use Git.

Point is: The less linear your history is, the less valuable it is.

## Causes of a non-linear history

In short, every merge commit is a potential source of a non-linear history. However, there are different kinds of merge commits.

### Merge from a topic branch into master

When you are done with your topic branch and want to integrate it into master, a common method is to merge the topic branch into master. Perhaps something along the lines:

```
git checkout master
git pull
git merge --no-ff my-topic-branch
```

A nice property with this method is that you preserve the information about which commits were part of your topic branch (an alternative would be to leave out "–no-ff", which would allow Git to do a fast-forward instead of a merge, in which case it may not be as clear which of the commits actually belonged to your topic branch).

The problem with merging to master arises when your topic branch is based on an old master instead of the latest tip of master. In this case you will inevitably get a non-linear history.

[![2 - merging-old-branch](http://www.bitsnbites.eu/wp-content/uploads/2015/12/2-merging-old-branch.png)](http://www.bitsnbites.eu/wp-content/uploads/2015/12/2-merging-old-branch.png)

Whether or not this will be a common problem or not largely depends on how active the Git repository is, how many developers are working concurrently, etc.

### Merge from master into a topic branch

Sometimes you want to update your branch to match the latest master (e.g. there are some new features on master that you want to get into your topic branch, or you find that you can not merge your topic branch into master because there are conflicts).

A common method, which is even recommended by some, is to merge the tip of master into your topic branch. _This is a major source of non-linear history!_

[![3 - merging-master-into-topic](http://www.bitsnbites.eu/wp-content/uploads/2015/12/3-merging-master-into-topic.png)](http://www.bitsnbites.eu/wp-content/uploads/2015/12/3-merging-master-into-topic.png)

## The solution: Rebase!

[Git rebase](http://git-scm.com/book/ch3-6.html) is a very useful function that you should use if you want a linear history. Some find the concept of rebasing awkward, but it's really quite simple: replay the changes (commits) in your branch on top of a new commit.

For instance, you can use Git rebase to change the root of your topic branch from an old master to the tip of the latest master. Assuming that you have your topic branch checked out, you can do:

```
git fetch origin
git rebase origin/master
```

[![4 - rebasing](http://www.bitsnbites.eu/wp-content/uploads/2015/12/4-rebasing.png)](http://www.bitsnbites.eu/wp-content/uploads/2015/12/4-rebasing.png)

Note that the corresponding merge operation would be to merge the tip of master into your topic branch (as depicted in the previous figure). The resulting contents of the files in your topic branch would be the same, regardless if you do a rebase or a merge. However, _the history is different_ (linear vs non-linear!).

This sounds all fine and well. However, there are a couple of caveats with rebase that you should be aware of.

### Caveat 1: Rebase creates new commits

Rebasing a branch will actually create new commits. The new commits will have different SHA:s than the old commits. This is usually not a problem, but _you will run into trouble if you rebase a branch that exists outside of your local repository_ (e.g. if your branch already exists on origin).

If you were to push a rebased branch to a remote repository that already contains the same branch (but with the old history), you would:

1. Have to force push the branch (e.g. `git push --force-with-lease`), since Git will not allow you to push a new history to an existing branch otherwise. This effectively replaces the history for the given remote branch with a new history.
2. Possibly make someone else very unhappy, since their local version of the given branch no longer matches the branch on the remote, which can lead to all sorts of trouble.

In general, _avoid overwriting the history of a branch on a remote_ (the one exception is to rewrite the history of a branch that is under code review – depending on how your code review system works – but that is a different discussion).

If you need to rebase a branch that is shared with others on a remote, a simple workflow is to create a new branch that you rebase, instead of rebasing the original branch. Assuming that you have `my-topic-branch` checked out, you can do:

```
git checkout -b my-topic-branch-2
git fetch origin
git rebase origin/master
git push -u origin my-topic-branch-2:my-topic-branch-2
```

...and then tell people working on `my-topic-branch` to continue working on `my-topic-branch-2` instead. The old branch is then effectively obsolete, and should not be merged back to master.

### Caveat 2: Resolving conflicts in a rebase can be more work than in a merge

If you get a conflict in a merge operation, you will resolve all the conflicts as part of that merge commit.

However, in a rebase operation, you can potentially get a conflict for _every commit_ in the branch that you rebase.

Actually, many times you will find that if you get a conflict in a commit, you will encounter related (very similar) conflicts in subsequent commits in your branch, simply because commits in a topic branch tend to be related (e.g. modifying the same parts of the code).

The best way to minimize conflicts is to keep track of what's happening in master, and avoid letting a topic branch run for too long without rebasing. Dealing with small conflicts up front every now and then is easier than to handle them all in one big happy conflict mess at the end.

## Some warnings for GitHub users

GitHub is great at many things. It's fantastic for Git hosting, and it has a wonderful web interface with code browsing, nice Markdown functionality, Gist, etc.

Pull requests, on the other hand, has a few functions that actively thwarts linear Git history. It would be very welcome if GitHub actually fixed these issues, but until then you should be aware of the shortcomings:

### "Merge pull request" allows non-linear merges to master

It can be tempting to press the green, friendly looking "Merge pull request" button to merge a topic branch into master. Especially as it reads "This branch is up-to-date with the base branch. Merging can be performed automatically".

[![GitHub merge pull request](http://www.bitsnbites.eu/wp-content/uploads/2015/12/github-merge-pull-request.png)](http://www.bitsnbites.eu/wp-content/uploads/2015/12/github-merge-pull-request.png)

What GitHub is really saying here, is that the branch can be merged to master _without conflicts_. It does _not_ check whether or not the pull request branch is based on the latest master.

In other words, if you want a linear history, you need to make sure that the pull request branch is rebased on top of the latest master yourself. As far as I can tell, no such information is available via the GitHub web interface (unless you're using "protected branches" – see below), so you need to do it from your local Git client.

Even if the pull request is properly rebased, there is no guarantee that the merge operation in the GitHub web interface will be atomic (i.e. someone may push changes to master before your merge operation goes through – and GitHub will not complain).

So really, the only way to be sure that your branches are properly rebased on top of the latest master is to do the merge operation locally and push the resulting master manually. Something along the lines:

```
git checkout master
git pull
git checkout my-pullrequest-branch
git rebase master
git checkout master
git merge --no-ff my-pullrequest-branch
git push origin master
```

If you're unlucky and someone manages to push changes to master between your pull and push operations, your push operation will be denied. This is a good thing, however, since it guarantees that your operation is atomic. Just `git reset --hard origin/master` and repeat the above steps until it goes through.

Note: Respect your project guidelines w.r.t. code reviewing and testing. E.g. if you're running automatic tests (builds, static analysis, unit tests, ...) as part of a pull request, you should probably re-submit your rebased branch (either using git push -f, or by opening a new PR) rather than just updating the master branch manually.

### Protected branches functionality encourages merges from master

If you are using [protected branches and status checks](https://github.com/blog/2051-protected-branches-and-required-status-checks) in your GitHub project, you actually get protection from merging a pull request branch into master unless it is based on the latest master (I think the rationale is that status checks performed on the PR branch should still be valid after merging to master).

However... If the pull request branch is not based on the latest master, you are presented with a friendly button called "Update branch", with the text "This branch is out-of-date with the base branch. Merge the latest changes from master into this branch".

[![github-update-branch](http://www.bitsnbites.eu/wp-content/uploads/2015/12/github-update-branch.png)](http://www.bitsnbites.eu/wp-content/uploads/2015/12/github-update-branch.png)

At this point, your best option is to rebase the branch locally and force-push it to the pull request. Assuming that you have `my-pullrequest-branch` checked out, do:

```
git fetch origin
git rebase origin/master
git push -f
```

Unfortunately, GitHub pull requests do not play well with force pushes, so some of the code review information may get lost in the process. If that is not acceptable, consider creating a new pull request (i.e. push your rebased branch to a new remote branch and create a pull request from the new branch).

## Conclusion

If you care about a linear history:

- Rebase your topic branch on top of the latest master before merging it to master.
- _Don't_ merge master into your topic branch. Rebase instead.
- When sharing your topic branch with others, create a new branch whenever you need to rebase it (`my-topic-branch-2`, `my-topic-branch-3`, ...).
- If you're using GitHub pull requests, be aware:

  - The "Merge" button does _not_ guarantee that the PR branch is based on the latest master. Rebase manually when necessary.
  - If you're using protected branches with status checks, _never_ press the "Update branch" button. Rebase manually instead.

If you don't care too much about a linear Git history – happy merging!

## Wishlist for GitHub

To you fine people working at GitHub: Please add support for rebase workflows in pull requests (these could be opt-in repository options).

- Add the possibility to disable the Merge button/operation in pull requests if the branch is not based on the latest master (this should not require the use of status checks).
- Add a "Rebase onto latest master" button. In many cases this should be a conflict free operation that can easily be done via the web interface.
- Preserve pull request history (commits, comments, ...) after a rebase / force push.

[EDIT: I did a follow-up article about different Git history models: [Git history: work log vs recipe](http://www.bitsnbites.eu/?p=241)]
