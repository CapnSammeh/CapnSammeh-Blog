---
showComments: true
draft: true
title: Test
date: 2023-01-16T10:26:44.043Z
thumbnail: '{{slug}}/featured.jpg'
---


{{< mermaid >}} sequenceDiagram
    participant SN as ServiceNow
    participant User
    participant DirectoryAPI

    User ->> SN: Access Sidekick Portal
    SN ->> User: Sidekick Portal Client Code
    User ->> User: Client Code Runs in Browser
    User ->> User: Needs to query DirectoryAPI
    User ->> SN: Widget invokes ServiceNow Bridge API
    SN ->> SN: Is there a valid token for this user?
    alt no valid token
        SN ->> SN: Generate a new Bearer Token
    else valid token
        SN ->> SN: Test2
end
 {{< /mermaid >}}
