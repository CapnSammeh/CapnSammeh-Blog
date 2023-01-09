---
showCMS: true
draft: true
title: Exporting Large ServiceNow Data using JS
date: 2023-01-09T05:56:20.186Z
tags:
  - servicenow
---
```javascript
function getElements(url) {
    var url = new URL(url);
    var urlString = url.toString();
    console.log(urlString);

    var link = document.createElement('a');
    var filePath = `${urlString}.xml`;
    link.href = filePath;
    link.download = filePath.substr(filePath.lastIndexOf('/') + 1);
    link.click();

    var getContent = fetch(`${url.toString()}`)
    getContent.then(resp => { return resp.text() })
        //Convert the HTML Body to XML
        .then(text => { return new DOMParser().parseFromString(text, "text/xml") })
        // Get last XML Element
        .then(parsedXML => { return parsedXML.getElementsByTagName('sys_id')[0] })
        //
        .then(element => {
            lastElementID = element.innerHTML;
            console.log(`Last Sys ID: ${lastElementID}`);

            if (url.searchParams.get('sysparm_query')) {
                //If the URL contains the last sys_id, we've already run this and we shouldn't run it again
                if ((url.searchParams.get('sysparm_query').includes(`${lastElementID}`))) {
                    console.log("No More Records to process, ending")
                    exit
                }

                if ((url.searchParams.get('sysparm_query').includes('sys_id'))) {
                    console.log("found sys_id in the params")
                    url.searchParams.set("sysparm_query", `sys_id>${lastElementID}`);
                    getElements(`${url.toString()}`)
                    // getElements(newURL.toString())
                }
            } else {
                console.log("no sys_id in params")
                url.searchParams.set('sysparm_query', `sys_id>${lastElementID}`)
                getElements(`${url.toString()}`)
            }
        }, err => {
            console.log(err);
            console.log("No more records to show")
        })
}
        
var url = 'https://cbasmtdev.service-now.com/.do?sysparm_record_count=500&sysparm_orderby=sys_id&XML'
getElements(url);
```
