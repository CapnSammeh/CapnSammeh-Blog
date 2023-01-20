---
draft: true
title: Over-Engineering a Technical Blog
description: >-
  Using Hugo, Netlify, NetlifyCMS and GitHub Actions to create a self-deploying,
  automatically managed Blog.
date: 2023-01-16T05:48:38.000Z
tags:
  - github
  - development
  - hugo
  - netlify
  - netlifycms
---
{{< mermaid >}} ---
title: Netlify/Hugo - Sam Herring Blog
---

flowchart LR
    hugo[Hugo]
    mainBranch[Main Branch - GitHub]
    contentBranch[Content Branch - GitHub]
    netlify[Netlify]
    netlifyCMS[NetlifyCMS]

    subgraph github[Github Flow]
        contentBranch --Nightly Release--> mainBranch
    end

    subgraph localDev[Local Development]
        direction TB
        contentBranch <--Local Development of Content--> hugo
        hugo[Hugo Local Server] --> contentBranch
    end

    subgraph netlifyHost[Netlify Hosting]
        mainBranch --Create new Build--> netlify
        netlifyCMS --Hosted via--> netlify
        contentBranch <--Creates Content via--> netlifyCMS
    end
 {{< /mermaid >}}
