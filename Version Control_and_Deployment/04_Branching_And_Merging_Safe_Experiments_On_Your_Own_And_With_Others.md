## Introduction

Arjun's portfolio is live on GitHub. His commit history is clean, his push/pull workflow is solid, and he can access his project from any machine. Things are going well.

Then he gets an idea. He wants to try a dark color scheme for the whole site -- dark background, light text, muted accent colors. He saw it on another developer's portfolio and thinks it would look better. But his current portfolio is already shared with a few internship recruiters who might check it this week.

If he starts making changes directly to `main`, one of two things happens. Either the experiment goes smoothly and ends up fine, or something breaks and the version the recruiters see is half-finished and broken. Both outcomes are uncertain, and Arjun will not know which one he is heading toward until he is already there.

This is the problem that **branching** solves -- and it is a problem every developer runs into, even when working completely alone.

### The Concept: A Separate Line of Development

Right now, Arjun's entire project history is one straight line. Every commit follows the last one on `main`. Branching lets him diverge from that line at a specific point and start a new, parallel line of development.

<!-- 
IMAGE: A horizontal commit history showing three commits on main (C1, C2, C3). At C3, a new line branches off and shows two more commits labeled C4 and C5 on a separate branch called "feature/dark-mode". The main line continues straight, while the branch line forks off diagonally above it. Caption: "A branch is a separate line of development. Commits on the branch do not appear on main until you decide to bring them in." Style: flat vector commit graph with two colored lines diverging from a common point.
-->

The key property of a branch: **commits made on a branch do not appear on other branches.** Arjun can make ten commits on `feature/dark-mode` -- trying colors, breaking things, fixing them -- and `main` stays exactly as it was when the branch was created. The recruiters see the unchanged portfolio. The experiment is fully isolated.

When the dark mode is ready and Arjun is happy with it, he **merges** the branch back into `main`. The work joins the main timeline. If the experiment fails entirely, he deletes the branch and nothing about `main` ever changed.

### Seeing Your Branches: git branch

Before creating anything, check what branches already exist in the repository:

```bash
git branch
```

```
* main
```

The `*` next to `main` shows which branch is currently active -- the one HEAD is pointing to. Right now there is only one branch.

### Creating a Branch: git branch branchname

To create a new branch:

```bash
git branch feature/dark-mode
```

This creates the branch at the current commit. It does not switch to it -- you are still on `main`. Run `git branch` again to confirm:

```bash
git branch
```

```
  feature/dark-mode
* main
```

Both branches exist now. The `*` is still on `main`.

### Moving to a Branch: git switch

Creating a branch and switching to it are two separate operations. To move to a branch:

```bash
git switch feature/dark-mode
```

```
Switched to branch 'feature/dark-mode'
```

Run `git branch` again:

```bash
git branch
```

```
* feature/dark-mode
  main
```

The `*` has moved. You are now on `feature/dark-mode`. Any commits you make from this point will be recorded on this branch, not on `main`.

### Creating and Switching in One Step: git switch -c

Since creating a branch and immediately switching to it is such a common pattern, there is a shortcut:

```bash
git switch -c feature/dark-mode
```

The `-c` flag means "create". This is equivalent to running `git branch feature/dark-mode` followed by `git switch feature/dark-mode` -- but in one command. Most developers use this form rather than the two-step version.

### HEAD and Branches

In the first article, HEAD was described as a pointer to the current commit your working directory reflects. Branching adds more precision to this.

**HEAD does not point directly to a commit. It points to a branch. The branch points to a commit.**

When you are on `main`, HEAD points to `main`, which points to the latest commit on `main`. When you switch to `feature/dark-mode`, HEAD points to `feature/dark-mode` instead.

<!-- 
IMAGE: Three diagrams side by side. Left: HEAD -> main -> C3 (latest commit on main). Middle: git switch feature/dark-mode runs. Right: HEAD -> feature/dark-mode -> C3 (same commit, but branch pointer changed). Caption: "HEAD always points to the current branch. Switching branches moves HEAD, not the commits." Style: flat vector pointer diagram with labeled arrows from HEAD to branch name to commit circle.
-->

When you make a new commit while on `feature/dark-mode`:

```
HEAD -> feature/dark-mode -> C4
                 main -> C3
```

The branch pointer moves forward with each new commit. `main` stays at C3. The two lines are now diverging.

### Making Commits on a Branch

Arjun switches to `feature/dark-mode` and starts working. He updates the CSS custom properties to dark values:

```bash
git switch -c feature/dark-mode
```

He edits `style.css`, changes the color scheme, and commits:

```bash
git add style.css
git commit -m "style: switch color palette to dark theme"
```

Then he adjusts the text contrast:

```bash
git add style.css
git commit -m "style: improve text contrast for dark background"
```

Now switch back to `main` and look at `style.css`:

```bash
git switch main
```

The file is unchanged. It looks exactly as it did before the branch was created. The two commits Arjun just made exist only on `feature/dark-mode`. Main has no idea they happened.

This is the isolation that makes branching valuable. Arjun can share `main` with anyone while the experiment is safely contained in a separate line of history.

### Bringing the Work Back: git merge

After a few more commits, Arjun is happy with the dark mode. Now he wants to bring those changes into `main`. The operation that does this is **merge**.

The rule for merging: **switch to the branch you want to merge INTO, then run `git merge` with the branch you want to bring FROM.**

Arjun wants to bring `feature/dark-mode` into `main`:

```bash
git switch main
git merge feature/dark-mode
```

What Git does next depends on the state of both branches.

### The Fast-Forward Merge

If `main` has no new commits since `feature/dark-mode` was created -- meaning the two lines have not truly diverged -- Git performs a **fast-forward merge**.

<!-- 
IMAGE: A commit graph showing main at C3, then feature/dark-mode extending to C4 and C5. Before merge: main pointer is at C3. After merge: main pointer has moved forward to C5. The branch line and main line are now the same. Caption: "Fast-forward merge: when main has no new commits, Git simply moves the main pointer to the tip of the feature branch. No new commit is needed." Style: flat vector before-and-after commit graph showing pointer movement.
-->

```
Before:  main -> C3
                  \
                   C4 -> C5 (feature/dark-mode)

After:   main -> C5 (feature/dark-mode still points here too)
```

Git outputs:

```
Updating a3b9c12..f7d2e88
Fast-forward
 style.css | 18 ++++++++++++------
 1 file changed, 12 insertions(+), 6 deletions(-)
```

No new commit was created. Git simply moved the `main` pointer forward to where `feature/dark-mode` already was. The history is still a straight line.

Fast-forward is the most common outcome when a solo developer uses short-lived branches.

### The Three-Way Merge

If both branches have moved forward since they diverged -- meaning `main` has new commits that `feature/dark-mode` does not, and `feature/dark-mode` has commits that `main` does not -- Git cannot fast-forward. Instead it performs a **three-way merge**.

Git looks at three commits:
1. The common ancestor (the commit where the branch split off)
2. The tip of `main`
3. The tip of `feature/dark-mode`

It combines the changes from both lines and creates a new **merge commit** that has two parents.

<!-- 
IMAGE: A commit graph showing main at C3, then main moving to C4 (a new commit on main). feature/dark-mode also branching from C3 and moving to C5 and C6. After merge: a new commit C7 on main with two arrows pointing back to C4 and C6. Caption: "Three-way merge: when both branches have new commits, Git creates a merge commit with two parents, combining both lines of history." Style: flat vector commit graph with two converging lines meeting at a merge commit node.
-->

```bash
git merge feature/dark-mode
```

```
Merge made by the 'recursive' strategy.
 style.css | 18 ++++++++++++------
 1 file changed, 12 insertions(+), 6 deletions(-)
```

Git opens an editor for a merge commit message (usually something like `Merge branch 'feature/dark-mode'`). Save and close it, and the merge is complete. The history now has a visible fork and rejoin.

### Merge Conflicts

A fast-forward always succeeds. A three-way merge usually succeeds automatically too -- Git is smart about combining non-overlapping changes. But if **both branches modified the same lines of the same file**, Git cannot decide which version to keep. This is a **merge conflict**.

Imagine Arjun was also fixing a typo in `index.html` on `main` while the dark mode work was happening on the branch. And the branch also touched a line in `index.html` for a different reason. When he merges:

```bash
git merge feature/dark-mode
```

```
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

Open `index.html`. The conflicting section looks like this:

```
<<<<<<< HEAD
<title>Arjun | Portfolio</title>
=======
<title>Arjun's Portfolio</title>
>>>>>>> feature/dark-mode
```

The three markers tell you exactly what is happening:

- `<<<<<<< HEAD` -- everything between here and `=======` is what `main` has
- `=======` -- the divider between the two versions
- `>>>>>>> feature/dark-mode` -- everything between `=======` and here is what the branch has

**You resolve the conflict by editing the file to what you actually want.** Delete the markers, delete both versions if needed, and write the line the way it should be:

```html
<title>Arjun's Dark Portfolio</title>
```

Once the file looks right, stage it and commit:

```bash
git add index.html
git commit -m "merge: resolve title conflict from dark-mode branch"
```

The merge is now complete. Conflicts look alarming the first time, but they are just Git saying "I do not know which version to keep -- you decide." Once you edit the file and commit, everything proceeds normally.

### Deleting a Merged Branch

After merging, the branch has served its purpose. Its history is now part of `main`. The branch pointer itself is just a label -- keeping it around does not add anything.

Delete it:

```bash
git branch -d feature/dark-mode
```

```
Deleted branch feature/dark-mode (was f7d2e88).
```

The `-d` flag is safe: it refuses to delete a branch that has not been merged yet. If you want to delete an unmerged branch (discarding all its commits permanently), use `-D` instead -- but that is a destructive operation, so only do it intentionally.

### The Typical Solo Branching Workflow

Put it all together. Arjun's pattern for any new piece of work:

```
1. git switch -c feature/whatever     (create and switch to branch)
2. Make changes and commit freely
3. git switch main                    (go back to main)
4. git merge feature/whatever         (bring the work in)
5. git branch -d feature/whatever     (clean up)
6. git push                           (send to GitHub)
```

This keeps `main` clean and stable at all times. Experiments never risk the working version. And because branches are cheap to create and delete -- they are just pointers -- there is no reason not to use one for every meaningful piece of work.

### Branching in a Team: Where This Scales

Everything covered so far is solo workflow. But branching is also the foundation of how teams collaborate without stepping on each other.

In a team setting, everyone works on the same remote repository on GitHub. If every developer committed directly to `main`, two things would happen constantly: commits from different people would mix together with no clear ownership, and one developer's half-finished work would land in `main` before it is ready.

The team equivalent of Arjun's workflow looks like this: each developer creates a branch for their task (say `feature/login-page` or `fix/broken-search`), works there independently, and when the work is ready, opens a **pull request** on GitHub. A pull request is a formal proposal to merge a branch into `main`. Other developers review the code, leave comments, and approve it before the merge happens.

<!-- 
IMAGE: A GitHub pull request page showing a branch called "feature/dark-mode", a list of commits from that branch, and a "Merge pull request" button at the bottom. A comments section shows one reviewer's comment: "Looks good, contrast ratio is fine." Caption: "A pull request on GitHub is a proposal to merge a branch. Team members can review and discuss the changes before they reach main." Style: realistic browser screenshot of a GitHub pull request page.
-->

The branch workflow is identical -- create branch, commit, merge. The only addition is the review step in between. This means every line of code that reaches `main` has been seen by at least one other person. No one accidentally ships a half-finished feature or a bug-introducing change to the shared main branch.

This is the model used by virtually every professional development team, from two-person startups to engineering teams of thousands. The mechanics you just learned -- branches, merges, fast-forward, conflict resolution -- are the same mechanics operating at that scale.

### Let's Define Things Now...

> **Branch:** A separate line of development in a Git repository. A lightweight pointer that moves forward with each new commit on that line.

> **git branch:** Lists all branches in the repository. The `*` marks the currently active branch.

> **git branch branchname:** Creates a new branch at the current commit without switching to it.

> **git switch branchname:** Moves to an existing branch (changes HEAD to point to it).

> **git switch -c branchname:** Creates a new branch and immediately switches to it. The most common way to start a branch.

> **HEAD (in the context of branches):** A pointer to the current branch. The branch points to the latest commit on that line. When you commit, the branch pointer moves forward and HEAD follows.

> **git merge branchname:** Integrates the history of another branch into the current branch.

> **Fast-forward merge:** When the target branch (main) has no new commits since the feature branch was created, Git moves the main pointer forward to the tip of the feature branch. No new commit is created.

> **Three-way merge:** When both branches have diverged (each has commits the other does not), Git finds the common ancestor and creates a new merge commit combining both lines of history.

> **Merge conflict:** When both branches modified the same lines of the same file, Git cannot choose automatically. It marks the conflict with `<<<`, `===`, `>>>` markers and waits for the developer to resolve it manually.

> **git branch -d branchname:** Deletes a merged branch. Safe -- Git refuses if the branch has unmerged commits.

> **Pull request:** A GitHub feature (not a Git command) that is a formal proposal to merge a branch. Used in team workflows to request review before merging to main.

### Activity: Branching Drill

Set up the scenario on your local `portfolio` repository from the previous articles:

1. Make sure you are on `main` and everything is committed and pushed.

2. Create and switch to a branch called `feature/dark-mode`:
   ```bash
   git switch -c feature/dark-mode
   ```

3. In `style.css`, change the background color to `#1a1a1a` and the text color to `#f0f0f0`. Stage and commit:
   ```bash
   git add style.css
   git commit -m "style: switch to dark color scheme"
   ```

4. Make one more commit on the branch -- change the link color to `#7eb8f7`. Stage and commit.

5. Switch back to `main`:
   ```bash
   git switch main
   ```

   Open `style.css`. Confirm the dark colors are not there. Main is untouched.

6. Merge the branch into main:
   ```bash
   git merge feature/dark-mode
   ```

   Note whether Git performed a fast-forward or a three-way merge, and why.

7. Delete the branch:
   ```bash
   git branch -d feature/dark-mode
   ```

8. **Bonus -- create a conflict:**
   - Create a new branch: `git switch -c fix/typo`
   - In `index.html`, change the page title to "Arjun | Web Dev"
   - Commit on the branch
   - Switch back to `main`
   - On `main`, change the same title line to "Arjun | Portfolio" and commit
   - Now merge `fix/typo` into `main`: `git merge fix/typo`
   - Resolve the conflict, stage, and commit

## What's Coming Next...

Arjun now has a complete, professional solo developer workflow. He can track his history, push to GitHub, and use branches to isolate any experiment without ever touching the working version of his project. This is the workflow used by developers the world over -- and it works entirely on its own.

But the moment a second developer enters the picture, one question comes up immediately: how does someone else propose changes to your repository without just being handed full push access? And how do you review, discuss, and accept or reject those changes in a controlled way?

The next article answers that. We look at **pull requests** -- the mechanism GitHub provides for proposing a branch merge, having it reviewed, and merging it only when it is approved. We also cover **forking**, which is how you contribute to a project you do not own. Together, these two concepts are how all open source collaboration happens, and how most professional teams manage code review.
