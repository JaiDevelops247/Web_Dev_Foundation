## Introduction

Arjun lands a short internship at a small startup. On his first day, his manager sends him a GitHub repository link and says: "Your first task is to add a loading spinner to the product page. Create a branch, make the change, and open a pull request."

Arjun knows branches. He creates one, writes the code, commits it. Then he stares at "open a pull request" and is not quite sure what that means or why the manager did not just say "merge it into main."

Later that week, he is using an open source JavaScript library for his personal project. He finds a bug in it and thinks he knows how to fix it. He looks up the contributing guide and it says: "Fork the repository, make your changes on a branch, and open a pull request from your fork."

Two separate situations -- a team codebase and a public open source project -- both arriving at the same concept: a **pull request**. This article is about understanding what that means, why teams work this way, and the vocabulary that surrounds it. You will not be implementing these workflows right now, but by the end of this article you will be able to read a team's contributing guide, a job description's required skills, or a project's documentation without needing to look up every second word.

### Why Teams Do Not Merge Directly

In the previous article, Arjun merged his own branch into `main` by himself. No one reviewed the code. No one saw what changed before it landed on the main branch. For a solo developer, that is perfectly fine -- he is the only stakeholder.

In a team, that changes. If every developer merges their own branches directly into `main`, a few things start going wrong quickly.

**No visibility.** Nobody sees what is about to land in `main` until it is already there. A logic error, an accidental deletion, or a half-finished feature goes live the moment the merge runs.

**No discussion point.** There is no formal moment where someone says "wait, this approach has a problem" before the code is in the shared branch.

**No accountability.** With no record of who reviewed what, it is hard to trace decisions or learn from mistakes.

Teams solve this with a rule: **no one merges their own branch into `main` directly.** Instead, they push the branch to GitHub and open a pull request -- a formal proposal for the merge. The code sits there, visible to the whole team, until it has been reviewed and approved.

### Pull Requests: The Review Checkpoint

A **pull request (PR)** is a GitHub feature that says: "I have work on a branch. I am requesting that it be pulled into another branch (usually `main`). Please look at it before it merges."

<!-- 
IMAGE: A GitHub pull request page. At the top, the branch name "feature/loading-spinner" and a title "Add loading spinner to product page". Below that, a tab row showing "Conversation", "Commits", "Checks", "Files changed". In the "Files changed" tab, a green-highlighted block of added lines and a comment from a reviewer on a specific line saying "Can we use a CSS animation here instead of GIF for better performance?" A green "Approve" button is visible. Caption: "A pull request on GitHub shows exactly what changed, line by line. Reviewers can comment on specific lines and approve or request changes before the merge happens." Style: realistic browser screenshot of a GitHub PR review page.
-->

When Arjun opens a pull request for his loading spinner:

- GitHub shows every commit on the branch and a line-by-line diff of every file changed
- His manager and teammates can leave comments on specific lines
- Someone might say "this GIF spinner will be slow on mobile -- use a CSS animation instead"
- Arjun addresses the feedback, makes a new commit on the same branch, and the PR updates automatically
- Once the reviewer is satisfied, they click **Approve**
- The branch merges into `main`

Nothing about the branch workflow changes. The PR is the conversation layer that happens between "push to GitHub" and "merge into main."

### The Pull Request Workflow Step by Step

Whether Arjun is working at a startup or contributing to an open source project, the flow is almost always the same:

```
1. Branch      -- create a branch for the specific piece of work
2. Commit      -- make changes and commit on the branch
3. Push        -- push the branch to GitHub
4. Open PR     -- go to GitHub, open a pull request from branch into main
5. Review      -- teammates review the code, leave comments
6. Address     -- make changes based on feedback, push new commits
7. Approve     -- a reviewer (or multiple) approves the PR
8. Merge       -- the PR is merged into main (often by the reviewer or team lead)
9. Delete      -- the branch is deleted after merge
```

The branch and commit steps are exactly what you already know. Steps 4 through 9 are the team coordination layer that GitHub provides on top of Git.

### Code Review: What Actually Happens During Review

When a teammate reviews Arjun's pull request, they are not just checking for bugs. A good code review looks at several things:

**Correctness.** Does the code actually do what the PR description says it does? Are there edge cases that are not handled? Does it break any existing functionality?

**Clarity.** Is the code readable? Would someone unfamiliar with this file understand what it is doing? Are variable names descriptive?

**Performance.** Is there a more efficient way to do this? Does this change add unnecessary network requests, heavy computation, or slow DOM operations?

**Security.** Are there any inputs that are not validated? Is any sensitive data being exposed?

**Consistency.** Does this follow the patterns and conventions already used in the codebase? A component written in a completely different style than the rest of the file creates maintenance problems later.

Good feedback in a review is specific and explains the reasoning. "Use a CSS animation here instead of a GIF -- CSS animations run on the GPU and will not block the main thread on lower-end devices" is useful feedback. "This is bad" is not.

Receiving feedback is a skill too. A comment on your code is not a comment on you. The goal of review is to make the code better before it reaches the shared branch -- not to evaluate the developer's worth. The right response to review feedback is to read it carefully, ask a clarifying question if it is unclear, and then address or discuss it. Most professional developers say that the review process is where they learned the most, faster than anywhere else.

### Forking: Contributing to a Repository You Do Not Own

When Arjun found a bug in that open source library, he cannot just create a branch on it -- he does not have push access to a repository that belongs to someone else.

This is where **forking** comes in.

A **fork** is a personal copy of someone else's repository, stored on your own GitHub account. It is fully independent -- changes you make to your fork do not affect the original, and the original's updates do not automatically appear in your fork.

<!-- 
IMAGE: A diagram showing two GitHub repository cards side by side. On the left, "facebook/react" with a star count and a "Fork" button highlighted. On the right, "arjun/react" showing the fork relationship below the repo name ("forked from facebook/react"). Caption: "A fork creates a personal copy of someone else's repository on your GitHub account. You have full control over your fork. The original is unaffected by anything you do." Style: realistic GitHub repository card mockup showing the fork button and fork attribution.
-->

The open source contribution flow:

```
1. Fork        -- click "Fork" on GitHub, creating arjun/libraryname from original/libraryname
2. Clone       -- clone your fork to your local machine
3. Branch      -- create a branch on your local fork for the fix
4. Commit      -- make and commit the fix
5. Push        -- push the branch to your fork on GitHub
6. Open PR     -- open a pull request FROM your fork's branch INTO the original repository
7. Review      -- the original project's maintainers review your contribution
8. Merge       -- if accepted, they merge it into the original
```

The key distinction is where the PR is opened. In a team workflow, the PR goes from `feature/branch` to `main` within the same repository. In the open source model, the PR goes from `arjun/libraryname:fix/bug-name` to `original/libraryname:main` -- across two different repositories.

This is how virtually every open source project accepts contributions. When you see a button that says "Fork" on any public GitHub repository, this is the mechanism it enables.

### git fetch vs git pull

In a team environment, the remote repository changes frequently. Multiple people are pushing branches and merging PRs throughout the day. Staying in sync becomes an active concern.

You already know `git pull`, which downloads remote changes and immediately integrates them into your current branch. In team contexts, some developers prefer to separate these two steps.

**git fetch** downloads the current state of the remote -- all new branches, all new commits -- but does not touch your working files or your current branch. Nothing in your local project changes. You are just updating your local knowledge of what the remote looks like.

```bash
git fetch origin
```

After fetching, you can inspect what arrived before deciding to integrate it:

```bash
git log main..origin/main
```

This shows the commits that are on `origin/main` but not yet on your local `main`. Once you have reviewed them and are satisfied, you run `git merge origin/main` to integrate.

`git pull` is the shortcut for `git fetch` followed immediately by `git merge`. For solo workflows, that shortcut is almost always fine. In team workflows with a busy `main` branch, some developers prefer to fetch and inspect before merging -- especially before pushing their own work -- to avoid surprises.

### git stash: Putting Work Aside Temporarily

Here is a situation that comes up constantly in team work. Arjun is in the middle of implementing something on his feature branch -- the code is not ready to commit, it is half-written. His manager messages him: "Can you quickly check the bug on the `fix/login-error` branch? It is urgent."

Arjun cannot switch branches right now. He has uncommitted changes in his working directory, and Git will either refuse the switch (if the changes conflict) or carry the changes over to the other branch with him (which would be confusing and messy).

He could commit the half-written work just to switch -- but that creates a meaningless commit in the history. The right tool for this situation is `git stash`.

```bash
git stash
```

This takes all uncommitted changes in the working directory and the staging area, saves them in a temporary storage area, and restores the working directory to the state of the last commit. The work is not lost -- it is shelved. Now the working directory is clean, and Arjun can switch branches freely.

```bash
git switch fix/login-error
```

He investigates the bug, does what he needs to, and switches back:

```bash
git switch feature/loading-spinner
```

Then he retrieves the shelved work:

```bash
git stash pop
```

The uncommitted changes come back exactly as they were. He continues where he left off.

`git stash` is not a replacement for committing -- it is a short-term tool for setting work aside temporarily so you can do something else cleanly. Do not leave work in the stash for days and forget it is there.

### Rebase: A Different Way to Integrate History

One term you will encounter on teams that care a lot about a clean commit history is **rebase**.

`git merge` integrates two branches by creating a merge commit that has two parents -- preserving the exact history of both branches. `git rebase` takes a different approach: it replays the commits from one branch on top of another, as if the branch had been created at the current tip of `main` rather than at some earlier point. The result is a linear history with no merge commit.

<!-- 
IMAGE: Two side-by-side commit graphs. Left (merge): main at C4, feature branch at C5 and C6, a merge commit C7 on main with two parent arrows. Right (rebase): main at C4, then C5' and C6' (the feature branch commits, rewritten) directly continuing main's line as C5' and C6'. Caption: "Merge preserves the full history of both branches. Rebase rewrites feature branch commits to appear as if they were built on top of the current main, producing a cleaner linear history." Style: flat vector commit graph side by side, one with a merge commit fork shape, one with a straight line.
-->

Teams that use rebase get a project history that reads as one clean sequence of commits rather than a graph of intersecting lines. Some teams and open source projects require it.

The critical rule is this: **do not rebase a branch that has already been pushed to a shared remote.** Rebasing rewrites commits -- it creates new commits with new hashes. If teammates have already based work on the old commits, their history and yours diverge in a way that is difficult to reconcile. Rebase is safe on a private branch that only you are working on. It is destructive on shared branches.

This is why rebase is mentioned here but not practiced. Once you are comfortable with the merge-based workflows in this module, rebase is a natural next step to explore with a team that uses it -- or to avoid entirely on teams that prefer merge.

### Let's Define Things Now...

> **Pull Request (PR):** A GitHub feature that is a formal proposal to merge one branch into another. Provides a space for code review, line-by-line comments, and approval before the merge happens. Used in team workflows instead of direct merges.

> **Code Review:** The process of teammates reading proposed changes in a pull request before they are merged. Reviewers check for correctness, clarity, performance, security, and consistency.

> **Fork:** A personal copy of someone else's repository on your own GitHub account. Used to propose changes to a project you do not have push access to. The standard model for open source contributions.

> **Open Source Contribution Flow:** Fork the repository, clone your fork, branch, commit, push to your fork, open a pull request from your fork into the original repository.

> **git fetch:** Downloads the current state of the remote (all new branches and commits) without changing local files or the current branch. Lets you inspect before integrating.

> **git pull:** A shortcut for `git fetch` followed by `git merge`. Downloads and immediately integrates remote changes into the current branch.

> **git stash:** Temporarily shelves all uncommitted changes in the working directory so the branch can be switched cleanly. The shelved work is not lost.

> **git stash pop:** Retrieves the most recently stashed changes and applies them back to the working directory.

> **Rebase:** An alternative to merge that replays commits from one branch on top of another to produce a linear history. Rewrites commit hashes, so it must never be used on branches that are already shared with teammates.

### Activity: Read the Room

Since this article is conceptual, the activity is about reading real things rather than running commands.

1. Go to any popular open source repository on GitHub (suggestions: `tailwindlabs/tailwindcss`, `facebook/react`, `vercel/next.js`). Click on "Pull requests" and open any recent merged PR. Read through the conversation:

   - What did the reviewer comment on?
   - Did the author push new commits in response to feedback?
   - How many approvals were required before it was merged?
   - What does the PR title and description tell you about the change?

2. On the same repository, click "Fork" (top-right corner of the main repository page). Do not make any changes -- just observe that a copy now appears under your GitHub account with the "forked from" label. You can delete it after.

3. Find the repository's `CONTRIBUTING.md` file (most large open source projects have one). Read through it. Notice how much of the workflow described there matches what you just learned: branch names, commit message conventions, PR requirements, review expectations.

4. Look at a few PR descriptions in the repository. Notice how the best ones explain the "why" behind the change, not just the "what." A good PR description is as important as a good commit message.

## What's Coming Next...

You now have everything you need to start using Git and GitHub in a real development workflow. You can initialise a repository, track history with commits, push and pull with GitHub, branch for new work, merge changes back in, and you understand the vocabulary of team collaboration -- pull requests, forks, code review, stash -- well enough to work within any team's contributing guide without confusion.

In the next article, we step back from the commands and look at the practices that make a developer genuinely good to work with in a version-controlled environment: writing meaningful commit messages, keeping branches focused, structuring pull requests that reviewers actually want to read, and a few habits that separate a clean, maintainable project history from a chaotic one. After that, we move on to the final topic of this course -- hosting, and putting your project on the actual internet for anyone to access.
