---
draft: false
branch: false
title: Splitting ServiceNow Export XML Files
description: How I chunked up a massive XML file into more manageable bites
date: 2023-01-09T04:51:01.215Z
tags:
  - servicenow
thumbnail: featured.jpg
---
I've had some pretty frustrating experiences with ServiceNow Data Exports lately (see [some of my other exploits](https://samherring.com/posts/exporting-large-servicenow-data-using-js/) if you're interested in knowing more), and one of the challenges we came across was the need to split out a massive XML file. 

There are a few ways to go about this, but one of the biggest roadblocks I faced with this particular situation was that we *were unable to use anything that wasn't installed on a vanilla Windows PC*. 

For your amusement (and if you're lucky, only that), this is the script we used to split one 900mb XML file into as many smaller components as needed (in our case, 100 XML records per file).

```powershell
$fileCount=1
$recordCount=0

$inputFile='Massive ServiceNow File.xml'

$recordBreak='<u_kb_template_custom_template action="INSERT_OR_UPDATE">'
$regex=$recordBreak

$outputPath = "$PWD\new_output3"

$OutputFile = New-Item -path $outputPath -Name "0.xml" -type "file"

switch -regex (Get-Content -Path $inputFile -Encoding 'utf8') {
    $regex {
        if($recordCount -eq 100){
            Add-Content -Path $OutputFile -Value '</unload>'
            $NewFile = "$fileCount" + '.xml'
            $OutputFile = New-Item -path $outputPath -Name $NewFile -type "file"
            Add-Content -Path $OutputFile -Value '<?xml version="1.0" encoding="UTF-8"?>
<unload unload_date="2022-12-22 23:43:06">'
            Add-Content -Path $OutputFile -Value $recordBreak -Encoding 'utf8'
            $fileCount++
            $recordCount = 0
        } else {
            Add-Content -Path $OutputFile -Value $_ -Encoding 'utf8'
            $recordCount++
        }
    }
    default {
      Add-Content -Path $OutputFile -Value $_ -Encoding 'utf8'
    }
  }
```
