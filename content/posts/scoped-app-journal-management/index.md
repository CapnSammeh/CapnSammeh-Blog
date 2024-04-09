---
showComments: true
draft: true
title: Writing to Journal Records from Scoped Applications
date: 2024-04-09T11:15:00.000Z
thumbnail: ""
---
```javascript
var workNote = new GlideRecord("sys_journal_field");
workNote.autoSysFields(false);

workNote.name = "incident";
workNote.element = "work_notes";
workNote.element_id = "b720d086dbc9065075275ec5f39619e7";
workNote.value = "This is a sample Work Note";
workNote.sys_created_on = new GlideDateTime();
workNote.sys_created_by = "System";

workNote.insert();
```
