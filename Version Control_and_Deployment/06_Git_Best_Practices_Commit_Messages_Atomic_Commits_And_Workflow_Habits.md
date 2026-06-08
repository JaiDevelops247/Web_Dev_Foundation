## Introduction

Arjun has been at the internship for two weeks. He has been branching, committing, pushing, opening pull requests -- the mechanics are clicking. But his first real pull request review does not go the way he expected.

His manager leaves no comments on the code itself. The logic is fine. But she leaves a comment at the top: "Before we merge, can you clean up the commit history? It's hard to understand what happened and in what order."

Arjun looks at his own commits for the first time through someone else's eyes:

```
fixed stuff
wip
navbar done finally
css fix + new api call + remove old component
more changes
oops
```

The code works. But the history tells a story that nobody can read. It does not say what changed, why it changed, or in what order the decisions were made. If a bug is introduced somewhere in these commits and needs to be traced six months from now, this history provides almost no help.

This is the gap between knowing Git and using Git well. The commands are the easy part. The habits -- how you write messages, how you group changes, what you keep out of the repository, how you name branches -- are what make a developer good to work with. They are also what make your own work easier to maintain and debug over time.

This article is about those habits. They are not enforced by Git itself. They are conventions that the developer community has converged on because they work. Following them consistently is what separates a clean, readable project history from a chaotic one.

### Writing Commit Messages That Actually Help

A commit message answers one question: **what does this change do, and why?**

It is a message to your future self, your teammates, and anyone who maintains this code after you. The commit hash identifies the change uniquely. The message explains it in human language.

#### The Subject Line

The subject line -- the first line of the commit message -- should be:

**Concise.** Keep it under 72 characters. This is not arbitrary; most Git tools truncate at this length, and short messages are easier to scan in a log.

**Written in the imperative mood.** Write the message as if you are giving the codebase an instruction. The convention is: if you apply this commit, it will...

```
Add login form to the homepage
Fix button alignment on mobile
Remove unused CSS variable from navbar
```

Not:

```
Added login form         (past tense)
Fixes button alignment   (present third person)
Removing unused CSS      (gerund)
```

The imperative mood is the standard because Git itself uses it for automated messages: "Merge branch 'feature/dark-mode'" -- not "Merged" or "Merging."

**Specific.** "Fix bug" tells nobody anything. "Fix null reference error when user submits empty search" tells you exactly where to look if that bug comes up again. "Update styles" is vague. "Increase button padding for touch targets on mobile" is useful.

#### The Body (Optional but Valuable)

For anything beyond a trivial one-line change, add a body after an empty line:

```
Fix null reference on empty search submission

The search handler was accessing query.trim() before checking
whether query was null. On iOS Safari, the input value returns
null instead of an empty string when cleared by the virtual
keyboard. Added an early return if query is falsy.
```

The body explains the why -- the context, the root cause, the reason this approach was chosen over another. The what is visible in the diff. The why is in the message.

### Conventional Commits: A Standard the Industry Uses

Many teams go one step further and adopt a prefix system called **Conventional Commits**. The idea is simple: every commit message starts with a short tag that tells anyone reading the log what category of change this is.

```
feat: add typing indicator to chatbot input
fix: prevent form submission on Enter key in search
docs: add API usage examples to README
style: reformat navbar CSS to match project conventions
refactor: extract validation logic into separate function
test: add unit tests for login form validation
chore: update .gitignore to exclude .DS_Store
```

The prefixes are standardized:

| Prefix | Meaning |
|---|---|
| `feat:` | A new feature |
| `fix:` | A bug fix |
| `docs:` | Documentation changes only |
| `style:` | Formatting, whitespace, missing semicolons -- no logic change |
| `refactor:` | Code restructuring with no behaviour change |
| `test:` | Adding or updating tests |
| `chore:` | Maintenance tasks, dependency updates, config changes |

The benefit is immediate readability. Looking at a log like this tells you at a glance what kind of work was happening and in what proportion. Tools can also parse conventional commit logs automatically to generate changelogs or determine whether a release includes new features or only bug fixes.

You do not have to adopt conventional commits to write good commit messages. But if your team uses them, or you are contributing to an open source project that requires them, you will now recognize the pattern instantly.

### Atomic Commits: One Change, One Commit

An **atomic commit** is a commit that represents exactly one logical unit of change. One feature addition, one bug fix, one refactor, one configuration update.

Look at Arjun's original commit:

```
css fix + new api call + remove old component
```

That is three separate changes bundled into one commit. Here is why that creates problems:

**Revert becomes destructive.** If the new API call turns out to be wrong and needs to be undone, reverting that commit also undoes the CSS fix and the component removal -- two things that were fine. There is no clean way to undo just the API part.

**Blame becomes useless.** Git can tell you which commit last modified any line of the codebase. If dozens of unrelated changes are in the same commit, that information stops being useful for tracing when and why something changed.

**Review becomes harder.** When a reviewer looks at a PR, they review diffs. A diff that mixes three different concerns forces the reviewer to mentally untangle them rather than evaluate each change on its own terms.

The correct version of that commit is three commits:

```
fix: correct padding on mobile product card
feat: add recommended products API call to homepage
refactor: remove deprecated UserCard component
```

Each can be reviewed, understood, or reverted independently. The history reads like a clear sequence of decisions.

The practical habit is this: before you commit, look at what you have staged. If staging everything produces a message that needs "and" or a comma, that is a signal to split it.

### What NOT to Commit

Some things should never enter a repository's history. The key word is history -- even if you delete a file in a later commit, the content of that file is still visible in every earlier commit. The history is permanent.

#### Secrets and API Keys

**Never commit API keys, passwords, tokens, or any credentials.** This applies even to private repositories -- private does not mean zero risk, and many credential leaks come from repositories that were briefly public or were later made public.

```javascript
// Do NOT do this
const API_KEY = "sk-proj-1a2b3c4d5e6f7g8h9i0j";
fetch(`https://api.openai.com/v1/...`, {
  headers: { Authorization: `Bearer ${API_KEY}` }
});
```

The correct approach is to store secrets in **environment variables** -- values that exist on the machine or server but are never written into the code files. In a Node.js project, a `.env` file holds these values locally. That file goes into `.gitignore` (covered below) so it is never committed. The code reads from `process.env.API_KEY` instead:

```javascript
const API_KEY = process.env.API_KEY;
```

A template file called `.env.example` with placeholder values (but not the real secrets) is committed instead, so teammates know what variables they need to set up on their own machines.

#### Generated Files and Dependencies

**Do not commit files that can be recreated from what is already in the repository.**

The most common example is `node_modules/` -- the folder that `npm install` creates by downloading every dependency listed in `package.json`. This folder can contain hundreds of thousands of files and hundreds of megabytes. There is no reason to commit it because anyone who clones the repository can recreate it by running `npm install`.

Committing generated files bloats the repository size, pollutes the diff on every dependency update, and slows down every clone and push.

#### Editor and OS Files

Files like `.DS_Store` (created automatically by macOS in every folder you open), `Thumbs.db` (Windows), `.vscode/` (VS Code workspace settings), and `.idea/` (JetBrains IDE settings) are personal to your machine and editor setup. They have no relevance to the project and should not be in a shared repository.

#### The .gitignore File

The solution to all of the above is the **`.gitignore`** file. This is a plain text file at the root of the repository that tells Git which files and folders to ignore entirely -- they will never be staged, committed, or tracked.

A typical `.gitignore` for a web project:

```
# Dependencies
node_modules/

# Environment variables
.env
.env.local

# OS files
.DS_Store
Thumbs.db

# Editor files
.vscode/
.idea/

# Build output
dist/
build/
```

GitHub provides a curated collection of `.gitignore` templates for every major language and framework. When creating a new repository, you can select the relevant template and GitHub will add it automatically.

<!-- 
IMAGE: The GitHub "Create a new repository" page with the ".gitignore template" dropdown open, showing options like "Node", "Python", "Java", "React". Caption: "GitHub offers .gitignore templates when creating a repository. Selecting the right one automatically ignores common generated files for that ecosystem." Style: realistic browser screenshot of the GitHub new repository form with the .gitignore dropdown open.
-->

### Branch Naming Conventions

A branch name should tell anyone reading it what the branch is for, without needing to look at the commits.

The widely adopted convention is **prefix/short-description** in lowercase with hyphens:

```
feature/user-authentication
feature/dark-mode-toggle
fix/broken-submit-button
fix/search-null-reference
chore/update-gitignore
chore/remove-unused-dependencies
docs/add-api-usage-examples
```

The prefix matches the conventional commit prefix for the work on that branch. Short, hyphenated, lowercase. No spaces, no vague names like `arjun-branch` or `new-stuff` or `test123`.

A good branch name answers: what is being done here? If a teammate sees `fix/search-null-reference` in the branch list, they know immediately what work is happening there. If they see `arjun-test-2`, they know nothing.

### Commit Frequently, Not Infrequently

A common instinct for new developers is to work for a long time and then make one big commit at the end of the session. This is the wrong direction.

Small, frequent commits are better than large, infrequent ones in every way:

**Easier to understand.** A commit that touches 3 files and changes 20 lines is readable in a review. A commit that touches 40 files and changes 800 lines takes an hour to review and is easy to miss problems in.

**Easier to revert.** If a small commit introduces a bug, reverting it undoes only the small change. If a large commit introduces a bug, reverting it undoes hours of unrelated work.

**Easier to locate bugs.** Git has a tool called `git bisect` that can automatically binary-search through commit history to find the commit that introduced a bug. This only works well when commits are small and represent discrete changes.

**Safer to work with.** A commit is a save point. If something goes horribly wrong with the working directory, the most recent commit is the recovery point. More frequent commits mean the worst-case recovery point is closer in time.

The practical habit: commit whenever a logical unit of work is complete -- not when the whole feature is done, but when a coherent piece of it is working. "Added the form markup" is a commit. "Added client-side validation to the form" is another. "Connected the form to the API" is a third.

### Writing a README That Represents the Project

Every public repository -- and most private ones -- should have a `README.md` at the root. It is the front page of the project on GitHub, the first thing anyone sees, and it is what determines whether someone understands the project in 30 seconds or closes the tab.

A complete README answers four questions:

**What is this?** A project title and one paragraph describing what it does and who it is for.

**What does it look like?** A screenshot or a short demo GIF. This is the single highest-value thing you can add to a README. Developers are far more likely to read further if they can see the thing before reading about it.

**How do I run it?** Clear steps to clone the repository and get it running locally. Assume the reader has the right language installed but nothing else.

**Where can I see it live?** A link to the deployed version if one exists.

```markdown
# Arjun's Portfolio

A personal portfolio site showcasing projects and contact information.
Built with HTML, CSS, and Vanilla JavaScript. Responsive across desktop and mobile.

## Preview
![Portfolio screenshot](screenshot.png)

## Technologies
- HTML5
- CSS3 (Flexbox, Grid, Custom Properties)
- Vanilla JavaScript

## Run Locally
1. Clone: `git clone https://github.com/arjun/portfolio.git`
2. Open `index.html` in any browser

## Live Demo
[arjun.github.io/portfolio](https://arjun.github.io/portfolio)
```

A README does not need to be long. It needs to be clear. A well-written README signals that the developer takes their work seriously and cares about the experience of anyone who encounters the repository -- including a potential employer.

### Quick Reference: All Practices at a Glance

| Practice | The Rule | Why It Matters |
|---|---|---|
| **Commit message mood** | Imperative: "Add form", not "Added form" | Reads as an instruction; matches Git's own automated messages |
| **Commit message length** | Subject line under 72 characters | Tools truncate beyond this; short messages are easier to scan in logs |
| **Commit message body** | Explain the why, not the what | The diff shows what changed; the message explains the reasoning |
| **Conventional commits** | Prefix with `feat:`, `fix:`, `docs:`, etc. | Immediate log readability; enables automated changelog generation |
| **Atomic commits** | One logical change per commit | Allows clean revert, clear blame, and focused review |
| **Never commit secrets** | Use environment variables and `.env` in `.gitignore` | History is permanent; secrets in history are permanently exposed |
| **Never commit generated files** | `node_modules/`, `dist/`, `build/` in `.gitignore` | Bloats the repo; can always be recreated from committed source |
| **Never commit editor/OS files** | `.DS_Store`, `.vscode/` in `.gitignore` | Personal to your machine; pollutes the repo for everyone else |
| **Branch naming** | `prefix/short-description` in lowercase hyphens | Branch list stays readable; purpose is clear without opening commits |
| **Commit frequency** | Small and frequent, not large and infrequent | Easier to review, revert, and bisect; closer recovery points |
| **README** | Title, description, screenshot, setup steps, live link | The face of the project; first thing any visitor sees |

### Let's Define Things Now...

> **Imperative mood:** The grammatical form used in Git commit subjects. "Add login form" -- as if instructing the codebase what to do if the commit is applied.

> **Conventional Commits:** A widely adopted commit message standard where the subject is prefixed with a type (`feat:`, `fix:`, `docs:`, etc.) to categorize the nature of the change.

> **Atomic Commit:** A commit that represents exactly one logical unit of change. Can be reviewed, understood, and reverted independently of other commits.

> **Environment Variable:** A value defined on the machine or server environment rather than in code files. Used to store secrets like API keys so they are never committed to the repository.

> **.gitignore:** A file at the root of a repository listing files and folders Git should never track. Typically includes secrets, generated files, and editor/OS files.

> **README.md:** A Markdown file at the repository root that appears on the GitHub project page. Should include a project description, screenshot, setup instructions, and a live demo link.

### Activity: History Audit

Take the `portfolio` repository from the previous articles and audit it:

1. Run `git log --oneline` to see all commit messages. For each one, answer: Is this written in the imperative mood? Is it specific enough to know what changed? Does it mention the why if the reason is not obvious?

2. Rewrite two commit messages mentally using conventional commit format. For example, if you have a commit called "first commit", what should it have been? `feat: initialise portfolio with HTML structure`? `chore: set up project with boilerplate HTML`?

3. Check the repository for a `.gitignore` file. If it exists, does it cover secrets, OS files, and editor files? If it does not exist, create one now with the patterns covered in this article.

4. Check the repository for a `README.md`. If it exists, does it include all four sections? If not, update it. If there is no README, write one from scratch.

5. Look at a recent commit where you changed multiple things. If you were doing it again today, would you have split it into more than one commit? Write out what the separate commit messages would have been.

## What's Coming Next...

This wraps up everything on Git and Version Control. You now know how to track history, work with GitHub, branch and merge, collaborate in a team setting, and maintain the habits that make a developer genuinely good to work with in a real project environment.

There is one piece left to complete the picture. Right now, your portfolio is a collection of files that open in a browser locally. A link to the GitHub repository lets people see the source code -- but not the actual running website. The final article in this course covers **hosting** -- taking your project from a local folder and a GitHub repository to a live URL that anyone on the internet can visit, on any device, at any time.
