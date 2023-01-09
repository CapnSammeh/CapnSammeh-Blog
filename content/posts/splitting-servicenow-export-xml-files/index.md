---
showCMS: true
draft: true
title: Splitting ServiceNow Export XML Files
date: 2023-01-09T04:51:01.215Z
tags:
  - servicenow
---


```powershell
$fileCount=1
$recordCount=0

$inputFile='Sidekick Attachments.xml'

$recordBreak='<u_kb_template_cba_standard action="INSERT_OR_UPDATE">'
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
