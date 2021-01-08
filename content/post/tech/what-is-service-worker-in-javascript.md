---
title: "What Is Service Worker in Javascript?"
date: "2019-08-03T11:16:55+07:00"
author: "Nhat Pham"
description : "service workker"
draft: false
categories : ["tech"]
type : "post"
---
{{< fancybox path="https://cdn-images-1.medium.com/max/1000" file="1*oOcY2Gn-LVt1h-e9xOv5oA.jpeg" caption="Service worker" gallery="Gallery Name" >}}

A service worker is simply a script (Javascript file) that run on background of the browser.
A service worker has the following characters:  

- Does not relevant to a specific web site.  
- Can not interact directly with DOM nor the webpage.  
- Supports only HTTPS.  

Although it run on a different thread but it can communicate with webpage through postMessage method.  
It also takes advanced of [background synchronization](https://developers.google.com/web/updates/2015/12/background-sync).  
With service worker, your website won't be interupted when internet connection is not available.

### Service worker VS Web Worker
Let's find out some different things between service worker and web worker:  

| Type | Purpose | Webpage communication |
| -------- | -------- | -------- |
| Service Worker | is designed to handle network request and assist offline first development| go through service workers PostMessage emthod|
| Web Worker| takes advanced of multi-threading model to deal with complex data or processor intensive tasks in the background | go through web workers PostMessage method|

### Service Worker Lifecycle  
There're 3 stages in lifecycle of a service worker:  

{{<mermaid align="left">}}
graph LR;
	  A((No Service Worker)) --> B(Install Event)
    B --> C(Activate Event)
    C --> D(Idle)
    D --> E[Fetch Event]
    E --> D
    D --> F[Stoped]
    F --> D

{{< /mermaid >}}

### Register A Service Worker
To register a service worker, we first check if the browser supports it then register it:

```javascript
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('service-worker.js')
  .then((response) => {
    console.log('Registers service worker successfully');
  }, (error) => {
    console.log('Failed to register service worker');
  });
}

```

`register` function require a path to your service worker file


{{% notice info %}}
An information disclaimer
{{% /notice %}}


{{% button href="https://getgrav.org/" icon="fas fa-download" icon-position="left" %}}Download ServiceWorker file{{% /button %}}
