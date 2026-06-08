## Introduction

Meet Arjun. He has been building his portfolio website for three weeks. The project folder on his desktop looks like this:

```
portfolio/
  index.html
  index_backup.html
  index_new.html
  index_FINAL.html
  index_FINAL_v2.html
  index_ACTUALLY_FINAL.html
  style.css
  style_old.css
  style_dark_experiment.css
```

He wanted to try a dark color scheme last Tuesday, so he copied `style.css` into `style_dark_experiment.css` and made changes there. Then he wanted to rearrange the homepage layout, so he saved a backup of `index.html` as `index_backup.html` before editing. At some point he renamed things. Now he is not sure which file is the current one, which backup is from when, or what exactly changed between any two of them.

Yesterday he made a change to the navigation that broke the mobile layout. He wants to undo just that one change. But he cannot -- he has no record of what the navigation looked like before he touched it, or when the change was made. His only option is to compare files manually and try to remember.

This is a problem of **versions**. Every time Arjun saved a backup, he was trying to preserve a version of the project -- a snapshot of what it looked like at that point in time. But he was doing it manually, without any system, and the result is a folder full of ambiguously named files with no timeline and no way to navigate between them.

### The Problems Without Version Control

Arjun's situation is not unique. Without a proper system, every developer working on any project of meaningful size runs into the same set of problems:

**No reliable history.** Files named `_FINAL` and `_FINAL_v2` tell you nothing about what changed or when. There is no record of the sequence of decisions made along the way.

**No safe way to experiment.** Trying something new means either risking the working version or manually duplicating files. Most developers end up doing one or the other and making a mess of both.

**No targeted undo.** If something breaks, rolling back means reverting to an old file entirely -- losing every other change made since that backup was taken. There is no way to undo one specific change while keeping everything else.

**No protection against loss.** If the laptop is stolen, damaged, or reset, the project is gone entirely. There is no external copy that reflects the full history.

**Collaboration is nearly impossible.** Two people cannot work on the same file at the same time without one overwriting the other's work. Emailing zip files back and forth is the only option, and merging changes manually is error-prone and slow.

Version control solves all of these problems with a single, systematic approach: instead of saving files manually at arbitrary points, a version control system tracks every change automatically, records who made it and when, and gives you the tools to navigate, compare, and restore any point in that history.

### What Version Control Is

**Definition:** A version control system is a tool that records every change made to a set of files over time, so that any earlier state of the project can be recovered at any point.

Think of it like the revision history in a Google Doc, but for your entire project folder, and with much finer control over what gets saved and when.

Each saved state is called a **version** -- or in the language of most version control tools, a **commit**. Instead of `index_FINAL_v2.html`, you have a project with a clean history of named, timestamped states: "Added navigation", "Fixed mobile layout", "Added contact form". You can jump to any of them, compare any two, and undo any specific change without disturbing anything else.

### Introducing Git

**Git** is the most widely used version control system in the world. It was created in 2005 by Linus Torvalds (the same person who created the Linux operating system) and is now the standard tool used by individual developers and large engineering teams alike -- at companies like Google, Microsoft, and Meta, and in virtually every open source project.

Git is a **distributed** version control system. This means every developer who works on a project has a complete copy of the entire history of that project on their own machine -- not just the current files, but every version ever committed. You do not need an internet connection to view history, create versions, or experiment with branches. The full timeline lives locally.

### Getting Git on Your Machine

Git is a command-line tool. Before anything else, check whether it is already installed:

```bash
git --version
```

If you see something like `git version 2.x.x`, Git is already installed. If you get a "command not found" error:

**On Mac:** Install Xcode Command Line Tools, which includes Git:

```bash
xcode-select --install
```

Alternatively, install Git directly from [git-scm.com](https://git-scm.com). The installer walks you through the process.

**On Windows:** Download the installer from [git-scm.com](https://git-scm.com). During installation, the default options are fine. This also installs Git Bash -- a terminal environment that lets you run Git commands on Windows the same way you would on Mac or Linux.

After installation, confirm it worked:

```bash
git --version
```

Then set your identity. Git attaches your name and email to every commit you make, so there is always a record of who made each change:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

This is a one-time setup per machine.

### The Core Terminology

Before using any Git commands, it helps to have a clear picture of the vocabulary. These are the terms you will see everywhere.

**Repository (repo)**

A repository is a folder that Git is tracking. It contains your project files plus a hidden folder called `.git` that stores the entire version history. The `.git` folder is Git's database -- everything about the project's past lives there.

When someone says "clone the repo" or "push to the repo", they mean this tracked folder and its history.

**Commit**

A commit is a named snapshot of the project at a specific moment. Each commit has:
- A unique identifier (a long hash like `a3f8c21`)
- A message describing what changed ("Add navigation bar")
- A timestamp and the author's name
- A record of exactly what the project looked like at that point

Commits are the core unit of Git. The history of a project is a sequence of commits.

**Working Directory**

The actual files on your disk -- the files you open in your code editor and modify. This is what you see in your project folder.

**Staging Area (Index)**

A preparation zone between your working directory and the repository. When you make changes to files, they start in the working directory. You explicitly choose which changes to include in the next commit by moving them to the staging area first. Only what is in the staging area goes into the commit.

This exists so you can work on multiple things at once but commit them separately. If you fixed a bug and also updated the navigation in the same editing session, you can stage and commit them as two separate commits rather than bundling them together.

**HEAD**

A pointer that tells Git which commit your working directory currently reflects. In everyday use, HEAD points to the most recent commit on the branch you are working on. When you make a new commit, HEAD moves forward to point at it.

### Git's Three-Area Model

Everything in Git flows through three areas:

```
Working Directory  -->  Staging Area  -->  Repository
   (your files)       (changes ready       (committed
                        to commit)           history)
```

<!-- 
IMAGE: A horizontal three-box diagram. Box 1 on the left labeled "Working Directory" with an icon of files/code. An arrow labeled "git add" points right to Box 2 labeled "Staging Area (Index)". An arrow labeled "git commit" points right to Box 3 labeled "Repository (.git)". Below each box: "Your actual files", "Changes prepared for the next commit", "Permanent version history". Caption: "Git's three-area model. Files flow from left to right. git add moves changes to staging; git commit saves them to history." Style: flat vector three-box diagram with labeled arrows.
-->

1. You edit files in the **working directory**.
2. You choose which changes to prepare with `git add` -- they move to the **staging area**.
3. You create a permanent snapshot with `git commit` -- the staged changes are saved to the **repository**.

The working directory is temporary. The repository is permanent. The staging area is the deliberate step in between that gives you control over what goes into each commit.

### How Git Works Internally (Brief)

Most version control systems store changes as diffs -- they record only what was added or removed between one version and the next, and reconstruct the file by replaying those diffs. Git takes a different approach.

**Git stores snapshots, not diffs.**

Each commit is a complete picture of every file in the project at that moment. If a file did not change between two commits, Git does not store it twice -- it uses a pointer to the same stored content from the previous commit. If it did change, Git stores the new version alongside the old one.

Git identifies content using a **SHA-1 hash** -- a 40-character string computed from the file's content. Two files with identical content produce the same hash, so Git can detect duplicates and share storage efficiently. This is also how Git guarantees integrity: if any file in the history is modified after the fact, its hash changes, and Git immediately knows something is wrong.

The result is a system that is fast (no need to replay a chain of diffs to reconstruct a file), space-efficient (unchanged files are not duplicated), and tamper-evident (every piece of history is cryptographically verified).

You do not need to think about this while using Git day to day. But understanding it explains why Git is both reliable and fast -- and why the commit history is trustworthy.

<!-- 
IMAGE: A commit history diagram showing three commits in a horizontal chain (C1, C2, C3) connected by arrows pointing left (each commit points to its parent). Each commit box contains a short hash, a message, and a small file manifest. Files that changed between commits are shown as new boxes; files that stayed the same are shown as pointers (arrows) to the same box from C1. Caption: "Each commit is a snapshot. Unchanged files are not duplicated -- the new commit points to the same stored content. Changed files get new entries." Style: flat vector commit chain diagram with shared file content shown as shared nodes.
-->

### Git vs GitHub: An Important Distinction

Git and GitHub are two different things. They are often mentioned together, but they serve different purposes.

**Git** is the version control tool. It runs on your machine. It tracks your history locally. It has no concept of the internet. Everything covered in this article happens entirely offline.

**GitHub** is a website -- a cloud hosting service for Git repositories. It stores a copy of your repository on its servers so it is accessible from any machine, shareable with others, and safe from local hardware failure. GitHub adds a web interface for browsing history, comparing commits, and managing team contributions on top of Git's core capabilities.

The relationship is this: Git is the engine, GitHub is the garage where you park the car and let others drive it. You can use Git without GitHub entirely. GitHub without Git does not exist.

In the next article, we look at GitHub: how to put your local repository on the internet, how to keep your local and remote copies in sync, and how this becomes the foundation for working with other people.

### Let's Define Things Now...

> **Version Control:** A system that records changes to files over time so that any earlier state can be recovered.

> **Git:** A distributed version control system. Every developer has a complete copy of the entire project history on their own machine.

> **Repository (repo):** A folder tracked by Git. Contains project files and the hidden `.git` directory that stores all version history.

> **Commit:** A named snapshot of the project at a specific moment. Has a unique hash, a message, a timestamp, and an author.

> **Working Directory:** The actual files on disk that you edit in your code editor.

> **Staging Area (Index):** A preparation zone where changes are gathered before being committed. You choose what goes in.

> **HEAD:** A pointer to the current commit your working directory reflects. Moves forward with each new commit.

> **SHA-1 Hash:** A 40-character identifier computed from file content. Git uses hashes to uniquely identify every commit and file, detect changes, and deduplicate unchanged content.

> **Snapshot model:** Git stores the complete state of all files at each commit, not just the differences. Unchanged files are referenced, not duplicated.

### Activity: Mental Model Check

Before touching the terminal, answer the following in writing to verify your understanding:

1. What is the difference between the working directory and the staging area? Why does the staging area exist as a separate step rather than committing directly from the working directory?

2. You modified five files this morning but only want to commit three of them right now. What do you do?

3. Why does Git store snapshots instead of diffs? What would be the disadvantage of the diff approach for a large project with hundreds of commits?

4. Arjun has `index_FINAL_v2.html` and `index_ACTUALLY_FINAL.html`. If he had been using Git, how would his project folder look different? What information would he have that he does not have now?

5. Open any public repository on GitHub (for example, the VS Code repository at github.com/microsoft/vscode). Find the commit history and read the last five commit messages. What changed in the most recent commit? How is this better than Arjun's file naming approach?

## What's Coming Next...

Everything in this article lives entirely on your local machine. Git tracks your history, you commit your versions, and you can navigate that history any time -- all without the internet.

But local-only has limits. The history is safe only as long as your machine is. You cannot share your work with anyone else. You cannot continue from a different computer. And if you ever want to collaborate, there is no shared place for everyone's work to live.

GitHub solves all three. In the next article, we look at how to take a local Git repository and publish it to GitHub, how to keep your local and remote copies in sync, and what this unlocks for working with other developers.
