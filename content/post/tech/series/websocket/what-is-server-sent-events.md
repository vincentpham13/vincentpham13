---
title: "What Is Server-Sent Events?" # Title of the blog post.
date: 2021-01-08T16:30:07+07:00 # Date of post creation.
description: "Article description." # Description used for search engine.
featured: true # Sets if post is a featured post, making appear on the home page side bar.
draft: true # Sets whether to render this page. Draft of true will not be rendered.
toc: false # Controls if a table of contents should be generated for first-level links automatically.
# menu: main
thumbnail: "https://png.pngtree.com/png-clipart/20190619/original/pngtree-vector-server--icon-png-image_3990001.jpg" # Sets thumbnail image appearing inside card on homepage.
shareImage: "/images/path/share.png" # Designate a separate image for social media sharing.
codeMaxLines: 10 # Override global value for how many lines within a code block before auto-collapsing.
codeLineNumbers: false # Override global value for showing of line numbers within code block.
figurePositionShow: true # Override global value for showing the figure label.
categories:
  - Technology
series:
  - websocket 
tags:
  - websocket
  - event source
  - SSE
comment: false # Disable comment if false.
---
<!-- **Insert Lead paragraph here.** -->
# About Server-Sent Events
SSE stands for Server-Sent Events, It's a technique for sending messages to client from server-sent API, which allows the client to update the changes sent from server by leveraging the Javascript [`EventSource`](https://developer.mozilla.org/en-US/docs/Web/API/EventSource) interface.

![Client Server In SSE technique :inline](https://i.imgur.com/VtYu9j0.png)

EventSource interface allows us to open a persistent, one-direction connection to the server over HTTP protocol by specifying a `text/event-stream` header and listen for incomming messages.
There serveral use cases that making choice to useSSE is well-suited such as when you don't need to send to the server any data. for example, a Twitter-style news feed or a real-time dashboard of stock quotes, notifications to browser.


| Pros                                                        | Cons                                                                    |
| ----------------------------------------------------------- | ----------------------------------------------------------------------- |
| Well-suited for apps that don't need to sent data to server | Not supported by older browsers                                         |
| Work over HTTP, so the API is relatively easy to use        | Most browsers limit the number of SSE connections made at the same time |
|                                                             | Only allows data reception from the server (unidirectional)             |
|                                                             | Events are limited to UTF-8 encoding (no binary data)                   |
# Prerequisites
- Node.js for the server side
- Express.js for web application
- React.js (hooks is reconmmended) for the client side

# Getting started
### React Web App - Frontend
Firstly, I recommend you to use this project boilerplate to skip stuffs that out of this article. Checkout my [react-node-server](https://github.com/vincentpham13/react-node-server.git) or you can setup your own coding environtment.

The project structure looks like this

```
├── node_modules
├── src
│   ├── App.js
│   ├── Home.js
│   ├── Login.js
│   ├── history.js
│   ├── server.js
└── package.json
```



# Conclusion

Download {{% button href="https://github.com/vincentpham13/react-node-server" icon="fas fa-download" icon-position="left" %}}The completed source code here{{% /button %}}
