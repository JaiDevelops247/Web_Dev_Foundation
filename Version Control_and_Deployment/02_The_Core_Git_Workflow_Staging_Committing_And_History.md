## Introduction

In the previous article, we established why version control matters and what Git is: a tool that tracks every change to your project, lets you name and save snapshots of your work, and gives you the ability to navigate that history at any point. We covered the three-area model -- working directory, staging area, repository -- and the core terminology.

Now we put that into practice. This article is a hands-on walkthrough of the Git commands you will use every single day. Follow along by opening your terminal and running each command as we go. By the end, you will have a real repository with a real commit history, and the workflow will feel familiar enough to use on your own projects immediately.

We will continue with Arjun's portfolio project from the previous article -- the one that had `index_FINAL_v2.html` and a folder full of unnamed backups. This time, he does it right.

### Starting Fresh: git init

The first thing Arjun does is create a clean project folder and tell Git to start tracking it.

```bash
mkdir portfolio
cd portfolio
```

This creates a folder called `portfolio` and moves into it. Now initialise a Git repository inside it:

```bash
git init
```

```
Initialized empty Git repository in /Users/arjun/portfolio/.git/
```

Git created a hidden `.git` folder inside `portfolio`. This is Git's database -- it is where all the version history will live. You never need to open or edit `.git` manually. Just know it is there and that deleting it would destroy the entire history.

The project folder is now a repository. Nothing is tracked yet -- Git is watching but has not seen any files.

### Checking What Git Sees: git status

Before adding any files, let us check what Git currently knows about the project:

```bash
git status
```

```
On branch main

No commits yet

nothing to commit (create/copy files first)
```

Now create the first two files:

```bash
touch index.html style.css
```

Run `git status` again:

```bash
git status
```

```
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        index.html
        style.css

nothing added to commit but untracked files present
```

Git sees the files but is not tracking them yet. **Untracked** means Git knows the file exists but has never been told to include it in any commit. It will not appear in the history until you explicitly add it.

`git status` is the command you will run most often. Any time you are unsure of what is staged, what is modified, or what is untracked -- run `git status`. It always gives you a clear picture of where things stand.

### Moving Files to the Staging Area: git add

Arjun opens `index.html` and adds the basic HTML structure. He also adds some CSS to `style.css`. Now he wants to save the first version of his project. The first step is staging the files.

**Stage a specific file:**

```bash
git add index.html
```

**Stage all changed files at once:**

```bash
git add .
```

The `.` means "everything in the current directory and all subdirectories." Use it when you want to stage everything that has changed.

Let us stage both files and check status:

```bash
git add index.html style.css
git status
```

```
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file: index.html
        new file: style.css
```

Both files are now in the staging area -- "Changes to be committed." They are ready for the next commit.

**Why stage as a separate step?**

Imagine Arjun spent the morning fixing a broken link in the navigation and also started experimenting with a new font. Both changes are in `index.html`. He wants to commit the bug fix with a clear message but is not ready to commit the font experiment yet.

Without staging, he would have to commit both together or neither. With staging, he can carefully select only the lines or files that belong to the bug fix, commit those with the message "Fix broken nav link", and leave the font changes unstaged for a separate commit later.

Every commit should represent one logical unit of work. Staging is what gives you that control.

### Saving a Snapshot: git commit

With the files staged, create the first commit:

```bash
git commit -m "Initial commit: add HTML structure and base CSS"
```

```
[main (root-commit) a3f8c21] Initial commit: add HTML structure and base CSS
 2 files changed, 47 insertions(+)
 create mode 100644 index.html
 create mode 100644 style.css
```

The `-m` flag lets you write the commit message inline. The message should describe what this snapshot represents -- not what you did (Git already records that), but what the change accomplishes. Write it in the imperative mood: "Add navigation", "Fix mobile layout", "Remove unused CSS" -- not "Added", "Fixed", "Removed".

**What if you omit -m?**

```bash
git commit
```

Git opens your default text editor (usually Vim or Nano) for you to type a longer message. The first line is the subject. After a blank line, you can add a longer description. This is used for complex commits that need more explanation. For most day-to-day commits, `-m` with a short message is sufficient.

To close Vim if it opens unexpectedly: press `Esc`, type `:wq`, press `Enter`.

### Making More Changes and Another Commit

Arjun keeps working. He adds a navigation bar to `index.html`, creates a new `script.js` file, and updates the CSS. Let us simulate this:

```bash
git status
```

```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified: index.html
        modified: style.css

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        script.js
```

`index.html` and `style.css` are **modified** -- Git is tracking them (they were in the previous commit) and it can see they have changed. `script.js` is **untracked** -- it is new and has never been committed.

Stage everything and commit:

```bash
git add .
git commit -m "Add navigation bar and link JavaScript file"
```

Now the repository has two commits.

### Reading the History: git log

```bash
git log
```

```
commit b7d4e9f (HEAD -> main)
Author: Arjun Sharma <arjun@example.com>
Date:   Mon Jun 6 11:42:00 2026 +0530

    Add navigation bar and link JavaScript file

commit a3f8c21
Author: Arjun Sharma <arjun@example.com>
Date:   Mon Jun 6 10:15:00 2026 +0530

    Initial commit: add HTML structure and base CSS
```

Each commit shows its SHA hash, author, date, and message. The most recent commit is at the top. `HEAD -> main` means your working directory currently reflects the latest commit on the `main` branch.

For a compact, one-line view:

```bash
git log --oneline
```

```
b7d4e9f (HEAD -> main) Add navigation bar and link JavaScript file
a3f8c21 Initial commit: add HTML structure and base CSS
```

This is the view you will use most often. Clean, readable, and shows the sequence clearly.

<!-- 
IMAGE: A terminal screenshot showing the output of `git log --oneline` with five commits listed, each showing a short hash and a commit message. The most recent commit at the top is labeled "(HEAD -> main)". Caption: "git log --oneline shows the full commit history in a compact format. The newest commit is always at the top." Style: realistic dark terminal screenshot with monospace font.
-->

### Seeing What Changed: git diff

Before staging and committing, it is useful to review exactly what you changed. Two commands handle this.

**See changes in your working directory that are not yet staged:**

```bash
git diff
```

This shows a line-by-line comparison between your current files and their last committed state. Lines beginning with `+` were added, lines beginning with `-` were removed.

```
diff --git a/index.html b/index.html
index 3a1f820..6b2c941 100644
--- a/index.html
+++ b/index.html
@@ -8,6 +8,9 @@
   <nav>
-    <a href="about.html">About</a>
+    <a href="about.html">About Me</a>
+    <a href="contact.html">Contact</a>
   </nav>
```

**See changes that are already staged but not yet committed:**

```bash
git diff --staged
```

This shows what is in the staging area -- what would go into the commit if you ran `git commit` right now.

Use `git diff` before `git add` to review your changes. Use `git diff --staged` after `git add` to confirm what is about to be committed. This habit prevents accidentally committing debugging code, incomplete changes, or things you did not mean to include.

### Undoing Changes Safely: git restore

Two scenarios come up regularly when you want to undo something.

**Scenario 1: You changed a file but have not staged it yet, and you want to discard the change entirely.**

```bash
git restore index.html
```

This reverts `index.html` back to the state it was in at the last commit. **This is destructive -- the unsaved changes are gone.** There is no trash bin for this. Only run it when you are certain you do not want the changes.

**Scenario 2: You staged a file with `git add`, but you changed your mind and want to unstage it without losing the changes in the file.**

```bash
git restore --staged index.html
```

This removes the file from the staging area but leaves your edits intact. The file goes back to "modified but not staged." Your work is not lost -- it just will not be included in the next commit.

```
Before:  Working directory (modified) --> Staging area (staged) --> Repository
After:   Working directory (modified)     [removed from staging]    Repository
```

A quick way to remember the difference: `git restore` affects the working directory. `git restore --staged` affects the staging area.

### Telling Git What to Ignore: .gitignore

Not every file in a project should be committed. Some files are generated automatically and do not need to be tracked. Some files contain sensitive information that should never be in version history. Some files are specific to your editor or operating system and have no meaning to anyone else.

`.gitignore` is a plain text file in the root of your repository that lists file patterns Git should never track.

Create it:

```bash
touch .gitignore
```

Open it and add patterns:

```
# Mac OS system file -- no use to anyone else
.DS_Store

# Environment variables -- contains secrets, never commit this
.env

# Editor configuration -- specific to your setup
.vscode/

# Dependencies folder -- can be recreated from package.json
node_modules/

# Build output -- generated files, not source
dist/

# Any file ending in .log
*.log
```

**Glob pattern reference:**

| Pattern | What it matches |
|---|---|
| `*.log` | Any file ending in `.log` in any folder |
| `node_modules/` | The `node_modules` folder and everything inside it |
| `.env` | Exactly the file named `.env` |
| `build/` | The `build` folder and everything inside it |
| `!important.log` | Un-ignores `important.log` even if `*.log` is listed |

The `!` prefix overrides a previous pattern. If you have `*.log` but one specific log file should be tracked, `!deployment.log` keeps it.

Commit `.gitignore` itself -- it is part of the project and should be tracked:

```bash
git add .gitignore
git commit -m "Add .gitignore for editor files and secrets"
```

**A critical warning about secrets:** If you commit a file containing an API key, password, or database credential -- even if you delete it in the next commit -- it is still visible in the history. Anyone who clones the repository can find it. Adding a file to `.gitignore` does not remove it from history if it was already committed. The correct practice is to never commit secrets in the first place. Use `.gitignore` for `.env` files from the very first commit.

### The Everyday Workflow

By now, the pattern for daily Git usage is clear:

```
1. Make changes to your files
2. git status          (see what changed)
3. git diff            (review the changes in detail)
4. git add <files>     (stage the changes you want in this commit)
5. git diff --staged   (confirm what is staged)
6. git commit -m "..." (save the snapshot with a clear message)
7. git log --oneline   (verify the commit was created)
```

Repeat this cycle as you work. Small, frequent commits are better than large, infrequent ones. A commit with the message "Add search functionality to the navigation bar" is easier to understand and safer to revert than one that says "Various updates".

<!-- 
IMAGE: A circular flow diagram showing the everyday Git cycle. Five steps arranged in a circle: 1. "Edit files" -> 2. "git status" -> 3. "git add" -> 4. "git commit" -> 5. "git log" -> back to 1. Each step has a short description below it. Caption: "The Git workflow is a loop. Each iteration produces one commit that represents one logical unit of work." Style: flat vector circular diagram with numbered steps and short labels.
-->

### Let's Define Things Now...

> **git init:** Initialises a new Git repository in the current folder. Creates the hidden `.git` directory.

> **git status:** Shows the current state of the working directory and staging area: untracked files, modified files, and staged changes.

> **git add filename:** Moves a specific file's changes to the staging area. `git add .` stages all changes in the current directory.

> **git commit -m "message":** Creates a permanent snapshot from whatever is currently staged. The message describes what the snapshot represents.

> **git log:** Shows the full commit history. `git log --oneline` shows one line per commit.

> **git diff:** Shows unstaged changes in the working directory. `git diff --staged` shows staged changes not yet committed.

> **git restore filename:** Discards unstaged changes in a file, reverting it to the last committed state. Destructive -- changes are lost.

> **git restore --staged filename:** Removes a file from the staging area without discarding the working directory changes.

> **.gitignore:** A file listing patterns of files Git should never track. Essential for excluding secrets, generated files, and OS-specific files.

### Activity: Git Workflow Drill

1. Create a new folder called `git-practice` and initialise a repository inside it.

2. Create three files: `index.html`, `style.css`, and `notes.txt`. Run `git status` and identify which category each file is in.

3. Stage only `index.html` and `style.css` (not `notes.txt`). Confirm with `git status`. Commit with a clear message.

4. Add some content to both files and create a new `script.js`. Stage and commit all three with a second message.

5. Make a change to `index.html`. Run `git diff` and read the output. Then run `git restore index.html` and confirm the change is gone.

6. Make another change to `style.css`. Stage it with `git add`. Then run `git restore --staged style.css` and confirm it is back to untracked but the change is still in the file.

7. Create a `.gitignore` that excludes `notes.txt` and any file ending in `.log`. Run `git status` and confirm `notes.txt` no longer appears as untracked.

8. Run `git log --oneline` and read your commit history. Do the messages clearly describe what each snapshot contains?

## What's Coming Next...

You now have a fully functional local Git workflow. Arjun can track every version of his portfolio, commit changes with meaningful messages, review exactly what changed before each commit, and undo specific changes without losing anything else. His project folder has two files instead of twelve.

But everything still lives on his machine. If his laptop breaks, the history is gone. If he wants to continue working from a different computer, he cannot. If he wants to share the project with a client or collaborator, there is no way to do that yet.

That is what GitHub is for. In the next article, we connect the local repository to the internet, push the history to a remote server, and unlock the ability to access, share, and collaborate on the project from anywhere.
