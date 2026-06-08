## Introduction

Arjun's portfolio is on GitHub. The repository is clean, the commit history is readable, the README has a screenshot and a description. He sends the link to a few people.

They click it and see this:

<!-- 
IMAGE: A GitHub repository page for "arjun/portfolio" showing the file tree: index.html, style.css, script.js, README.md. The page shows source code and file names -- not a rendered website. Caption: "A GitHub repository shows your source code, not your website. The HTML, CSS, and JavaScript files are visible, but clicking them opens the raw text -- not a running site." Style: realistic browser screenshot of a GitHub repository file listing.
-->

The files are there. The code is there. But what loads in the browser is the repository view -- a file explorer, not a website. When someone clicks `index.html`, they see raw HTML text. There is no running site. There is no URL that opens the portfolio the way it looks in a browser.

This gap -- between code that exists in a repository and a website that anyone can visit -- is what **deployment** closes.

**Deployment** means publishing a project to a server that serves it over the internet, so that a URL opens the finished, running product rather than the source code. Until something is deployed, it exists only on your machine or in a repository. After deployment, it has a public address and works for anyone, anywhere, on any device.

For a project like Arjun's portfolio -- pure HTML, CSS, and JavaScript, no backend -- GitHub offers a hosting service built directly into the repository: **GitHub Pages**.

### What GitHub Pages Is

**GitHub Pages** is a free static site hosting service built into GitHub. It reads the HTML, CSS, JavaScript, and image files from a repository branch, puts them on GitHub's servers, and serves them at a public URL. The deployment happens automatically -- no separate hosting account, no configuration files, no payment.

The key phrase is **static site**. GitHub Pages serves files exactly as they are. It does not run server-side code. There is no database, no backend logic, no Node.js or Python process running in the background. The files go out to the browser as-is and the browser renders them.

This is exactly what most of what we have built in this course is. An HTML file, a CSS file, a JavaScript file. No server needed. GitHub Pages handles it perfectly.

### Two Types of GitHub Pages Sites

GitHub Pages can host two categories of sites, and the URL structure differs between them.

#### User Sites

A **user site** is tied to your GitHub account itself. To create one, you make a repository with a very specific name: `username.github.io` -- where `username` is your exact GitHub username. GitHub Pages automatically serves whatever is in that repository's `main` branch at:

```
https://username.github.io
```

For Arjun, that would be `arjun.github.io`. No path after the domain. This is typically used for a personal homepage or developer portfolio -- the single site that represents you as a developer.

Each GitHub account can have one user site.

#### Project Sites

A **project site** is tied to a specific repository rather than the account. Any repository -- not just one named `username.github.io` -- can have GitHub Pages enabled. The resulting URL follows this pattern:

```
https://username.github.io/repository-name
```

So if Arjun enables GitHub Pages on a repository called `portfolio`, the URL becomes:

```
https://arjun.github.io/portfolio
```

Each GitHub account can have unlimited project sites, one per repository. This is what we will use for the portfolio example in this article.

<!-- 
IMAGE: A side-by-side comparison. Left side: A GitHub repository named "arjun/arjun.github.io" with a label underneath saying "User Site" and URL "arjun.github.io". Right side: A GitHub repository named "arjun/portfolio" with a label underneath saying "Project Site" and URL "arjun.github.io/portfolio". Caption: "User sites are served from a repository named username.github.io. Project sites are served from any repository at username.github.io/repository-name." Style: flat vector side-by-side repository card comparison.
-->

### Enabling GitHub Pages

Go to the repository on GitHub. Click **Settings** in the top navigation.

<!-- 
IMAGE: A GitHub repository page with the top navigation tabs visible: Code, Issues, Pull requests, Actions, Projects, Security, Insights, Settings. The "Settings" tab is highlighted. Caption: "GitHub Pages is configured inside the repository's Settings tab." Style: realistic browser screenshot of a GitHub repository top navigation with Settings highlighted.
-->

In the left sidebar, scroll down to find **Pages** under the "Code and automation" section.

<!-- 
IMAGE: The GitHub Pages settings page. A "Source" section shows a dropdown set to "Deploy from a branch". Below it, a "Branch" section shows two dropdowns: the first set to "main" and the second set to "/ (root)". A "Save" button is to the right of the branch dropdowns. Caption: "The GitHub Pages settings. Select the branch to deploy from (usually main) and the folder (root for most projects). Click Save to start deployment." Style: realistic browser screenshot of the GitHub Pages settings section.
-->

The settings to configure:

**Source:** Set to "Deploy from a branch." (The other option, "GitHub Actions," is for projects with a build step -- not needed here.)

**Branch:** Select `main` (or whichever branch has the finished code). The second dropdown is the folder -- for most projects, leave it as `/ (root)`, meaning GitHub Pages will look for `index.html` in the root of the repository.

Click **Save**.

GitHub starts building the site immediately. Refresh the page after about 30 to 60 seconds and a URL appears at the top of the Pages section:

```
Your site is live at https://arjun.github.io/portfolio
```

Click it. The portfolio opens in the browser -- the actual rendered website, not the source code. Anyone with that URL can now visit it.

### Updating the Deployed Site

The deployed site stays in sync with the branch automatically. To update it, push new commits to the deployed branch:

```bash
git add .
git commit -m "feat: add projects section to portfolio"
git push
```

GitHub Pages detects the push, rebuilds the site, and the live URL reflects the changes within a minute or two. There is no separate "redeploy" step. The push is the deployment trigger.

This is the full deployment loop:

```
Edit files locally  -->  git add + git commit  -->  git push  -->  Site updates automatically
```

### Common Problems and How to Fix Them

GitHub Pages is straightforward for most projects, but a few specific issues come up regularly. Knowing them in advance saves time.

#### Blank Page or Missing Content

You visit the URL and the page is blank, or it loads but images and styles are missing.

The most common cause is **file paths**. On your local machine, absolute paths like `/style.css` or `/images/photo.jpg` work fine because they resolve to the root of your file system. But on a project site at `arjun.github.io/portfolio`, the root of the URL is `arjun.github.io` -- not `arjun.github.io/portfolio`. So `/style.css` resolves to `arjun.github.io/style.css`, which does not exist. The file is at `arjun.github.io/portfolio/style.css`.

The fix is to use **relative paths** throughout:

```html
<!-- Breaks on a project site -->
<link rel="stylesheet" href="/style.css">
<img src="/images/photo.jpg">

<!-- Works correctly -->
<link rel="stylesheet" href="style.css">
<img src="images/photo.jpg">
```

Relative paths resolve from the current file's location, not from the domain root. They work correctly in both local development and on GitHub Pages.

#### Assets Not Loading: Case Sensitivity

On Mac and Windows, file systems are case-insensitive. `Style.css`, `style.css`, and `STYLE.CSS` all refer to the same file. The browser on your local machine will find the file regardless of how the name is capitalized in the HTML.

GitHub Pages runs on **Linux**, where file systems are case-sensitive. If the file is named `style.css` and the HTML references `Style.css`, Linux treats these as two different files. The stylesheet does not load. The site looks broken in production but works fine locally.

The fix is to be consistent: pick a casing convention (all lowercase is the standard for web files) and use it in both the file names and every reference to those files in the HTML and CSS.

#### The Site Shows an Old Version

You push a change, visit the URL, and the old version is still there.

This is almost always a caching or build-time issue. GitHub Pages takes up to two minutes to rebuild after a push. If you visit the URL immediately after pushing, you will see the previous version.

Wait a minute and do a hard refresh (`Cmd + Shift + R` on Mac, `Ctrl + Shift + R` on Windows). The updated site appears.

If it has been more than five minutes and the site still has not updated, check the repository's **Actions** tab on GitHub. GitHub Pages uses a workflow to build and deploy. If the build failed, the Actions tab shows the error in red with a log explaining what went wrong.

<!-- 
IMAGE: The GitHub Actions tab for a repository showing a list of recent workflow runs. One run shows a green checkmark labeled "pages build and deployment" with a timestamp. Another run above it shows a red X with the same label. Caption: "The Actions tab shows every GitHub Pages deployment attempt. A red X means the deployment failed -- click it to see the error log." Style: realistic browser screenshot of the GitHub Actions workflow run list.
-->

### Custom Domains: Pointing Your Own Domain to the Site

By default, the site lives at `username.github.io/repository-name`. If you want it at a custom domain like `arjun.dev` or `portfolio.arjun.in`, GitHub Pages supports this.

The setup involves two steps:

**On your domain registrar:** Add a `CNAME` record pointing your domain to `username.github.io`.

**On the repository:** Add a file called `CNAME` (no extension) to the root of the repository, containing just the custom domain on a single line:

```
portfolio.arjun.dev
```

In the GitHub Pages settings, enter the custom domain in the "Custom domain" field. GitHub verifies the DNS record and switches the site to the custom URL. It also provisions an HTTPS certificate automatically.

This is an optional step -- the `github.io` URL works perfectly well for a portfolio. But it is useful to know the option exists, especially once you purchase a personal domain.

### Where GitHub Pages Reaches Its Limits

GitHub Pages is excellent for what it does -- and that scope is clearly defined. It serves static files. That is all.

As soon as a project needs anything beyond that, GitHub Pages is the wrong tool. Here are the specific situations where it stops working:

#### Server-Side Code

Any project that runs a backend -- a Node.js Express server, a Python Flask app, a PHP script -- cannot be hosted on GitHub Pages. These require a server process running continuously. GitHub Pages has no such concept. It only delivers files.

If Arjun wanted to add a contact form that emails him when submitted, the email-sending logic would need a server. GitHub Pages cannot run it.

#### Environment Variables and API Keys

Suppose Arjun adds a feature to his portfolio that fetches data from an API, and that API requires a key. He learned not to commit the key to the repository. But on GitHub Pages, there is no way to inject environment variables at runtime. The only place to put the key would be directly in the JavaScript file -- which anyone visiting the site can read in the browser's developer tools.

GitHub Pages has no secrets management. Any value that must stay private cannot be used safely on a GitHub Pages project that calls external APIs.

#### Projects That Require a Build Step

Modern JavaScript frameworks like React, Vue, and Next.js are not plain HTML and JavaScript files. They require a build process -- a command like `npm run build` -- that compiles the source code into the static files a browser can actually use. GitHub Pages can serve the output of that build, but it does not run the build process for you.

Setting this up manually (committing the built files, or using GitHub Actions to run the build) is possible but adds complexity. Platforms designed for modern frameworks handle this automatically.

#### When You Need More

These three situations -- backend code, private environment variables, and build processes -- cover the majority of real-world projects that grow beyond a simple static site. When any of them applies, the right answer is a deployment platform built for that purpose.

This is where **Vercel** comes in. Vercel is a hosting platform designed specifically for modern web projects. It runs your build process automatically on every push, supports environment variables stored securely on the server, can run serverless backend functions alongside your frontend, and deploys in seconds. In the next article, we look at how to deploy the same portfolio to Vercel -- and what that unlocks that GitHub Pages cannot.

### Let's Define Things Now...

> **Deployment:** Publishing a project to a server so that it is accessible at a public URL. Until a project is deployed, it exists only locally or as source code in a repository.

> **Static Site:** A website made entirely of HTML, CSS, and JavaScript files. No server-side processing, no database. Files are delivered to the browser as-is.

> **GitHub Pages:** A free static site hosting service built into GitHub. Reads files from a repository branch and serves them at a public URL. Supports any project that does not require a server.

> **User Site:** A GitHub Pages site served from a repository named `username.github.io`, at the URL `https://username.github.io`. One per GitHub account.

> **Project Site:** A GitHub Pages site served from any repository, at `https://username.github.io/repository-name`. Unlimited per account.

> **Relative Path:** A file path that resolves from the current file's location (`style.css`, `images/photo.jpg`). Required on GitHub Pages project sites to avoid path resolution errors.

> **Case Sensitivity:** GitHub Pages runs on Linux, where `Style.css` and `style.css` are different files. File names and their references in HTML/CSS must match in casing exactly.

> **CNAME File:** A file at the repository root containing a custom domain name. Tells GitHub Pages to serve the site at that domain instead of the default `github.io` URL.

### Activity: Deploy the Portfolio

1. Open your `portfolio` repository on GitHub. Go to **Settings** then **Pages**.

2. Under "Branch", select `main` and `/ (root)`. Click **Save**.

3. Wait about 60 seconds and refresh the page. Copy the live URL that appears.

4. Open the URL in the browser. Does the portfolio render correctly? Check:
   - Does the CSS load? (Page should be styled, not plain text)
   - Do images load?
   - Does the JavaScript work?

5. If anything is missing, open DevTools (F12) and check the Console and Network tabs. Look for 404 errors. For each 404, find the referenced file path in the HTML and correct it to a relative path.

6. Make a small visible change to the portfolio -- update the heading text or background color. Commit and push:
   ```bash
   git add .
   git commit -m "style: update hero heading text"
   git push
   ```
   Wait two minutes and visit the live URL again. Confirm the change appears.

7. Share the live URL with someone and have them open it on their phone. Confirm it works for a visitor with no connection to your machine or your repository.

## What's Coming Next...

GitHub Pages gives Arjun's portfolio a real public URL, and for a purely static project it works perfectly. But as soon as a project grows to need a build process, private environment variables, or any kind of backend logic, GitHub Pages hits its ceiling.

In the next article, we look at Vercel -- a deployment platform designed for exactly these cases. We will deploy the same portfolio to Vercel, see how it handles the build process automatically, and set up environment variables so API keys can be used without ever appearing in the source code. This is the deployment workflow used by the majority of production web teams today.
