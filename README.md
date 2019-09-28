Primary author: Ruchita Joshi

This page describes the Code Management practices stage of the Software Development Life Cycle (SDLC) for College Applications.


# Table of contents

  * [Overview](#overview)
  * [RACS Git Flow](#racs-git-flow)
      * [The Flow](#the-flow)
  * [Concepts](#concepts)
    * [Branches](#branches)
    * [Repositories](#repositories)
        * [.Net Solutions](#net-solutions)
        * [SQL Databases](#sql-databases)
        * [PHP Projects](#php-projects)
        * [Javascript Projects](#javascript-projects)
        * [Applications with Code & Configurations](#applications-with-code-configurations)
    * [Feature Syntax](#feature-syntax)
    * [Code Reviews & Merge Requests](#code-reviews-merge-requests)
        * [Screenshots](#screenshots)
  * [Examples](#examples)
    * [Setting up a new project](#setting-up-a-new-project)
        * [Commands](#commands)
    * [Migrating an existing project to RACS GIT flow](#migrating-an-existing-project-to-racs-git-flow)
        * [Commands](#commands-1)
    * [Working with issues](#working-with-issues)
        * [Commands](#commands-2)
    * [Moving dev into master for production release](#moving-dev-into-master-for-production-release)
        * [Commands](#commands-3)
  * [Git Commands](#git-commands)
    * [Tags](#tags)
    * [Branches](#branches-1)
    * [Amend Message](#amend-message)
  * [Troubleshooting](#troubleshooting)
    * [Rebase with VIM on Windows](#rebase-with-vim-on-windows)
    * [Set Global Line Endings](#set-global-line-endings)
    * [Merge Conflicts](#merge-conflicts)
        * [Override using Ours strategy](#replace-the-head-of-a-branch-with-your-own)

# Overview
 
Git is a version control system that is used for software development and other version control tasks. As a distributed revision control system it is aimed at speed, data integrity, and support for distributed, non-linear workflows. Git was created by Linus Torvalds in 2005 for development of the Linux kernel, with other kernel developers contributing to its initial development.
  
# RACS Git Flow

A visualization of the RACS Git flow is available here - [RACS Git Graph Example](https://git.surgeons.org/HowTo/Git/raw/master/Screenshots/Branching/Branching.png)

## The Flow

1. Pull updates from **remote dev** branch to **local dev**
2. Create a feature branch from **dev** with a name following the naming convention, i.e. `issue#22`
3. Commit changes to the feature branch
4. Pull potentially new updates from the **remote dev** branch
5. Merge **dev** into your feature branch, i.e. dev > issue#22
6. In GitLab raise merge request to merge the **feature** branch into **dev** branch

Once dev contains all of the desired changes and has been tested in **staging** you will need to push those changes into **master** for a **production** build/deployment

7. In GitLab raise merge request to merge the **dev** branch into the **master** branch
8. Pull latest **master** from remote and tag with release number such as v1.2.3 `git checkout master && git pull && git tag vX.X.X && git push --tags`
9. Merge **master** into the **dev** branch to complete the loop `git checkout dev && git pull && git pull origin master && git push`

# Concepts

## Branches

* master is the default branch, and is sacred
* dev is the development branch and all commits are pulled into it via a GitLab merge request
* feature branches are branches created soley to focus on a **single** issue/ticket and are named with the following syntax; `<type>#<ticketNumber>`, i.e. `issue#42`, `bug#22`, `hotfix#123`

## Feature Syntax

When referencing a local issue that resides within the same repository as the codebase use;

`<type>#<ticketNumber>`

* issue#123
* bug#123
* hotfix#123

When referencing an issue located in a different respository to the codebase you're committing against use;

`<repoName>/<type>#<ticketNumber>`

* myRepo/issue#123
* myRepo/bug#123
* myRepo/hotfix#123

*Note: We decided not to use the full gitlab reference of `group/project` which results in `<group>/<repoName>/<type>#<ticketNumber>`, as the repoName should be unique enough, and full gitlab repo referencing can be used in commit messages.*

*Note2: To comment in a commit message regarding another repositorys issue, the syntax is `<group>/<repoName>#<ticketNumber>` i.e. `victory/k2-enrolment#376`


## Repositories

### .Net Solutions

### SQL Databases

When using the RedGate SQL Source control tool, we follow the following rules.

1. Download and install SQL Toolbelt Essentials
2. Create a git repository for the SQL database within the associated project group, i.e. for **Portfolio** it would be `Portfolio_DB`
3. Select the sql database you wish to source control
4. Push SQL source control to the newly created repository `Portfolio_DB`

For more information to setting up SQL Toolbelt please see here: [Setting Up Redgate SQL Source Control](https://git.surgeons.org/HowTo/Git/blob/master/Redgate.md#sql-source-control)

*Note: Never store the source controlled database within the solutions repository - they must always be kept seperately*


### PHP Projects

### Javascript Projects

### Applications with Code & Configurations



## Code reviews & Merge Requests

No changes are to be committed directly from a **feature** branch into the **dev** branch.

Instead, once the feature branch, i.e `issue#42` has been developed and tested locally, the developer must then do the following;

* Raise a Merge Request in the GitLab GUI, i.e. `https://git.surgeons.org/<group>/<repository>/merge_requests/new`
* Select the **Source** branch and **Destination** branch, potentially Source **issue#42** and Destination **dev**, respectfully.
* Select **Compare branches and continue**
* Provide a **description** of the merge
* **Assign** another developer to perform the **Code Review** - `You cannot approve your own merge requests!`
* Select the appropriate milestone/release
* Select the **Remove source branch when merge request is accepted**
* Select **Submit merge request**

The developer who was **assigned** the merge request should then;

* Assess the changes within the merge request and confirm that they look correct - **please also use this review as a knowledge sharing moment**
* If you **don't have any issues** with the merge request, please **provide a comment**
* Then select **Accept Merge Request**


_Important: **If there is an issue with the MR** please provide a relevant comment and select **Comment & close merge request**_

### Screenshots

![Step 1](Screenshots/MRs/MergeRequest1.png)
![Step 2](Screenshots/MRs/MergeRequest2.png)
![Step 3](Screenshots/MRs/MergeRequest3.png)

# Examples

## Setting up a new project

All RACS projects utilitize 2 persistent branches;

* master (default)
* dev (short for 'development')

The **master** branch is sacred, and only commits that have passed UAT and have been signed off in staging are merged into master.

The **dev** branch is the where all completed development tasks that have passed a **Code review** and have had their merge request accepted are committed into.

### Commands

```bash
# Enter the local directory where repos are kept
cd /c/git/repos
# (Optional 1a) Create folder for new project
mkdir myProject && cd myProject
# (Optional 1b) Initialize a GIT instance
git init
# Clone the empty repo from git.surgeons.org
git clone git@git.surgeons.org:<project>/<repository>.git
# Create development branch, referred to as 'dev'
git checkout -b dev
# (Optional 1c) Add GIT Remote for SSH
git remote set-url origin git@git.surgeons.org:<project>/<repository>.git
# Push the new dev branch to the remote git server and setup tracking
git push -u origin dev
```

## Migrating an existing project to RACS GIT flow

Originally RACS projects only had a single persistent branch, **master**, and utilized **release** branches (basically feature branches).

Inorder to align an existing repo to the new RACS GIT flow, please follow the below commands.

### Commands

```bash
# Enter the local directory where the existing git repo is
cd /c/git/repos/myProject
# Create development branch, referred to as 'dev'
git checkout -b dev
# Push the new dev branch to the remote git server and setup tracking
git push -u origin dev
```

## Working with issues

When a git issue is raised, below is the agreed approach to working with the code base.

### Commands
```bash
# Enter the local directory of the git repo
cd /c/git/repos
# Pull the latest changes from the git remote
git pull
# Create a new branch from the dev branch by first checking it out
git checkout dev
# The new branch must follow one of the following naming conventions
# Alt 1 - Issue, referrencing the ticket number
git checkout -b issue#123
# Alt 2 - Bug, referrencing the ticket number
git checkout -b bug#42
# Alt 3 - Hotfix, referrencing the ticket number
git checkout -b hotfix#2
# Once the development work is complete pull from dev branch to ensure you have latests base commits
git pull origin dev
# Resolve any merge conflicts in your branch.. and push your branch to the remote
git push 
# Raise a merge request to merge your feature branch issue#42 into dev
```

## Moving dev into master for production release

Once you are ready to promote changes from staging (the *dev* branch) into production (the *master* branch) you will need to follow the below steps.

Essentially we are raising a merge request from **dev** > **master** and then pulling changes back from **master** > **dev** to continue with development.

*Note: we won't instantly need to do a pull from **dev** > **featureBranch** every time we deploy to production, as the new process already includes a step to pull from **dev** > **featureBranch** prior to any MRs from the **featureBranch** > **dev** branch.*

### Commands

```bash
# First you will need to raise a MR in gitlab from `dev` > `master`
# Make sure the MR is assigned to **someone other than yourself**
# Make sure the MR is associated with a **milestone** that represents the release, i.e. `v1.2.22`
# **DO NOT** delete the `source branch` on completion - ideally we'd like to retain the `dev` branch
# Once the MR is accepted you will need to do a git pull on your local master to pickup the changes
git checkout master && git pull
# Next you want to checkout the `dev` branch and pull the changes in from `master` to update dev
git checkout dev && git pull origin master
git push
# master now has the latest changes from production, dev now has changes from master and is ready to be branched from for future development work.
```

# GIT Commands

A full list of git commands is available in the [Git Cheat Sheet](git-cheat-sheet.md)

## Tags

Git has the ability to tag specific points in history as being important. Typically people use this functionality to mark release points (v1.0, and so on). In this section, you’ll learn how to list the available tags, how to create new tags, and what the different types of tags are.


```bash
# List local tags
git tag
# Create local tag, usually following the following syntax 'v<Major>.<Minor>.<Patch> ie. v1.0.0
git tag <tagName>
# Create a local tag with an annotation
git tag -a <tagName> -m "Initial version of product"
# Push tag from local to remote
git push origin --tags
# Or
git push origin <tagName>
# Delete local tag
git tag -d <tagName>
# Delete remote tag
git push origin :refs/tags/<tagName>
```

## Branches

General GIT branch commands

```bash
# Delete local branch
git branch -d <branchName>
# Delete remote branch
git push --delete origin <branchName>
# To delete a branch that has a tag with the same name requires you to be more explicity
git push origin :refs/heads/<branchName>
```

## Amend Message

To amned a GIT Commit message do the following..

```bash
# Amend in text editor
git commit --amend
# If your default editor is VIM type the below to start inserting text
i
# modify text as appropriate
# Save the message
SHIFT + wq!
```


# Troubleshooting

## Rebase with VIM on Windows

If you encounter an error while trying to use -i interactive rebase on windows, it might be because your GIT client doesn't have a default editor, i.e. vim

Run this in gitbash to default vim as the editor
`git config --global core.editor "vim"`

## Set Global Line Endings

If you're using GIT on windows, you may want to set the default global git line ending setting to CR LF
`git config --global core.autocrlf true`

## Merge Conflicts

If you experience a merge conflict through a Merge Request you can manually remediate it locally.

```bash
# Checkout destination branch and update
git checkout <destinationBranch>
git pull
# Merge from source with no fast forward
git merge --no-ff <sourceBranch>
# The branch will now be BRANCH | MERGING
# The console will list files where conflicts exist, you can manually fix them
# Once fixed you can push to the remote
git push origin <destinationBranch>
```

### Replace the head of a branch with your own

```bash
# Following the 'ours' merge strategy:
git checkout <destinationBranch>
# merge branches, but use our branch head
git merge -s ours <sourceBranch> 
```
