# Source control
All scripts, artifacts and source code should be held under source control. Due to the number of different technologies used in the team there is no single source control solution. However Git is our tool of choice and this page provides details on how to use it.

## Git flow

Git flow is a process of using Git for software development. Git is very unspecific in how you use it so Git flow provides some guidance and processes about using Git on your own or as part of a team for software development.

A full description of Git flow can be found here: [https://nvie.com/posts/a-successful-git-branching-model/](https://nvie.com/posts/a-successful-git-branching-model/) this documentation takes all of its information from this linked web page.

## Workflows
A project should consist of the following branches:

* **master** - this is what is currently in production
* **develop** - this is the state of the source code up to the latest developments

### Feature branches

Feature branches are where you do your work. You would create a branch of the **develop** branch and make any changes in this new branch. The name of a feature branch should be **feature\\\<unique name\>** e.g.

* feature\ENH0012345
* feature\new-login

You create a feature branch like this:

```shell
$ git checkout develop # make sure you are in the develop branch
$ git pull # get any updates to the develop branch
$ get checkout -b feature/my-new-feature develop # create the new branch
```

Once you have completed your feature you merge it back into the develop branch like this:

```shell
$ git checkout develop # switch to the develop branch
$ git merge --no-ff feature/my-new-feature # merge the feature branch
$ git branch -d feature/my-new-feature # delete the feature branch
$ git push origin develop # push changes to the development branch to the origin
```

the **--no-ff** flag causes the merge to always create a new commit object, even if the merge could be performed with a fast-forward. This avoids losing information about the historical existence of a feature branch and groups together all commits that together added the feature.

### Release branches

Release branches support preparation of a new production release. They allow for last-minute dotting of i's and crossing t's. Furthermore, they allow for minor bug fixes and preparing meta-data for a release (version number, build dates, etc.). By doing all of this work on a release branch, the **develop** branch is cleared to receive features for the next big release. The name of the release branch much be **release[-identifier]** e.g.

* release
* release-v2
* release-feisty-ferret

You create a release branch like this:

```shell
$ git checkout develop # make sure you are in the develop branch
$ git pull # get any updates to the develop branch
$ get checkout -b release-1.2 develop # create the new branch
```

Once the release is reach we merge it into the master branch and create a tag for the release:

```shell
$ git checkout master # switch to the master branch
$ git merge --no-ff release-1.2 # merge the release branch
$ git push origin master # push changes to the master branch to the origin
```

We then merge any changes in the release branch back into the develop branch:

```shell
$ git checkout develop # switch to the develop branch
$ git merge --no-ff release-1.2 # merge the release branch
$ git push origin develop # push changes to the develop branch to the origin
```

we can now clean up by deleting the release branch:

```shell
$ git branch -d release-1.2 # delete the release branch
```