---
title: "Additional Material: Git Intro"
layout: episode
teaching: 15
exercises: 15
questions:
- "What is Git?"
- "What are the main Git commands?"
objectives:
- "Initialize and clone repositories."
- "Make commit to a repository."
keypoints:
- "Git is a collaborative version control system that allows you to go back to any past version of your project."
- "Using Git is an extremely useful habit that saves a lot of time, especially when several people are working on the same project."
---

## Introduction
This short episode is dedicated to Git and GitHub - two popular tools for keeping 
track of the changes that happen in your code. A full explanation of Git and its features
would require a separate workshop (you can have a look at e.g. this 
[Carpentries Git Novices workshop](https://swcarpentry.github.io/git-novice/index.html)), however,
you will benefit even from using just few most popular features. In fact, in most cases
these features are all that you'll ever need!

**Git** is a free, collaborative, open-source version control system - a tool for recording the changes made to your
working files, and giving you an opportunity to recover any previous version of these files. 
Git does not upload all of the tracked files every time when you **make a commit**; instead it records only 
the changed parts. This approach is computationally efficient and flexible, however, it requires some input from
your side. Git does not work as an auto-backup; in order to be useful, you have to explicitly state,
what are the changes that you want to save.

![Version control before Git](../fig/phd101212s){: .image-with-shadow width="500px" }
{% comment %}Version control before Git. “notFinal.doc” by Jorge Cham, [https://www.phdcomics.com](https://www.phdcomics.com){% endcomment %}

**GitHub** is a cloud-based hosting platform for storing Git repositories. Apart from storing the repositories,
it provides us with a number of additional functionalities:
- You can add collaborators to your repositories and manage their rights;
- You can let know the owners of other repositories about some bugs or ask for additional features ('Issues');
- You can propose your changes to the repositories of other people, and other people can propose changes to your projects ('Pull requests');
- You can edit your files right at the website;
- You can add automatic actions that will trigger at each commit, such as launching tests or build a website from your updates files;
- You can create private repository, that will be visible only for you;
- And many more.

Some of these features will be covered in the upcoming workshop. For now, let's concentrate on the basics.

### Installing Git on your PC

If you are using Windows or Linux OS, most likely, you already have Git available. 
You can check if it's true by running the following code in the terminal:
~~~
$ git --version
~~~
{: .language-bash}
You should get the version of your Git installation, something like this:
~~~
git version 2.18.4
~~~
{: .output}

If you are using Mac OS or seem to not have Git on Windows or Linux, 
[here](https://carpentries.github.io/workshop-template/install_instructions/#git)
you will find the instructions on how to fix this.

### Setting up a Github account
Setting up a GitHub account will take you some time, since you need not only 
register on the website, but also generate an SSH key on your PC, add it to GitHub
and set up an authentication method (which can be an app on your smartphone or text messages).
You can find the instructions on how to register on 
GitHub [here](https://swcarpentry.github.io/git-novice/index.html#creating-a-github-account).

### Connecting the Git on your PC with your GitHub account
Now you have the last step: you have to tell your Git installation what are your credentials
on GitHub. You have to do it only once, when you use Git for the first time on your machine.
To do this, in the terminal you have to configure the username and the email to which your GitHub
account is connected. 
~~~
$ git config --global user.name "Your Name"
$ git config --global user.email "name@example.com"
~~~
{: .language-bash}

By using `--global` you are telling Git to use these credentials for all repositories.

That's it! Now you can start using GitHub.

## Basic Git usage workflow

**Git init**
Local git init, Github git init, creating .gitignore and README.md

**Adding changes**
1. Git pull (explain why)
2. Make a `.txt` file
3. Add 'Hello world'
4. Git add, commit, push
5. Writing good Git commit messages
6. Delete your local repo
7. Git clone

{% include links.md %}
