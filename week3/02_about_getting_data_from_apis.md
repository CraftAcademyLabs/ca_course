# About API's in the context of the Web

API is technically short for "Application Programming Interface", and the term is tossed around in the programming industry so much, the overall complexity of what they are, and how they can make your life easier - or a living hell gets overlooked.

Some definitions:

"a set of programming instructions and standards for accessing a web-based software application or web tool"

"a set of functions and procedures allowing the creation of applications that access the features or data of an operating system, application, or other service."

"...to provide a way to connect computer software components together"



Some time ago, it became a big thing for web startup to offer public access to some of their internal data through a web service API, typically using REST and JSON, thus allowing third-party developers to integrate with their systems. Web developers started using the term "API" to mean specifically (and only) "publically accessible web service", and misusing it to include the implementation thereof. Okay, we will be working on this a lot moving forward, so today is NOT the day for a deep dive into this concept and becoming experst. Today is the day when we will make use of an API for the first time, and later, we will disect what occured and go over some of the moving parts.

Okay, we will be working on this a lot moving forward, so today is NOT the day for a deep dive into this concept and becoming experts. Today is the day when we will make use of an API for the first time, and later, we will disect what occured and go over some of the moving parts.

In typical web application architecture we have frontend/client side(detached from the backend) and server side/backend. Normally the backend and frontend run on different servers with different domain names may be in different locations too. They should be able to communicate with each other to share information. Frontend is responsible for capturing the user inputs then validate and send it to the server and also fetch and display the data on browser.The backend is responsible for storing and retrieving data from the database.

We will make use of one API that is public and allow us to fetch some data (we often use the expression "provides API endpoints" - I will explain this expression further down the line.)

Let's execute this in our browsers console or in the Node console in the terminal:

```
const xhr = new XMLHttpRequest();
xhr.open('GET', 'https://reqres.in/api/users')
xhr.onload = () => {
  const data = JSON.parse(xhr.responseText)
  console.table(data.data)
}
xhr.send()
```

You can also try this in the Node console in your terminal, but XMLHttpRequest is a built-in object in web browsers only. It is **not** distributed with Node; you have to install it separately,

Install it with npm:

```
$ npm install xmlhttprequest
```
Now you can require it in your code.

```
const XMLHttpRequest = require("xmlhttprequest").XMLHttpRequest;
const xhr = new XMLHttpRequest();
// ...and the rest of the snippet
```

You can play around with these commands in your browser and the traminal for a bit.

