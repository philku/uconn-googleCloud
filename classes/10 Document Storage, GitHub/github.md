
# [Git](https://git-scm.com/)
Git is a <b>Version Control System (VCS)</b> that allows you to track
changes over time, so you can recall changes and develop your codebase
in a controlled manner. Git works primarily through the command line,
but many integrations are available for popular IDEs (Integrated
Development Environments).

Git is a powerful system with many capabilities. We will only focus on
the commands and features most people need to know. To explore all of Git,
click on the title of this section.

<div align="center">
    <img src="http://imgs.xkcd.com/comics/git.png">
</div>

In this course we will be using Git in the context of GitHub, the most
popular hosting platform and community for software development using
Git.

# [GitHub](https://github.com)
GitHub is a free collaborative platform for sharing and working on code.
It allows teams of developers to publish and maintain software in an
organized fashion.

Through the use of: repositories, branches, commits, pushes, and pull requests; code can
be organized, tested, staged, and deployed.

## Repositories (Repos)
Repositories function as the main storage container for your code. Generally, a project
will use one repository.

### Setting up a Repo
You can set up a repo from GitHub or from the command line. In this
example, we will set it up quickly and easily from the GitHub website.

...

## Branches
The branches of a repository are much like the branches of a tree. A
repository will always have one main "master" branch. This branch generally
contains the tested code that is in production.

When a developer would like to implement a new feature to an existing
codebase, they typically will create a new branch. The new branch will
contain the same code as the master branch, but a developer can make
changes to their branch, without impacting the master branch.

## Commits
Whenever you do work on a specific part of the codebase, you should
create a commit. Commits organize the code into chunks and allow you
and other developers to see the changes you made. It is best practice to
work on one thing at a time, and commit before switching to another task
in the codebase. You should also include a commit message that labels the
work you've done, to make it easier for developers that may need to review
your code, or approve the work you've done.

## Push
To send your code from your local environment to GitHub, you will need
to "push" it to GitHub. This is essentially just uploading your changes.

## Pull (merge) Requests
If you're happy to the changes you've made to your branch, in order to
merge your code into the master branch, you'll have to submit a pull/merge
request. This request goes to the maintainers of the repository and
gives them some details about what you've done. They can review your code,
and respond. Using GitHub, the maintainers have the option to approve,
reject, or ask you to fix some parts of your code. If approved, you will
be notified, and have the opportunity to put your code into the master branch.

# Lab
The first thing you'll need to get started is a GitHub account. If you
do not have one, you should register at this time:

# Resources