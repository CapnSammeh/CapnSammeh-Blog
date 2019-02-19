---
title: "Deploying Hugo via Netlify"
date: 2019-02-19T16:23:32+10:00
draft: false
---

Hugo is a fantastic way to quickly deploy a themed website; but unfortunately the guides to hosting a Hugo-Driven Site (in my person case, *this blog*) are either out of date, or a little too vague to follow along with without an in-depth understanding of both Hugo and Netlify.

Luckily for you, I've done the hard work for you.

# Deploying a Blog via Netlify with Hugo

There are 3 major components that are required in order to achieve your goal:
1. GitHub Account
2. Netlify Account
3. A Solid Text Editor (I recommend Atom)

## General Overview

The general overview of this process is as follows:

1. First, create a place to store of the pre-built Hugo Files (we use GitHub in this example).
2. Deploy Hugo to our local machine and build out a sample site.
3. Find a Theme we like and fork it (this allows us to make changes to the theme as we deem appropriate).
4. Create a Netlify Application and hook it into out Hugo GitHub Repo.
5. Give Netlify some idea as to how it should build out the contents of the Repo
6. Start creating posts by simply pushing commits to your Repo.

## Creating a GitHub Repo

This is the first step towards ~~total world domination~~ having a nice, easily updated blog; you need a base to store all of the required information behind building your site; GitHub is **literally** built for this.

Create a new Repository in GitHub and pull it down to your local machine. This folder is now the site that you'll be working out of. 

Once the directory has been pulled locally, you can start the next part of the process

## Installing Hugo

I personally found the directions available on the [Hugo Website](https://gohugo.io/getting-started/installing/) **immensely** helpful, and I would **highly** recommend following those steps.

In short, install the hugo.exe to a safe location (*C:/Hugo/hugo.exe* for example), open PowerShell or Command Prompt and then navigate to your Pulled Repo Location (*C:/Users/CapnSammeh/GitHub/CapnSammeh-Blog* for example).

## Initializing your Hugo Site

Provided that Hugo has been installed as an environment variable, you should now be able to run Hugo from any location on your machine. Run 

`hugo version` 

to check if Windows has any issues running the Hugo Executable.

If you're not experiencing any issues, now's the time to deploy your site. run the following code from the directory above your pulled repo to allow Hugo to create the pre-requisite files:

`hugo new site INSERT_REPO_FOLDER_NAME_HERE`

Once Hugo does it's thing, it's time to push up your initalized files...

### But wait! What about all of these empty directories?!

GitHub doesn't capture changes made to empty directories, and Hugo is mostly empty folders at this point. In order to prevent losing these folders (in case anything happens to your locally stored copy of the site, or if you want to work from another machine and simply pull all of the files), we need to add a .gitkeep file in the root of each of the following folders:

* archetypes
* content
* data
* layouts
* resources
* static
* themes

I personally went the route of PowerShell to do this:

`Get-ChildItem -Recurse -Directory | ForEach-Object {New-Item .gitkeep}`

This will create a new, 0kb file in each directory underneath your current directory (which should still be the root of the Repo) called .gitkeep. This isn't really a part of Git, but is good practice and a widely adopted method for maintaining empty directories in Git Repositories. 

Once you've created the required files, you can make your initial push up to GitHub.

## Forking/Creating a Custom Theme

Because Netlify will attempt to pull/build any repo that you include in your parent repo, we're unable to simply pull the theme that you're wanting locally; as this limits our ability to make customizations to the theme 

**And we like customization**

So what can we do?

The easiest way around this issue is to Fork a copy of the Repo containing the theme you like and including the fork as a submodule in your main Hugo Repo. This allows you to make changes to the Theme as required, without impacting the original developer.

*Please note, give credit for your theme where credit is due. People work really hard on themes (myself **not** included) and they deserve credit or a coffee.*

The theme used here for [The Capn's Log](http://capnsammeh.tk) is kindly provided by [vaga](https://github.com/vaga) and can be found [https://github.com/vaga/hugo-theme-m10c](https://github.com/vaga/hugo-theme-m10c).

Once forked, grab the .GIT URL from GitHub and jump back into PowerShell.

We want to add your newly forked Theme as a submodule of the Hugo Repo so it's built every time the site is deployed on Netlify. 

To do this, simply run the following code from PowerShell:

`git submodule add [INSERT YOUR .GIT URL HERE] themes\[THEME NAME]`

This will add some files to your .git folder, as well as creating a new file called .gitmodules in the root of the Repo. When running Git Commands on a windows machine, sometimes the files are incorrectly named/strucutred. Change your .gitmodules file so it looks like the following:

```.gitmodules
[submodule "themes/m10c"]
	path = themes/m10c
	url = https://github.com/CapnSammeh/hugo-theme-m10c
```

Once completed, push your changes up to the Repo.

## Creating Netlify.TOML 



## Making Changes to Config.TOML



## Linking GitHub and Netlify

Linking your Repository and Netlify is as simple as logging into Netlify using your GitHub Credentials, authenticating into GitHub and selecting the appropriate Repo/Branch to Deploy from. 

Click *New Site from GitHub* and select the Repo we've been working with. On the next page, you'll be able to select a Branch (use Master here unless you've been working in a Branch), and you should see that the build information is pre-specified; this is because we defined that information in our Netlify.TOML file in the previous step.

Pressing Deploy will start the build process and you should see that once it completes, you can navigate to the URL Netlify have provided you with and see your content **LIVE AND ONLINE**!

## Creating Content