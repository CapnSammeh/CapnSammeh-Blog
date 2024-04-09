---
showComments: true
draft: true
title: Writing to Journal Records from Scoped Applications
date: 2024-04-10T07:47:00.000Z
thumbnail: "{{slug}}/featured.jpg"
---
1. Update the Journal Entry table to allow for record creation and update.
2. Create a new X-Scope Permission that allows your scoped application to access the GlideRecord.put function. 
3. Invalidate Cache

Note, this doesn't allow you to fudge the Number of a record due to [this KBA](https://support.servicenow.com/kb?id=kb_article_view&sysparm_article=KB0788878).


```javascript
var IAP_JournalHelper = Class.create();
IAP_JournalHelper.prototype = {
    initialize: function() {},

    /**
     * 
     * @param tableName label of the target Table.
     * @param sysID SysID of the Record within the table
     * @param fieldName Journal field details, either work_notes or comments
     * @param details Contents of the Journal Record
     * @param user String of the user name of the user creating the record. Using an email address will show the user's details if they're in the Sys_User table. 
     * @returns GlideRecord Object of created Journal Field 
     */
    createJournalEntry: function(tableName, sysID, fieldName, details, user) {
        
		//Initialize a Sys Journal Field Record
		var workNote = new GlideRecord("sys_journal_field");
        
		//Turn off the Auto Sys Fields so we can specify the User.
		workNote.autoSysFields(false);

		//Table Name
        workNote.name = tableName;
        
		//Field Name
		workNote.element = fieldName;
        
        //Table SysID
        workNote.element_id = sysID;
        
        //Comment Details
        workNote.value = details;
        
        //Ensure the record has an appropriate timestamp.
        workNote.sys_created_on = new GlideDateTime();
        
        //Created By User
        workNote.sys_created_by = user;

        //Insert the record into the table.
        workNote.insert();

        return(workNote);
    },

    /**
     * 
     * @param tableName label of the target Table
     * @param elementId field name on the target table
     * @param element work_notes or comments
     * @returns Array of Journal Entries that match the query params
     */
    returnJournalEntry: function(tableName, elementId, element) {
        var workNoteQ = new GlideRecord("sys_journal_field");
        workNoteQ.addQuery("name", tableName);
        workNoteQ.addQuery("element_id", elementId);
        workNoteQ.addQuery("element", element)
        workNoteQ.orderByDesc("sys_created_on");

        workNoteQ.query();
        var outputArray = [];

        while (workNoteQ.next()){
            outputArray.push({
                "sys_id": workNoteQ.getUniqueValue,
                "value": workNoteQ.getValue("value"),
                "sys_created_on": workNoteQ.getValue("sys_created_on"),
                "sys_created_by": workNoteQ.getValue("sys_created_by")
                });
            
        }

        return outputArray;
    },

    type: 'IAP_JournalHelper'
};
```
