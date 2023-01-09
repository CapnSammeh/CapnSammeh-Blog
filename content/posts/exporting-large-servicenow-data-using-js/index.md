---
showCMS: true
draft: false
title: Bulk Exporting ServiceNow Data as XML using JavaScript
date: 2023-01-09T05:56:20.186Z
tags:
  - servicenow
---
Recently, I came across a need to export large amounts of information from ServiceNow in XML format - problematically, these records (within *kb_knowledge* specifically) include their own discrete attachment/s, meaning that a single XML file could be 20 -> 30mb in size.

Understandably, this has some impact on the total file size of the table - I suddenly needed to import a 900mb XML file into ServiceNow. 

To avoid needing to do this again, I scoured the internet for some tooling, and came across a few options, but unfortunately none of them suited my needs:

* [Dave's ServiceNow Bulk Export Tool](https://davidmac.pro/posts/2021-07-02-sn-bulk-export/) looked promising, but I needed something I could run in Production without asking for approvals for an API user.
* [SN Community Posts](https://www.servicenow.com/community/now-platform-forum/large-data-exports-csv-xml/m-p/1050824) asking for assistance resulted in the usage of FTP uploads - useful maybe, but not for my use case. 

Ultimately, I decided to resort to good ol' JS to quickly throw together a function I could paste into my browser's console and run to make things nice and simple.

The resulting function has the following "features":
* Inputs for TableName and ServiceNow Instance
* Record Counter to split records evenly by a predetermined amount
* Optional custom encoded query so you can export information you care about. 
* Saving the output as XML directly via the browser.
* Free (?) bugs!

Please find the script snippet below - use at your own peril!

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
    link.click();

    fetch(`${urlObj.toString()}`)
        // Get the Response Body as text
        .then(resp => resp.text())

        // Convert the HTML Body to XML - if it's null, then we don't have to run it
        .then(text => new DOMParser().parseFromString(text, "text/xml"))

        // From our Parsed XML, get the last XML element
        .then(parsedXML => {
            var returnedXMLNodeCount = parsedXML.getElementsByTagName('sys_id').length;
            if (returnedXMLNodeCount == 0) {
                return new Error;
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
