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

Netlify has this [wonderfully documented](https://www.netlify.com/docs/netlify-toml-reference/) .TOML File (aptly named *netlify.toml*) that allows you to configure the way that Netlify will build and deploy your site.

Because we're using Hugo to build the site, Netlify needs to be provided with some information on how it should build the contents of your Repo into a fully-fledged Hugo-Driven Site. 

Create a new file in the root of your project called *netlify.toml*; within this file, put the following lines of markup:

```
[build]
publish = "public"
command = "hugo --gc --minify"

[context.production.environment]
HUGO_VERSION = "0.54.0"
```

To explain:

```
[build]
```

Settings listed under the build context are global, and are applied to all contexts unless otherwised specified.

Basically, this means that unless you specify a context (like Production or Development), these settings will be used throughout your deployment for all deployment types. Because we're not really doing any staging of this site (Yet!), this is all we need for now.



```
publish = 'public'
command = "hugo --gc --minify"
```

Provides Netlify with the build instructions; in this case, that includes publishing the output of the command to the 'Public' directory; this is pretty standard in Netlify-land. 



Command is the exact command being used to build out the site; 

​	--gc (Presumably Garbage-Clean) enables Hugo to run some cleanup activities to remove unused files after the build.

​	--minify allows for minification support. For more information on Minification, see [here](https://gohugo.io/hugo-pipes/minification/) and [here](https://developers.google.com/speed/docs/insights/MinifyResources). 



```toml
[context.production.environment]
HUGO_VERSION = "0.54.0"
```

The context declaration is largely unnecessary at this stage, but is nice to have for now for when we deploy a Development and Testing Branch of Hugo, potentially running previews of the newest build.

The HUGO_VERSION line gives Netlify an idea as to what version of the application to pull and build in order to run our command. At the time of writing, the most stable release of Hugo is 0.54.0, hence the version number. 

Once this file has been populated, feel free to push this to your repo.


## Making Changes to Config.TOML

This one is a bit tough, as it's very dependent on the Theme that you're using, and what variables it's expecting. At the very least, your Config.TOML File (the file that Hugo uses for configuring your site), should contain the following fields:

```toml
baseURL = "/"
languageCode = "en-us"
title = "The Capn's Log"
```

Ensure the Language Code and Title are to your liking (the Title is the name of your site, and language code is pretty obvious). 

**IMPORTANT: ** Make sure you set the baseURL of the site to "/"! This ensures that the URLs that are built using your theme point to the current site, providing you with the ability to change the Domain Name as required. 

Once complete, push the newly created file to your repo. 

## Linking GitHub and Netlify

Linking your Repository and Netlify is as simple as logging into Netlify using your GitHub Credentials, authenticating into GitHub and selecting the appropriate Repo/Branch to Deploy from. 

Click *New Site from GitHub* and select the Repo we've been working with. On the next page, you'll be able to select a Branch (use Master here unless you've been working in a Branch), and you should see that the build information is pre-specified; this is because we defined that information in our Netlify.TOML file in the previous step.

Pressing Deploy will start the build process and you should see that once it completes, you can navigate to the URL Netlify have provided you with and see your content **LIVE AND ONLINE**!

## Creating Content

Creating Content in Hugo is a simple as pulling the newest version of the repo to your workspace of choosing; ensuring Hugo is pre-installed, and using Hugo in the directory to create a new post using the following command:

```powershell
hugo new posts/post_name.md
```

Once your .MD file has been created, using your Text Editor of choice (Atom is a great all-rounder, but Typora is an excellent Markdown Editor if you want something a bit cleaner), create your content!

If you're happy to publish the file directly to the site, ensure that the file isn't in draft (Draft = False in the header of the page) and commit/push your changes. Netlify should see the change to your Repo and rebuild the site using your newly added content!