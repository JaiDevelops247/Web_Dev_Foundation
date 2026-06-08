## Introduction

Arjun now has a local Git repository with a clean commit history. His portfolio project is properly tracked -- no more `index_FINAL_v2.html`. He can see every version, every change, and every commit message.

But the repository lives on one machine. His laptop. If it gets stolen, dropped, or reset, the entire history is gone with it. If he wants to continue working at a friend's house or a college lab, he has no way to access the project. If a potential employer asks for a link to his work, there is nothing to share.

This is the limit of local-only version control: it is personal and fragile. The history is safe from mistakes, but not from hardware failure, and it is invisible to everyone else.

**GitHub** is where this changes. GitHub is a cloud hosting service for Git repositories. It stores your repository on its servers, accessible from any browser or machine, shareable with a URL, and safe from anything that could happen to your local hardware. It is built on top of Git, so everything you already know -- commits, history, branches -- works exactly the same way. GitHub just adds a place for all of it to live on the internet.

### Creating a GitHub Account

Before anything else, you need a GitHub account. Go to **github.com** and click "Sign up".

<!-- 
IMAGE: The GitHub homepage (github.com) with the "Sign up" button prominently visible in the top-right corner. Caption: "Create a free account at github.com. Everything in this article requires one." Style: realistic browser screenshot of the GitHub homepage.
-->

The signup process asks for:
- A username (this becomes part of your profile URL: `github.com/yourusername` -- choose something professional if you plan to share this with employers)
- An email address
- A password

Verify your email when GitHub sends the confirmation. Once you are in, your GitHub dashboard is where all your repositories will appear.

### What GitHub Adds on Top of Git

Git is a tool that runs on your machine. GitHub is a website that hosts Git repositories in the cloud. The relationship is straightforward:

- Your local repository (managed by Git) is the place where you work
- The GitHub repository (called the **remote**) is the backup and sharing point

When you want to save your local commits to GitHub, you **push** them. When you want to bring changes from GitHub down to your local machine, you **pull** them.

<!-- 
IMAGE: A two-computer diagram. On the left, a laptop labeled "Local Machine" with a Git repository icon. On the right, a cloud labeled "GitHub (Remote)" with a repository icon. An upward arrow from local to cloud labeled "git push". A downward arrow from cloud to local labeled "git pull". Caption: "Push sends your local commits to GitHub. Pull brings GitHub's commits to your local machine. Both machines stay in sync." Style: flat vector diagram with laptop and cloud icons and bidirectional labeled arrows.
-->

### Creating a Repository on GitHub

Log in to GitHub and click the **"+"** icon in the top-right corner, then "New repository".

<!-- 
IMAGE: The GitHub "Create a new repository" page. Fields visible include "Repository name", "Description (optional)", and two radio buttons for "Public" and "Private". At the bottom, a "Create repository" button. The "Initialize this repository with a README" checkbox is visible but unchecked. Caption: "The new repository page on GitHub. Leave 'Initialize with README' unchecked if you already have local commits to push." Style: realistic browser screenshot of the GitHub new repository form.
-->

Fill in the details:

**Repository name:** Use the same name as your local folder (`portfolio`, for example). It does not have to match, but keeping them consistent avoids confusion.

**Description:** Optional, but a one-line description is good practice and appears on your profile.

**Public vs Private:**
- **Public:** Anyone on the internet can view the repository. The code, history, and README are visible to all. Good for portfolio projects, open source work, and anything you want to share.
- **Private:** Only you (and people you explicitly invite) can see it. Good for work in progress, client projects, or anything not ready to be public.

**"Initialize this repository with a README":** Leave this **unchecked** if you already have commits in a local repository. Initialising with a README creates a commit on GitHub that your local repository does not have, which causes a conflict when you try to push. If you are starting a brand new project with no local history, checking this is fine.

Click **"Create repository"**. GitHub shows you an empty repository with setup instructions. Keep this page open -- you will need the repository URL.

### Linking Your Local Repository to GitHub: git remote add

Your local repository does not automatically know that a GitHub repository exists. You have to tell it. This is done with `git remote add`:

```bash
git remote add origin https://github.com/yourusername/portfolio.git
```

Breaking this down:
- `git remote add` -- adds a connection to a remote repository
- `origin` -- the name you are giving this remote. `origin` is the universal convention for the primary remote. You could name it anything, but every tutorial, team, and tool expects `origin`
- The URL -- the address of your GitHub repository. Copy this from the GitHub repository page

Verify the connection was set up:

```bash
git remote -v
```

```
origin  https://github.com/arjun/portfolio.git (fetch)
origin  https://github.com/arjun/portfolio.git (push)
```

This shows that `origin` is configured for both fetching and pushing. The local repository now knows where its remote counterpart is.

### Pushing to GitHub for the First Time: git push -u origin main

With the remote linked, push the local commits to GitHub:

```bash
git push -u origin main
```

- `git push` -- send commits to the remote
- `-u` -- sets the "upstream tracking relationship": tells Git that this local branch (`main`) corresponds to `origin/main` on GitHub. After this, you can just type `git push` without specifying the remote and branch name every time
- `origin` -- the remote to push to
- `main` -- the branch to push

```
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Writing objects: 100% (6/6), 542 bytes | 542.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To https://github.com/arjun/portfolio.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.
```

Go to your GitHub repository in the browser. All the files are there, and the commit history is visible. Arjun's portfolio is now on the internet.

For every subsequent push after the first, the upstream is already set:

```bash
git push
```

That is all. Git knows where to push and which branch to use.

### Authentication: Proving You Are Who You Say You Are

When you push to GitHub, it needs to verify that you have permission to modify that repository. There are two ways to authenticate.

#### HTTPS with a Personal Access Token (PAT)

When using an HTTPS URL (like `https://github.com/...`), GitHub asks for your credentials. Since 2021, GitHub no longer accepts your account password here -- it requires a **Personal Access Token (PAT)**, which is a generated string that acts as a password specifically for Git operations.

**Generating a PAT:**

1. Go to **github.com** and click your profile picture (top-right)
2. Select **Settings**
3. Scroll down the left sidebar and click **Developer settings**
4. Click **Personal access tokens** then **Tokens (classic)**
5. Click **Generate new token**
6. Give it a descriptive name ("laptop git access"), set an expiry date, and check the **repo** scope (gives access to your repositories)
7. Click **Generate token**
8. Copy the token immediately -- GitHub only shows it once

When Git asks for your password during `git push`, paste the token instead of your account password. Most systems will remember it after the first time.

#### SSH with a Key Pair

SSH is an alternative authentication method that does not require typing credentials on every push. You generate a key pair on your machine (a public key and a private key), add the public key to your GitHub account, and Git uses the private key to authenticate silently.

SSH uses a different URL format: `git@github.com:yourusername/portfolio.git` instead of `https://github.com/...`.

SSH setup is a one-time process per machine. It is the preferred method for frequent Git users. The full setup steps are on GitHub's documentation under "Connecting to GitHub with SSH."

For getting started quickly, HTTPS with a PAT is simpler. For regular use on your own machine, SSH is more convenient.

### Pulling Changes from GitHub: git pull

`git push` sends your commits up. `git pull` brings changes down.

This becomes relevant in two situations: you made a change on GitHub directly (through the web editor, which GitHub supports), or someone else pushed changes to the repository that you do not have locally.

```bash
git pull
```

`git pull` is actually two operations in one:
1. `git fetch` -- downloads the remote state to your local machine but does not change your files
2. `git merge` -- integrates the downloaded changes into your current branch

For most solo workflows, `git pull` is what you use. In team contexts, some developers prefer running `git fetch` first to inspect what is coming before merging.

**Real-world scenario:** Arjun pushed his portfolio to GitHub on his laptop. The next day at college, he opens the GitHub repository on his phone and uses the web editor to fix a typo in the README. When he is back on his laptop, his local copy does not have that fix. He runs:

```bash
git pull
```

```
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
Unpacking objects: 100% (5/5), done.
From https://github.com/arjun/portfolio
   b7d4e9f..c2a1f38  main -> origin/main
Updating b7d4e9f..c2a1f38
Fast-forward
 README.md | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

The README change is now on his laptop. Both copies are in sync.

### Cloning: Starting from GitHub on a New Machine

If you are on a machine that does not have the repository at all, `git clone` downloads the entire thing -- all files and the complete history -- and sets up the remote automatically.

```bash
git clone https://github.com/arjun/portfolio.git
```

This creates a `portfolio` folder in your current directory, copies all files into it, initialises a Git repository, and configures `origin` to point to the GitHub URL. Everything is ready to use immediately -- no need for `git init` or `git remote add`.

`git clone` is how you get started with any existing repository: someone else's open source project, a project from your team, or your own project on a new computer.

### The README: Your Repository's Front Page

Every repository on GitHub has a **README.md** file that appears on the repository's main page. It is the first thing anyone sees when they visit your project -- the description, the instructions, the context.

```markdown
# Portfolio Website

A personal portfolio site built with HTML, CSS, and JavaScript.

## What it does
Showcases projects, skills, and contact information. Includes a dark mode toggle and responsive layout for mobile and desktop.

## Technologies used
- HTML5
- CSS3 (Flexbox and Grid)
- Vanilla JavaScript

## How to run locally
1. Clone the repository: `git clone https://github.com/arjun/portfolio.git`
2. Open `index.html` in a browser

## Live demo
[View live site](https://arjun.github.io/portfolio)
```

A good README answers four questions: what is the project, what does it do, how do I run it, and where can I see it live. It uses Markdown for formatting -- the same syntax used throughout this course's articles.

Add and commit the README:

```bash
git add README.md
git commit -m "docs: add README with project overview"
git push
```

The README now appears on the GitHub repository page.

### The Everyday Remote Workflow

Once the repository is connected to GitHub, the daily pattern extends what you learned in the previous article:

```
1. git pull                     (get any changes from GitHub before starting)
2. Make changes to files
3. git add / git commit         (save snapshots as you work)
4. git push                     (send commits to GitHub at the end of a session)
```

Pull before you start. Push when you are done. Everything in between is the same local workflow from the previous article.

### Let's Define Things Now...

> **Remote:** A version of the repository hosted on a server (like GitHub). The local repository pushes to and pulls from the remote to stay in sync.

> **origin:** The conventional name for the primary remote. Set with `git remote add origin url`.

> **git remote add origin url:** Links the local repository to a GitHub repository.

> **git push -u origin main:** Pushes local commits to GitHub for the first time. The `-u` flag sets the upstream so future pushes only need `git push`.

> **git push:** Sends local commits to the remote repository (after upstream is set).

> **git pull:** Downloads and integrates remote changes into the local branch. Combines `git fetch` and `git merge`.

> **git fetch:** Downloads the remote state without changing local files. Inspect before merging.

> **git clone url:** Copies an existing remote repository to a new local machine, including all files, history, and remote configuration.

> **Personal Access Token (PAT):** A token generated in GitHub settings used instead of a password for HTTPS authentication.

> **README.md:** A Markdown file at the root of a repository that appears on the GitHub repository page. Documents what the project is, how to run it, and where to see it live.

### Activity: Remote Workflow

1. Create a GitHub account if you do not have one.

2. Create a new empty repository on GitHub called `portfolio`. Do not initialise with a README.

3. In the local `git-practice` repository from the previous article, add the GitHub repository as a remote with `git remote add origin <url>`.

4. Push all local commits to GitHub with `git push -u origin main`. Verify the files appear on GitHub in the browser.

5. On GitHub, use the web editor to edit the README directly (or create one if it does not exist). Save the change as a commit on GitHub.

6. On your local machine, run `git pull` and confirm the change appears in the local file.

7. Clone the same repository into a different folder (`git clone <url> portfolio-clone`) and verify all files and history are present.

8. Write a README.md for the portfolio project with the four sections covered in this article. Commit and push it.

## What's Coming Next...

You now have the full solo developer workflow: local Git for tracking history, GitHub for backup and sharing, and the push/pull cycle for keeping both in sync. Arjun's portfolio is safe, accessible from any machine, and shareable with a single URL.

The next article introduces one more concept that matters even when you are working alone: branching. Right now, all commits go directly onto the `main` branch -- the primary timeline of the project. Branching lets you create a separate line of development where you can try a new feature, experiment freely, and only bring the work back to `main` when it is ready. It is the difference between building a house and drawing a blueprint first.
