---
showComments: true
draft: true
title: Test
date: 2023-01-16T10:26:44.043Z
thumbnail: '{{slug}}/featured.jpg'
---


{{< mermaid >}} sequenceDiagram

participantSNasServiceNow

participantUser

participantDirectoryAPI



User->>SN:Access Sidekick Portal

SN->>User:Sidekick Portal Client Code

User->>User:Client Code Runs in Browser

User->>User:Needs to query DirectoryAPI

User->>SN:Widget invokes ServiceNow Bridge API

SN->>SN:Is there a valid token for this user?

altnovalidtoken

SN->>SN:Generate a new Bearer Token

elsevalidtoken

SN->>SN:Test2

end {{< /mermaid >}}
