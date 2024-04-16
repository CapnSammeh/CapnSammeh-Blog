---
showComments: true
draft: true
title: Using Custom Icons for UI Builder Buttons
description: Extending beyond ServiceNow's FontAwesome Icons
date: 2024-04-12T08:39:00.000Z
tags:
  - servicenow
  - ui
  - ui-builder
  - development
  - developer
thumbnail: "{{slug}}/featured.jpg"
---
We're going to upload the following image to ServiceNow to use for custom UI Builder buttons: 

<https://icons8.com/icon/26028/networking-manager>

1. As an Administrator within your instance, access the System UI -> Images menu item so you can upload your new iconography

![](https://res.cloudinary.com/capnsammeh/image/upload/c_scale,f_auto,q_auto,w_400/v1713165764/CleanShot_2024-04-15_at_17.21.52_2x_o2akxl.png)

2. When uploading the file, note that the Category isn't mandatory (nor does it impact this use case), however the Name is very important - this is how we'll be accessing the file later. 

![](https://res.cloudinary.com/capnsammeh/image/upload/c_scale,f_auto,q_auto,w_400/v1713165927/CleanShot_2024-04-15_at_17.25.03_2x_xuolqc.png)

3. Now, we can navigate to our UI Builder page that we plan on adding the Button to - for this use-case, we've created a new Page using the *Standard Record* template, and we want to add the button up next to the Save Icon (for some reason). 

![](https://res.cloudinary.com/capnsammeh/image/upload/c_scale,f_auto,q_auto,w_400/v1713166078/CleanShot_2024-04-15_at_17.27.00_2x_ev2liu.png)

4. Add the *Button Iconic* component to the page in the correct configuration, and go ahead configuring it however you like - noting that if we want an Icon we can just click, the ***Bare*** configuration should be specified.

![](https://res.cloudinary.com/capnsammeh/image/upload/c_scale,f_auto,q_auto,w_400/v1713166205/CleanShot_2024-04-15_at_17.29.17_2x_leqfhk.png)

5. Click the <> icon that's visible when hovering over the Icon field to tell the platform we want to derive the value of this field from a Script - here, we can simply return the path of the Image we want to reference (see the code block below):

![](https://res.cloudinary.com/capnsammeh/image/upload/c_scale,f_auto,q_auto,w_400/v1713166210/CleanShot_2024-04-15_at_17.29.45_2x_kop5xy.png)



```javascript
/**
  * @param {params} params
  * @param {api} params.api
  * @param {TransformApiHelpers} params.helpers
  */
function evaluateProperty({api, helpers}) {
	return '/Networking-Manager.png';
}s
```
