---
layout: post
title:  "Resources Index"
date:   2023-05-01 18:09:15 -0700
categories: jekyll update
---

# Resources

## Getting started in Exacloud

ACC maintains an [Exacloud wiki](https://wiki.ohsu.edu/display/ACC/Exacloud). You have to be behind the firewall to access it.

A great tutorial created by Ted Laderas can be found [here](https://laderast.github.io/exacloud_tutorial/).
The lustre file system has been replaced by gscratch (`/home/exacloud/gscratch/CEDAR`), most of the other submission details are the same and this provides a great walkthrough.

More information on how to submit jobs can be found [here](https://slurm.schedmd.com/overview.html).

If you are connected to the OHSU VPN, also check out the OHSU Wiki [here](https://wiki.ohsu.edu/display/ACC/Exacloud).
You will find information on basic access, FAQs, policy, and a user guide.

**Important note:**
Save your documents and environments in your `/home/groups/CEDAR/<your_username>` directory, not your `/home/users/<you_username>` directory. The latter has a very limited amount of space and should be limited to basic configuration files like your `.bashrc`.


## Exacloud etiquette

## Installing software in Exacloud

## How to find the Comp Hub speaker rotation

You will need access to the Teams "Comp Hub working group" channel. Click the "Notes & Schedule" tab. On the far left is a small purple icon that resembles 3 books. Click this, then select "Schedule" then "Speaker Rotation - 2022-23". Alternatively, try clicking the link [here](https://teams.microsoft.com/l/entity/0d820ecd-def2-4297-adad-78056cde7c78/_djb2_msteams_prefix_3814411637?context=%7B%22subEntityId%22%3Anull%2C%22channelId%22%3A%2219%3A9352a970dc9f4aa4a51da1569d3dc675%40thread.tacv2%22%7D&groupId=bf21840b-d228-40c8-bb63-9bffe3e9b98c&tenantId=e2737957-fab8-4d7e-94f6-9bd6af9f7158&allowXTenantAccess=false).

## Commonly used tools and how to use them

## How to set up virtual or project environments

## How to generally run workflows

## Containerization on Exacloud

## Tutorial on Snakemake

Presentation on Snakemake can be found here https://github.com/ohsu-cedar-comp-hub/ohsu-cedar-comp-hub.github.io/tree/main/data

## GitHub and Git

The main purpose of this section is to understand and help get started with GitHub and Git. Feel free to add more information or resources as needed.

### Resources

This [link](https://docs.github.com/en/get-started/quickstart/hello-world) does a great job on how to set GitHub.
This [link](https://www.w3schools.com/git/git_getstarted.asp?remote=github) does a great job on how to use Git and pushing/pulling from GitHub.

### GitHub

GitHub is a cloud-based open-source community to create projects, collaborate with others, make contributions while having the ability to version control. This allows people to keep track of files that were changed or edited, revert to previous versions of the files, etc.

GitHub uses Git, which is a version control system that helps keep track of the files and help manage repositories. Therefore, GitHub is a platform to help manage projects by using Git.

### Repository

Repositories are where we store projects. Each project should have its own repository. Within the repo, there can be folders and files, including scripts, images, etc. This [link](https://medium.com/code-factory-berlin/github-repository-structure-best-practices-248e6effc405) is good for describing best practice for setting GitHub strcture, however this webpage includes a lot of information. The main file to always have is the README.md file to describe the project.
README.md is a markdown file that should include information about the project, purpose of the project, description of additional directories within the repo, description of files, including scripts, etc.

### Branches

There are branches within a repository which are like creating different versions of the repository by branching off. There can be several purposes for creating branches, and one purpose is to work on a part of a project (making changes to code, fixing bugs, etc.) without messing with the main repository. Once the adjustment or changes are made to the branch, this can be merged into the original main branch. Therefore, there is always a main branch and new branches are created from the main branch. The new branch contains all the edits which is then merged to the main branch to include all the new edits and updates.

### Commits

Commits is used for saving a change in the repository. For best practice, there should be a message to every commits to annotate and describe what changes were made for good tracking.

### Pull Requests

This is what brings all the commits (changes) together into the main branch. Once pull request is made, we can see the changes that would be made (including addition of lines of codes, deletions, etc). After making the commit (with description message!), open a pull request and merge to combine the edits to the main branch.

During this step, there can be a lot of conflicts. These conflicts are generally due to changes that were made in the main branch that is no longer the same as the branch that was branched off in the beginning (ex: other collaborators made edits and merged their pull requests to the main branch, making this new main branch different from when your branch was created), and when your branch is trying to merge, Git will recognize there are edits that come in conflict with the main branch. These edits can be resolved with quick Google search.

## Tutorial for Jupyterlab/notebook


