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

In the Home functional component, we initiate an instance of EventSource by using `new` operator and passing to it an URL. The URL is API endpoint that we will create later. This example is to display messages from server in every single 1 second, so I name the API path something likes `/stream-random-number`.

You can optionaly specify should the api

EventSource interface also provides us useful events to capture the stage of the request like `onopen`, `onmessage` and  `onerror`.

I gonna use the `useState` hook to create a state to hold the list of returned messages and a method to update that state.

Each time the `message` event is triggered, we can get the message from `MessageEvent` argument which inside the callback function.

```javascript
const [listNumbers, setListNumbers] = useState([]);

useEffect(() => {
    const eventSource = new EventSource(`${baseUrl}/stream-random-number`, { withCredentials: true });
    eventSource.onopen = (e) => {
      console.log('The connection was established');
    }
    eventSource.onmessage = (messageEvent) => {
      setListNumbers([
        ...listNumbers,
        messageEvent.data,
      ]);
    }
  }, []);
```
#### Error Handling
When a problem occurs such as network timout or reloading page, we can capture this error using on `error` event.
```javascript
eventSource.onerror = (error) => {
  console.log('The connection was closed', error.message);
}
```

### Closing Event Stream
We can explicily terminate the connection by using the `close` method of EventSource.
```javascript
eventSource.close(); // close connection
```
**Note:** You can optionally use `addEventListener` to handle these above events, there's no difference between these two types.

eg:
```javascript
eventSource.addEventListener('message', (messageEvent) => {
      // do some stuffs
});
```

At this stage, if you take a quick look at the browser console, you will might we the following results
![](https://i.imgur.com/kfuevus.png)

The first red error log indicates that our server does not configure header for this request efficently, therefore, the `The connection was closed` message was displayed obviously.

### Express Server - Backend
The server-side script that send events that need to response with MIME type `text/event-stream`.  
Messages in the event stream are separated by a pair of newline characters. A colon as the first character of a line is in essence a comment, and is ignored.
eg:


- `event: custom\n\ndata: this is the message with custom event\n\n`

#### Fields Of A Message
A message can have more than one field in it or event no field
- `data`: the data field for a message, a message can have many data fields which seperated by a newline.  
for example:  
**data: this is message A`\n`data: this is also belong to message A`\n\n`**  
- `event`: the type of event, if this field is specified, you must use `addEventListener()` to listen to this given type of event, otherwise, `onmessage` will handle it.  
for example:  
**event: custom`\n`data: this is message A\ndata: this also belong to message A`\n\n`**  
Next step, we need to initiate a server that handle the `/stream-random-numbers` API, I decided to choose Express.js since it's quite simple to setup.
Open the `serser.js` file inside the boilerplate and create a new route.

```javascript
app.get('/stream-random-number', cors(corsOptions), (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Connection', 'keep-alive');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Allow-Control-Allow-Credentials', true);

  res.flushHeaders();
  const interval = setInterval(() => {
    const randomNumber = Math.floor(Math.random() * 100);
    const message = {
      id: new Date().getTime(),
      number: randomNumber,
    };

    const response = `event: random_number\ndata: ${JSON.stringify(message)}\n\n`;
    res.write(response);
  }, 1000);

  res.on('close', () => {
    clearInterval(interval);

    res.end();
  });
});
```

This example, we leverage `setInterval` function to send a message every 1 second. And an unforgetable thing is termitating the created interval when `close` event is triggered for some reasons.  Note that you must clear the interval before returning the http response.  
# Conclusion

Download {{% button href="https://github.com/vincentpham13/react-node-server" icon="fas fa-download" icon-position="left" %}}The completed source code here{{% /button %}}