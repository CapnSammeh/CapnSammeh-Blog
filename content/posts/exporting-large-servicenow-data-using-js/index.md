---
showCMS: true
draft: true
title: Exporting Large ServiceNow Data using JS
date: 2023-01-09T05:56:20.186Z
tags:
  - servicenow
---
```javascript
/**
 * Batch ServiceNow XML Downloader
 * @param {string} tableName ServiceNow Table Name
 * @param {string} instanceName ServiceNow Instance Name
 * @param {int} recordCount Number of records to include
 * @param {string} queryParam ServiceNow Encoded Query
 */
function bulkDownload(tableName, instanceName, recordCount, queryParam) {

    var urlObj = new URL(`https://${instanceName}.service-now.com/${tableName}_list.do?sysparm_record_count=${recordCount}&XML&sysparm_order_by=sys_id&sysparm_query=${queryParam}`);
    console.log('---')
    console.log(urlObj.toString());

    var link = document.createElement('a');
    var filePath = `${urlObj.toString()}.xml`;
    link.href = filePath;
    link.download = filePath;
    // link.click();

    fetch(`${urlObj.toString()}`)
        // Get the Response Body as text
        .then(resp => resp.text())

        // Convert the HTML Body to XML - if it's null, then we don't have to run it
        .then(text => new DOMParser().parseFromString(text, "text/xml"))

        // From our Parsed XML, get the last XML element
        .then(parsedXML => {
            var returnedXMLNodeCount = parsedXML.getElementsByTagName('sys_id').length;
            if (returnedXMLNodeCount == 0) {
                console.log("No data found! Exiting...");
                return;
            } else {
                return parsedXML.getElementsByTagName('sys_id')[returnedXMLNodeCount - 1];
            }
        })

        // Now, let's do some logic with it:
        .then(element => {
            var lastElementID = element.innerHTML;
            // First, make sure our search params exist
            if (urlObj.searchParams.get('sysparm_query')) {
                //If the URL contains the last used sys_id, we've already run this and we shouldn't run it again
                if ((urlObj.searchParams.getAll('sysparm_query').findIndex(
                    element => element.includes(`${lastElementID}`)
                )) != -1) {
                    console.log("No More Records to process, ending");
                    return;

                } else if ((urlObj.searchParams.get('sysparm_query').includes('^sys_id>'))) {
                    //If the sysparm_query parameter contains sys_id, then we need to remove it
                    var regex = /(?<=sys_id>)(.*)/;
                    var param = urlObj.searchParams.get('sysparm_query').replace(regex, '');
                    urlObj.searchParams.set("sysparm_query", `${param}${lastElementID}`);
                    bulkDownload(tableName, instanceName, recordCount, urlObj.searchParams.get('sysparm_query'));
                } else {
                    console.log("no sys_id in params");
                    urlObj.searchParams.set('sysparm_query', `${queryParam}^sys_id>${lastElementID}`);
                    bulkDownload(tableName, instanceName, recordCount, urlObj.searchParams.get('sysparm_query'));
                }
            }

        }, err => {
            console.log(err);
            console.log("No more records to show");
        });
}
```