---
title: "Implement Push Notification Delivery Using Onesignal"
date: "2019-07-22T22:56:36+07:00"
author: "Nhat Pham"
description : "Implement Push Notification Delivery Using Onesignal"
draft: false
categories : ["tech"]
type : "post"
---

Push Notification is one of most important part of a modern website or mobile application nowaday.
It's a communication chanel between users and the apps.
Some of famous push notification services such as [Firebase Cloud Messaging](https://firebase.google.com/) of Google, [SNS (Simple Notification Service)](https://aws.amazon.com/sns/) of Amazon, [Urban Airship](https://www.airship.com/) and so on...  

There are many famous push notification services out there, But in this article, I'm gonna use [OneSignal](https://onesignal.com/). You will learn how to add push notification feature into your application.

{{< fancybox path="https://whatsnewinpublishing.com/wp-content/uploads/2019/03/" file="Push-Notification-1170x780.jpg" caption="Notifications display on iPhone smartphone" gallery="Gallery Name" >}}

## What is OneSignal?

> OneSignal is a hight volume and reliable push notification service for websites and mobile applications.  
> It supports all major native and mobile platform by providing dedicated SDKs for each platform, a RESTful server API and an online dashboard for marketers to design and send push notifications.

One signal has becomes the most widely used push notification service for both web and mobile developers since it has various reasons such as:  

- **Easy to use**: Implementing reliable interfaces to the GCM/FCM (Google), APNS (Apple), and the Web Push protocols is difficult. There are open source projects to do it, but even the best ones break when message quantity begins to exceed 500,000 at a time.  

- **Better Than Competition**: Other notification service lack feature such as *segmentation* , *automatic/triggered notifications*, *tag-variable substitution*, a notification delivery Server REST API, or *detailed reporting tools*. Additionally, other services  often provice low quality or out of date SDKs that do not support new platform features.  

- **Advanced Functionality**: OneSignal provides marketing tools including **A/B notification testing**, segment targeting, variable-substitution, localization, drip marketing and conversion tracking.  

- **Platform Support**: OneSignal provides a single UI and API to deliver message across iOS, Android, Amazon Fire, Windows Phone, Chrome Apps, Amazon Alexa, Safari, Chrome Web and Firefox.  

- **Popular**: OneSignal has over 500,000 registered developers and 200,000 applications and websites.  

## Register OneSignal

OneSignal has three pricing plans: Free, Starter and Pro, [click to see detail](https://onesignal.com/pricing).
The **Free** plan provides serveral useful features such as Rich notification, personalized notifications, Segmentation, A/B Testing, Templates, API Access, Real-Time Reporting and Deliver by Timezone. It's quite enough to discover the benifits of OneSignal before going to deep to advanced features. 

To register an onesignal account, you simply go to [official OneSignal website](https://onesignal.com/) and click signup

## Create your first app

After creating your account, you're redirected to app dashboard. At this page, we click **ADD APP** button and name it, the name is up to you, in my case it's **MyFirstApp**

{{< fancybox path="https://i.imgur.com/" file="DSQhN9V.png" caption="Create your first OneSignal app" gallery="Gallery Name" >}}

Next step, you will have to select one platform for your created app, but you can return to edit more platforms.
In my example, for convenient, I chose Web Push then hit **Next**
{{< fancybox path="https://i.imgur.com/" file="qwxdRsg.png" caption="Select one platform" gallery="Gallery Name" >}}

This step is about configuring your web push, you may not see this step if you choose another platform.
Since we selected Web Push as app platform, so we need to setup something to our web site can receive the push notifications.

There're there kind of integration here:  
- **Typical Site**: Select this if you are unsure.  
- **Workpress Plugin or Website Builder**: For sites using Workpress, Blogger, Shoptify or other content management system.  
- **Custom Code**: For advanced users who wish to code their web push configuration using Javascript.  

I recommend you choose the typical site integration.  
From section 2, all configuration are optional to you, you can skip them, but it's very useful for your site.  
On the section 2: you will need to input your site name, site url, turn on HTTPS option if your site supports SSL cert.  
{{< fancybox path="https://i.imgur.com/" file="jpGTmt7.png" caption="Site setup" gallery="Gallery Name" >}}
On the section 3: We need to customize an promt which will ask your users at the first time they visit your site.  
On the section 4: we define a default welcome notification which will show to the users when they allow to subscribe to receive your site news or updates.  
{{< fancybox path="https://i.imgur.com/" file="2SxyOHb.png" caption="Permission Prompt Setup and Welcome Notification" gallery="Gallery Name" >}}

On the section 5: There's some advanced config here like Webhooks, Service workers. beside that, we're also able to config the behavior of the notification such as opening a new window tab when users clicking or keep the notification remain util users click it.  

{{< fancybox path="https://i.imgur.com/" file="JthHoeC.png" caption="Advanced options" gallery="Gallery Name" >}}

## Configure Web Push
To integrate OneSignal into your site, you need to download the OneSignal SDK files which based on your selected platform before. Additionally, you must also attach its script into your entry html file. 
{{< fancybox path="https://i.imgur.com/" file="uVNNJ9V.png" caption="Configure Web Push" gallery="Gallery Name" >}}
