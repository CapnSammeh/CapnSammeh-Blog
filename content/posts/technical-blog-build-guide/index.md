---
showComments: true
draft: true
title: Technical Blog Build Guide
date: 2024-04-10T07:44:00.000Z
thumbnail: "{{slug}}/featured.jpg"
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
