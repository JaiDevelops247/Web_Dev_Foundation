## Introduction

Arjun's portfolio is live on GitHub Pages. Recruiters can visit the URL, see his work, and browse the project without touching a terminal or a file. That part is done.

A few weeks later, he adds a feature: a live section on the portfolio that fetches his recent GitHub activity using the GitHub API and displays it. He stores the API token in a `.env` file, keeps it out of the repository, and everything works perfectly on his local machine.

Then he tries to put it on GitHub Pages.

The token cannot go into the source code -- anyone can read JavaScript files in a browser's DevTools. GitHub Pages has no way to inject it at runtime. The feature works locally and breaks in production. Every option he can think of either exposes the key or requires more infrastructure than GitHub Pages offers.

This is a version of the problem that every developer hits eventually. It might be an API key. It might be a React project that needs `npm run build` before it can be served. It might be a Next.js app that needs server-side rendering. The details vary, but the pattern is always the same: the project needs something to happen on the server side before the files reach the browser, and GitHub Pages cannot do that.

**Vercel** exists to solve exactly this problem. It is a deployment platform that runs a build process, manages environment variables, and handles all the infrastructure between your GitHub repository and a live URL -- so you do not have to think about servers at all.

### What Vercel Does That GitHub Pages Cannot

The fundamental difference is the **build step**.

GitHub Pages takes whatever files are in your repository branch and serves them as-is. No processing, no compilation, no environment injection. Files in, files out.

Vercel introduces a stage in between. When it detects a new push, it:

1. Pulls the latest code from the repository
2. Runs a **build command** (like `npm run build`) that compiles, bundles, or processes the source files into output files a browser can use
3. Injects **environment variables** into the process so API keys and configuration never have to live in the source code
4. Serves the output files at a public URL

For a plain HTML, CSS, and JavaScript project with no build step -- like the portfolio -- the build command is simply left empty, and Vercel serves the files directly from the root, just like GitHub Pages would. The deployment process is the same. But when the project grows to use a framework or needs private configuration, Vercel already handles it. There is nothing to migrate or reconfigure.

<!-- 
IMAGE: A side-by-side comparison diagram. Left side labeled "GitHub Pages": a repository box with files, an arrow directly to a globe icon labeled "Serves files as-is". Right side labeled "Vercel": a repository box with files, an arrow to a box labeled "Build step (npm run build) + Environment Variables injected", then an arrow to a globe icon labeled "Serves processed output". Caption: "GitHub Pages delivers files directly. Vercel runs a build process and injects environment variables before serving -- making it compatible with frameworks and projects that need private configuration." Style: flat vector two-path comparison diagram.
-->

### Creating a Vercel Account

Go to **vercel.com** and click "Sign Up." The fastest path is to sign in with your GitHub account -- Vercel gets permission to access your repositories, which is what allows it to pull code and set up automatic deployments.

<!-- 
IMAGE: The Vercel signup page showing three options: "Continue with GitHub", "Continue with GitLab", "Continue with Bitbucket". The "Continue with GitHub" option is highlighted. Caption: "Sign up with your GitHub account. This is the easiest path -- Vercel can then access your repositories directly for deployment." Style: realistic browser screenshot of the Vercel signup page.
-->

Once signed in, the Vercel dashboard shows your projects. It starts empty.

### Deploying a Project: Step by Step

Click **"Add New..."** then **"Project"**.

<!-- 
IMAGE: The Vercel "Import Git Repository" page. A search box at the top labeled "Search..." and below it a list of GitHub repositories with an "Import" button next to each one. The "arjun/portfolio" repository is visible with an "Import" button highlighted. Caption: "Vercel lists your GitHub repositories. Find the project you want to deploy and click Import." Style: realistic browser screenshot of the Vercel import repository page.
-->

Vercel lists all repositories from the connected GitHub account. Find the portfolio repository and click **Import**.

The configuration screen appears:

<!-- 
IMAGE: The Vercel project configuration screen. Fields visible: "Project Name" (pre-filled as "portfolio"), "Framework Preset" dropdown (set to "Other"), "Root Directory" (set to "./"), "Build Command" (empty), "Output Directory" (set to "./"). A "Deploy" button is at the bottom. Caption: "For a plain HTML/CSS/JS project with no build step, leave the Build Command empty and the Output Directory as the root. Vercel will serve the files directly." Style: realistic browser screenshot of the Vercel deploy configuration form.
-->

For the portfolio project:

**Framework Preset:** Set to "Other." Vercel can auto-detect React, Next.js, Vue, and other frameworks. For a plain HTML project, "Other" tells it not to assume any framework.

**Build Command:** Leave empty. There is no compilation step. Vercel will serve the files directly.

**Output Directory:** Leave as `./` (the repository root). This is where Vercel looks for the files to serve.

Click **Deploy**.

Vercel pulls the code from GitHub, runs through the (empty) build step, and within about 30 seconds generates a live URL:

```
https://portfolio-arjun.vercel.app
```

The site is live. Every file -- `index.html`, `style.css`, `script.js`, images -- is served from that URL. It behaves identically to the GitHub Pages deployment.

### Automatic Redeployment on Push

Like GitHub Pages, Vercel watches the connected branch for new commits. Push anything:

```bash
git add .
git commit -m "feat: add GitHub activity section to portfolio"
git push
```

Vercel detects the push within seconds, runs the deployment pipeline, and the live URL reflects the changes in under a minute. The deployment workflow is identical to what you already know -- push is the trigger, everything else is automatic.

### Preview Deployments: Every Branch Gets a URL

This is where Vercel pulls noticeably ahead of GitHub Pages for team workflows.

Every time a branch is pushed to GitHub -- not just `main` -- Vercel automatically builds it and deploys it to a unique **preview URL**. The preview URL is separate from the production URL and updates with every new push to that branch.

```
Production:  https://portfolio-arjun.vercel.app          (from main)
Preview:     https://portfolio-git-feature-dark-mode-arjun.vercel.app  (from feature/dark-mode)
```

<!-- 
IMAGE: The Vercel project dashboard showing a "Deployments" list. At the top, a deployment labeled "Production" from the main branch with a green "Ready" badge and a production URL. Below it, two deployments labeled "Preview" from branches "feature/dark-mode" and "fix/nav-spacing", each with their own unique preview URLs and green "Ready" badges. Caption: "Vercel creates a unique preview URL for every branch push. Teammates and reviewers can see the running version of a feature branch without merging it to main." Style: realistic browser screenshot of the Vercel deployments list.
-->

The practical consequence is significant. In a team workflow, a reviewer looking at a pull request can click the preview URL in the PR description and see the feature branch running as a real site -- without merging anything, without pulling the branch locally, without running any commands. They review the code and the live preview side by side.

This is one of the reasons Vercel has become the default deployment platform for most modern frontend teams.

### Environment Variables: Keeping Secrets Off GitHub

Back to Arjun's original problem: the API key that cannot go in the source code.

Vercel solves this with **environment variables** managed through the project settings. These are key-value pairs stored on Vercel's servers, injected into the build process and available to the running application -- but never written into any file in the repository.

In the Vercel dashboard, go to the project, click **Settings**, then **Environment Variables**.

<!-- 
IMAGE: The Vercel Environment Variables settings page. A form with two fields: "Key" (filled with "GITHUB_API_TOKEN") and "Value" (filled with a masked string of asterisks). Three checkboxes below: "Production", "Preview", "Development" -- all checked. An "Add" button is to the right. Caption: "Environment variables in Vercel are stored securely on the server. They are injected at build time and never appear in the source code or the repository." Style: realistic browser screenshot of the Vercel environment variables settings panel.
-->

Add the key name (for example, `GITHUB_API_TOKEN`) and the actual token value. Choose which environments it applies to -- Production (live site), Preview (branch previews), and Development (for local use via Vercel CLI).

In the JavaScript code, the value is accessed the same way environment variables are accessed in any Node.js-compatible build environment:

```javascript
const token = process.env.GITHUB_API_TOKEN;
```

The token is never in `script.js`. It is never in the repository. No one viewing the page source or the repository can find it. Vercel injects it at build time and it exists only in the running process.

For projects without a build step (plain HTML and JS files served directly), environment variables injected this way are not available in browser-executed JavaScript -- that limitation is inherent to how browsers work, not to Vercel specifically. Environment variables in this context are meaningful for frameworks that run a build step, where the value can be embedded during compilation. For plain JS files, the correct approach is to use a serverless function (a backend endpoint Vercel can also host) to make the API call on the server side, keeping the key entirely out of the browser.

This distinction is worth knowing: **environment variables solve the secrets problem for projects with a build step or a backend function. For pure static sites, the only real solution is a server sitting in between.**

### Choosing Between GitHub Pages and Vercel

Both platforms are free for personal projects. The choice depends on what the project needs:

| | GitHub Pages | Vercel |
|---|---|---|
| Plain HTML / CSS / JS | Works perfectly | Works perfectly |
| React / Vue / Next.js | Manual setup required | Auto-detected, zero config |
| Environment variables | Not supported | Built-in, secure |
| Preview URLs per branch | Not supported | Automatic |
| Custom domain | Supported | Supported |
| Backend / serverless | Not supported | Serverless functions supported |

For everything built in this course -- static HTML, CSS, and JavaScript -- either platform works. GitHub Pages is simpler to set up for a quick deployment. Vercel is the better choice the moment the project needs a build step, private configuration, or team preview workflows.

As soon as you move to a JavaScript framework in your next step -- React, Vue, or anything that runs `npm run build` -- Vercel is effectively the default platform. It detects the framework automatically and deploys with zero configuration.

### Let's Define Things Now...

> **Build Step:** A command (like `npm run build`) that compiles or processes source files into output files before deployment. Required for JavaScript frameworks; not needed for plain HTML/CSS/JS.

> **Vercel:** A deployment platform that runs a build step, injects environment variables, and serves the output at a public URL. Auto-redeploys on every push. Supports modern frameworks with zero configuration.

> **Preview Deployment:** A Vercel feature that automatically builds and deploys every branch push to a unique URL. Allows reviewers to see a running version of a feature branch before it merges to main.

> **Environment Variables (Vercel):** Key-value pairs stored securely in Vercel's settings panel. Injected into the build process at deployment time. The values never appear in the source code or the repository.

> **Serverless Function:** A small piece of backend code that Vercel (and similar platforms) can run in the cloud without a dedicated server. Useful for keeping API calls and secrets off the browser entirely.

### Activity: Deploy to Vercel

1. Go to **vercel.com** and sign up with your GitHub account.

2. Click "Add New > Project" and import the `portfolio` repository.

3. Set the framework preset to "Other", leave the build command empty, and leave the output directory as `./`. Click Deploy.

4. Once the deployment completes, open the Vercel URL and confirm the site loads correctly.

5. Make a small change to the portfolio locally. Commit and push to `main`. Watch the Vercel dashboard -- a new deployment should start automatically. Confirm the change appears on the live URL within a minute.

6. Create a new branch (`git switch -c test/vercel-preview`), make a visible change, and push it. Check the Vercel dashboard for the automatically generated preview URL. Open it in the browser.

7. Delete the test branch locally and on GitHub. The preview deployment will become inactive.

---

## What We Have Built Together

This brings us to the end of the course. Look at where we started and where we are now.

When this course began, the goal was to understand what the internet is and how it works. That seemed far removed from writing a web page. But every concept since then has been a layer on top of that foundation.

Here is what you now know how to do:

**You understand the web end to end.** You know what happens from the moment a URL is typed in a browser to the moment a page appears on the screen -- DNS, TCP, HTTP, servers, clients, rendering. When something breaks in production, you have a mental model of every stage where it could go wrong.

**You can build the structure and style of any web page.** HTML gives you the skeleton -- semantic elements, forms, tables, links, media. CSS gives you the visual layer -- layout with Flexbox and Grid, typography, color, responsive design with media queries, transitions and animations. These are not beginner tools that you will outgrow. They are what every website on the internet is made of.

**You can write real JavaScript.** Variables, data types, functions, loops, arrays, objects, error handling, promises, async/await -- and more importantly, you can connect all of it to the browser: manipulating the DOM, handling user events, fetching data from APIs, managing state. You have the logic layer, not just the visual one.

**You can manage your code professionally.** Git and GitHub, version history, branching, merging, pull requests, meaningful commit messages, and `.gitignore`. You can work in a team without stepping on anyone's work, trace any change in a project's history, and keep a clean, readable timeline of every decision made.

**You can ship.** Not just write code, but put it on the internet. GitHub Pages for static sites, Vercel for anything more complex. A project you build today can have a live URL that anyone in the world can visit by the end of the day.

### Where the Path Goes From Here

This course gave you the foundation. Every path from here builds on exactly what you have learned.

**JavaScript Frameworks -- React or Vue**
The most immediate next step for most web developers. React and Vue are libraries that make building complex, interactive UIs much more manageable -- component-based architecture, state management, routing. Everything you know about JavaScript, the DOM, and events applies directly. The syntax is new; the concepts are the same. Vercel deploys React apps with zero configuration.

**Angular**
A full-featured framework maintained by Google, commonly used in enterprise environments. More opinionated than React or Vue -- it prescribes a specific structure for the entire application, which makes large team projects more consistent. TypeScript (a typed superset of JavaScript) is central to Angular, which introduces a new layer on top of the JavaScript you already know.

**Backend Development**
The other side of the client-server model you studied in the first module. Node.js lets you write server-side JavaScript -- handling HTTP requests, connecting to databases, building REST APIs. Python with Django or Flask is a popular alternative. Once you understand the backend, you can build full-stack applications: a React frontend talking to your own API, reading and writing to your own database.

**Going Deeper into the Foundations**
CSS alone has depth that this course only scratched. CSS architecture (BEM, utility-first with Tailwind), design systems, accessibility, performance optimisation, animations and transitions -- each of these is a meaningful specialisation. Similarly, JavaScript has a lot more to explore: TypeScript, design patterns, testing, build tooling.

The clearest next step is the one that connects to what you want to build. If you want to build product UIs, go to React. If you want to build APIs and handle data, go to Node.js. If you want to build visually polished sites, go deeper into CSS. The foundation you have built makes all of those paths shorter than they would have been otherwise.

## What's Coming Next...

There is no next article. This is the end of the course.

What comes next is yours to decide -- a framework, a backend, a project idea, an open source contribution, or simply building more things with what you already have. The skills in this course are real, production-grade, and immediately usable. Start with whatever excites you most and build something.
