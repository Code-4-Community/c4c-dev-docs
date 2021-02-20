# Week 5: Server, Request, and Backend Basics

## Agenda
- Server Basics
    - What is a Server
    - What is a Client
    - Importance of a Clear Server/Client Interface
    - Hosting and Localhost
- HTTP/REST Basics
    - What is HTTP
    - Status Codes Overview
    - Request Types
    - JSON
- Backend Basics and GET Requests
    - Routing on the Backend
        - How to Make a New Route
        - Static Routing
        - Route Params
    - Using Postman to Call APIs
    
## Basic Plan
1. Servers
    - What is a server?
    - What is a client?
    - Importance of communication
    - Hosting
        - IP Addresses
        - Localhost
        - Ports
        - Ngrok
2. HTTP Requests and REST
    - What is HTTP
        - Status Codes in HTTP
            - Informational Responses
            - Successful Responses
            - Redirect Responses
            - Client Errors
            - Server Errors
        - JSON
            - Values
            - Arrays
            - Objects
        - Request types overview
            - GET
    - What is REST (REpresentational State Transfer)
    - Postman and API clients
3. Routing on the Backend
    - Structure of the Backend
    - What is routing?
    - Routing with Vert.x
        - Static routing
        - Route parameters
        - Query parameters
        - Creating a Sub-Router
    - Processor Interfaces
    
## Servers

The server-client architecture is the way that the web works. Simply put, servers are programs that
await requests sent by client programs (like web browsers), and send back responses.

![Server-client architecture model](img/server-client-model.png)

*Gnome-fs-client.svg: David Vignoni Gnome-fs-server.svg: David Vignoni derivative work: Calimo 
/ LGPL (http://www.gnu.org/licenses/lgpl.html)*

### What is a server?

Servers are the programs that power every website and remote service you use. Servers
are usually just programs that are always-on and listening, whose purpose is to run remote operations 
for you. They handle incoming and outgoing requests, errors, and running functionality you design! They can be run for 
programs that you use locally as well as remotely. 

As an example, when you play video games with other people, that's spinning up a server that handles running everyone's 
game and the interactions that may happen between each player and AI.

There are two main reasons you would want to have a server as part of your application. The first is that
some operations are computationally expensive, and would slow down your users' computers. The second reason is
that you may want to hide parts of the application's design from your users, possibly for security reasons.

### What is a client?

A client is any program that makes requests to servers. Servers can also be clients if they need to 
interact with other servers, but in a lot of cases, they can just be standalone programs too.

From the example of the game above, the client side of that would be the part of the game that you interact with and 
download locally, which ends up sending requests to the (usually remote) server. Another example of a client could be 
your web browser, which sends requests when you try to load websites or send information.

The client acts as a user interface (UI) for the app you're using; it provides a way for a user to give input to your 
app! The user's experience (UX) is heavily tied to both the design of the interface and the workflows you create.
Together, UI/UX make up the user interactivity part of the design process!

### Importance of communication

As you can imagine, with separate programs for the server and client, the interactions need to be carefully planned. 
If, for example, a client is sending data to the server, but the data is missing information or is sent
in a way that is not expected (which is a surprisingly common problem to run into), there are going to be errors. If this 
occurs, then depending on the processes you or your company use for development, going back and fixing this can end up
causing other errors or end up taking valuable time in possibly unplanned work.

### Hosting

Hosting is the act of starting a server and allowing clients to connect to it. These connections are usually 
accessible by hostnames (the URLs you usually use when you go to a website) and IP addresses.

#### IP Addresses

An IP address is an identification number assigned to every device in a computer network which uses the Internet 
Protocol (IP) to communicate and relay information. In most cases, IPv4 (the fourth version) addresses are used, which
usually look like four numbers from 0-255 separated by periods. A couple examples are `192.168.0.1`, `127.0.0.1`, 
`255.255.255.255`, and `8.8.8.8` (This one is actually Google! Try going to [https://8.8.8.8](https://8.8.8.8)). If you
want to see your own IP, try out a [what is my IP website](https://whatismyipaddress.com/).

Often, when you navigate to a website by typing in a name, like [https://c4cneu.com](c4cneu.com), special service called
the Domain Name Service (DNS) translates that text into an IP address. If you're interested, check out the [How Stuff 
Works page](https://computer.howstuffworks.com/dns.htm) on it, but here's a simple image to demonstrate.

![How a DNS lookup works](img/how-dns-works.png)

*How Stuff Works*

#### Localhost

Localhost (or http://127.0.0.1) is the address that locally hosted servers can be accessed at. If you want to access 
something hosted locally, all you have to do is load that `http://127.0.0.1` address or `http://localhost`. Localhost
is actually a 'loopback address', which means that any requests sent to it are routed back to the computer itself, but
for simplicity's sake, you can just think of it as a really quick and simple way to write this computer's address. 
You'll be using this a lot later on when you're running your website locally.

#### Ports

A port is an individual endpoint at a specific address. Each program that wants to act as a server reserves a port
to be used throughout its lifespan, and no two programs can share the same port. They can range from 0 to 65535, and the ones ranging from 0 to 1024 are commonly referred to as
the [well-known port numbers](https://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers#Well-known_ports). These 
include special commonly used things such as the [File Transfer Protocol (FTP, ports 20 and 
21)](https://en.wikipedia.org/wiki/File_Transfer_Protocol), [Secure Shell (SSH, port 22)](https://www.ssh.com/ssh/),
[Simple Mail Transfer Protocol (SMTP, port 25)](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) (a protocol
for sending email), DNS (port 53), (unencrypted) [Hyper Text Transfer Protocol (HTTP, port 
80)](https://developer.mozilla.org/en-US/docs/Web/HTTP), (encrypted) [HTTP Secure (HTTPS, port 
443)](https://developer.mozilla.org/en-US/docs/Glossary/https). When writing about ports, they can be written as either 
"3000" or ":3000" (with the prefixed ":").

HTTP/HTTPS ports is how production websites (the websites that are 'live' for a company) are usually served, but 
for now we'll use our own defined port for testing. You'll see that it's common to run programs on ports outside of the 
well known port range, and ports :3000, :8000, and :8081 (what we'll be using) are quite common. Also, when testing 
locally, HTTPS is usually not used since a certificate has to be created, so just focus on using HTTP for now. We'll 
talk about HTTP more later.

To access a port at a given address, you write the protocol (optional, http is assumed by default), followed by the 
address, followed by the colon-prefixed port. So for example, accessing http on port :4000 locally would look something
like `http://localhost:4000` or `http://127.0.0.1:4000`.

#### Ngrok

When you're hosting on localhost, it's often not easy for someone on a different network to
access your app. This is because of a bunch of firewall rules in place and the structure of the internet, and to fix 
that you will end up having to look into to port-forward your application. An easy 
way to get around that is to look into [Ngrok](https://ngrok.com/), which ends up forwarding traffic through one of 
their servers for your local app and avoids having to deal with those rules. So if you want to show your friends and 
family, download it and check out their [docs](https://ngrok.com/docs). It's really easy and extremely useful!

## HTTP Requests and REST

### What is HTTP

Hypertext Transfer Protocol (HTTP) is a protocol for requests and responses in a server-client architecture model. In
the web browser example from earlier, the client's request to the server is an HTTP request, and the server provides an
HTTP response in a form similar to a string containing returned data.

HTTP provides a few useful pieces of information in both requests and responses that we'll use. Both types of requests 
have *headers* that provide metadata about the request or response, and an optional *body* including any data that 
has to be transferred. Responses additionally have a status code that tells you quickly what happened while processing 
the request.

#### Status Codes in HTTP

Status codes are special numerical codes returned in an HTTP response which indicate the status of the request. There 
are a couple groups of status codes, which define a high level meaning as to what happened during the server.

| Code Range | Description |
| :--- | :--- |
| 100 - 199 | Informational Responses |
| 200 - 299 | Successful Responses |
| 300 - 399 | Redirect Responses |
| 400 - 499 | Client Errors |
| 500 - 599 | Server Errors |

For more info on the responses outside of what's below, check out [MDN's HTTP Status Code 
Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) or the [API REST Tutorial 
Cheat Sheet](https://www.restapitutorial.com/httpstatuscodes.html).

You can also see a lot of these if you open up your browser's developer console and look at the network tab. This 
network tab can give you information about exactly what's going on when you try to load a webpage. Try it out by
right clicking on the page and selecting "Inspect" or "Inspect Element" in Chrome or Firefox. 

In this program, we're only going to be dealing with the 200, 400, and 500 ranges. If you're interested in the 100 or 
300's, go ahead and check it out on the HTTP Status Code Guide above.

##### Successful Responses

Successful responses are responses that indicate success for whatever request was made. The specific response indicates
exactly what kind of success was achieved.

Here are a few of the common ones:

| Code | Name | Description |
| :--- | :--- | :--- |
| 200 | OK | The most common one, indicates that everything went as expected (and data may have been returned in the body) |
| 201 | Created | A new resource has been created on the server |
| 204 | No Content | The request was a success but no body was returned (necessary data *might* be in the headers) |

##### Client Errors

These represent errors that a client has made and are used to let it know what went wrong.

Here are a few of the common ones:

| Code | Name | Description |
| :--- | :--- | :--- |
| 400 | Bad Request | There was an issue with the body of the request (usually due to invalid syntax) | 
| 401 | Unauthorized | The client needs to authenticate (log in) before using this resource |
| 403 | Forbidden | The client is authorized, but doesn't have the right permissions |
| 404 | Not Found | The requested resource could not be found or the URL is not recognized |
| 405 | Method Not Allowed | The wrong method is being used for a route (like a POST vs a GET, will be explained later) |
| 410 | Gone | The requested resource has been permanently deleted from the server |
| 418 | I'm a teapot | The server refuses the attempt to brew coffee (yes this is real) | 

##### Server Errors

These responses let the client know that there was an issue processing the request by the server. Usually you want to 
avoid returning these because you properly handle every error that can pop up, but there are times when it's unavoidable 
or necessary.

Here are a few of the common ones:

| Code | Name | Description |
| :--- | :--- | :--- |
| 500 | Internal Server Error | Usually thrown on an exception the server doesn't know how to handle, you want to try and avoid these |
| 502 | Bad Gateway | The server (acting as a gateway) got an invalid response (usually this happens for certain server configuration errors) |
| 503 | Service Unavailable | The server isn't ready to handle the request (usually when the server is down or overloaded) |
| 504 | Gateway Timeout | When the server (acting as a gateway) could not get a response in a specified timeframe |

#### JSON

JavaScript Object Notation (JSON) is a way of representing data in a very clean and easy to write way. It's commonly 
compared to XML. You can see an in-depth description of everything on the 
[JSON website](https://www.json.org/json-en.html). It's also very similar to an object datatype in JavaScript, and 
there are only a few syntactical differences to worry about.

The JSON data format consists of three main data categories; the `value`, `array`, and `object` types.

##### Values

Values are very simple, and there are only about a few main types: strings, numbers, `true`, `false`,
and `null`. 

##### Arrays

Arrays are an ordered collection of `value`s, arrays (yeah, you can have arrays in arrays!), or objects.

>They're also commonly also referred to as a vector, list, or sequence. 

See examples below:

```json
[]

["a", "b", "c", "d", "e"]

[[], ["a"], ["b", "c"]]

[1, 2, ["a"], "b", true, false, null, [[[[]]]]]
```

##### Objects

Objects are similar to arrays, however, they have string keys that are assigned to each contained item. Just like 
arrays, objects can have other objects (objects in objects just like arrays in arrays!), arrays, and values as the 
items.

>They're also commonly known as serialized versions of an object, record, struct, dictionary, hash (the data type kind), map, hash table, keyed list, or
>associative array if you've heard of any of those before.

>Serialization refers to the act of taking data from a data type or object state and turning it into a format that can
>be stored/transmitted/more easily read and reconstructed later.

>Deserialization refers to taking serialized data and turning it back into its original state.

Here are some examples:

```json
{}

{
  "simple": "object"
}

{
  "key": "value",
  "other key": 5,
  "nested object": {
    "key": true,
    "anotherKey": [1, 2, "a", { "hello": "world" }]
  }
}
```

#### Request Types Overview

In HTTP (and as mentioned previously) there are multiple different request types. A lot of them will be covered in the 
following lesson, so we'll just have a high level overview here.

Each type of request is supposed to represent a different operation in the idea of [Create, Read, Update, Delete 
(CRUD)](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete).
There are a couple outside of those, but in most cases, you'll just stick with those operations.

##### GET

The `GET` method is one of the most commonly used request methods (along with `POST`). It corresponds to the read 
operation of CRUD, so it's usually used when you're trying to just get information from a server. In fact, whenever you
load a website using a web browser, you're sending `GET` requests to the website you want to access. There are a bunch 
of other request methods too (like `POST`, `PUT`, `DELETE`, ...), which we'll be covering next week. Be sure to join 
us then!

### What is REST

Representational State Transfer (REST) is a set of constraints for building web services. RESTful web services, or 
web applications that conform to REST techniques require the following constraints.

- *Server-Client Architecture*: Follows the server-client architecture discussed earlier.
- *Stateless*: The server doesn't store any context about who the client is, so clients are the only ones that know 
about their session state and contain all information necessary to make a request. If the server needs to know any of 
the client context (like authentication information), it should be stored in a database.
- *Savable (Cacheable)*: Responses can be marked as savable or non-savable so that optimizations can be performed for 
scalability and performance.
- *Layered System*: A client shouldn't need to know if it's connected directly to the end server or if there are 
intermediary services.
- *Uniform Interface*: The server's resources and implementation should be decoupled from what is returned to the 
client.

We'll be trying build our servers by following those REST principles, but they're more just guidelines to keep in mind
when designing everything. most servers don't usually follow every rule above super strictly.

### Postman and API Clients

Now that you know some of the basics of web requests and the way that the web works, let's try out some HTTP requests
and inspect the responses. API clients are programs that nicely let you send any kind of request with any information to
whatever URLs you define. Some of the nice ones even let you define setup scripts or post request tests for ease of use
and validation later on.

In terms of being able to be run through the command line and being on almost every computer, 
[curl](https://curl.haxx.se/) is an extremely useful and functional (but quite complicated) tool. Feel free to try it 
out with a command like `curl https://curl.haxx.se/`, and you should see the curl webpage in HTML. If you try 
`curl -o test.html https://curl.haxx.se/`, and then open that file in a web browser, you should see the webpage, but
downloaded!

Working entirely on the command line like that can get really annoying though, so try out one of our favorite API 
clients: [Postman](https://www.postman.com/). In here, you can run almost any type of request, authenticate, set
headers and body (if applicable), run scripts and tests, see the output (in HTML too!), status code, time, size, and
returned cookies and headers! If you find that you end up using a lot of the same requests, you can also save them and 
pull them up later on. We've used this for classes, coop, and personal projects, and hopefully you'll find this useful.

On this docs site, we have an example of how to send a POST request using Postman (which will be covered next week). If 
you're interested in checking it out sooner, you can view it [here](../guides/postrequest.md).

## Routing on the Backend

Now we'll get to setting up the individual routes for the backend of our website so that you can access different 
resources. If you haven't set up the backend yet, go check out the Project Setup section of 
[Week 1](./week-1.md).

### Structure of the Backend

From the root directory, you'll notice that there are three main directories: api, persist, and service. 

The api directory is where all requests will enter through. It is the way that any external clients will interact with
and view the results of our application.

The persist directory is where all *persisting* information will live. Starting out, it is where we'll create our 
database mocks, and later on, it will be where our database interactivity class files will be placed.

The service directory is what unifies everything together. Since we want to not make the persist and api directories 
dependent on each other, there will end up being a few interfaces that are made available and implemented in service. 
This is because some of the operations we want to perform need to know of both persist and api functionality (but 
remember, they shouldn't know about each other). 

Don't worry about having to figure all of this out yourself though, we'll provide some help to get you started. Also,
for those of you who know about the 
[Model-View-Controller](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) design pattern, this 
follows that! In this case, persist is our model (since it stores and holds the data), api is our view (since it's what
the user/external applications use), and service is our controller (since it links everything together and handles
the interactions between them).

>For those of you who don't know, Model View Controller (or MVC) is a design pattern which separates out functionality 
>into three different main functionalities. 
>
>- Model: deals with handling the data and functionality with processing data
>
>- View: deals with presenting data (whether visually, textually, or in any other way) in a way that an external entity 
>(like you or another program) can understand what is happening and interact with your program
>
>- Controller: deals with linking up interactions between the model and view, and is the mastermind behind running your 
>program and handling external entity interactions (like you clicking a button or something else sending requests)

### What is Routing?

Routing is when we define the actual routes/paths of our application and the functionality that they provide. 

![simple-url-example](./img/simple_url.png)

*https://doepud.co.uk/blog/anatomy-of-a-url*

In the image above, when we refer to the path, we're talking about the part from (and including) the slash after the 
domain name. So, for example, in the [https://c4cneu.com/jumpstart](https://c4cneu.com/jumpstart) URL, `/jumpstart` is 
the path. Another image with a more complex URL will be shown below which dissects more components.

### Routing in Vert.x

To handle routing and general server boilerplate code, we're going to be using Vert.x. Vert.x is a library created and
maintained by Eclipse (you might recognize it as an IDE if you've taken Fundies 2) that provides a lot of powerful
functionality. 

Routing in Vertx is done with `Router` and `Route` objects provided by the Vert.x dependency. In the api directory, we 
have an `ApiMain` class, which handles creating a `Router` Vert.x object, setting up a lot of the routing settings we'll 
need in the future, creating a home `Route`, and setting the port to start on. If you take a peek at the `ApiMain` class
in the api directory, this is what you'll find for setup:

```java 
/** The initialize the sub-router and start the API server. */
public void startApi() {
  // Get a 'Vertx' object. This will provide us an HttpServer and Router.
  Vertx vertx = Vertx.vertx();
  // Get an HttpServer from the Vertx object.
  HttpServer server = vertx.createHttpServer();

  // Create a new router using the vertx object.
  Router router = Router.router(vertx);
  /*
   * Set up CORS so that back end and front end can interact through different servers. Usually
   * web browsers will only allow a front end to fetch resources from back-ends
   * that have the same address as them (with a few caveats), but this allows us to 'allow' other
   * things too.
   * See https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS for more info.
   */
  router
      .route()
      .handler(
          CorsHandler.create("*")
              .allowedMethods(
                  Set.of(HttpMethod.GET, HttpMethod.POST, HttpMethod.PUT, HttpMethod.DELETE))
              .allowedHeaders(
                  Set.of(
                      "Content-Type",
                      "origin",
                      "Access-Control-Allow-Origin",
                      "Access-Control-Allow-Credentials",
                      "Access-Control-Allow-Headers",
                      "Access-Control-Request-Method")));

  // Set a logger handler
  router.route().handler(LoggerHandler.create());

  // Set a body handler. This deals with gathering the entire body and sets it on the
  // RoutingContext.
  router.route().handler(BodyHandler.create(false));

  // Set a custom method to be called on failure or uncaught exception.
  router.route().failureHandler(this::handleFailure);

  ...

  // Start the server and listen on port :8081
  // (you can access this locally at http://localhost:8081)
  server.requestHandler(router).listen(defaultPort);

  // Let the user know the server has started
  System.out.println("Hey! The server has started on port " + defaultPort);
```

The home `Route` or root route is the path `/`. Using what we learned earlier, if we were hosting a server locally on 
port :8081, that path would be accessible by the URL `http://localhost:8081/`. We can also see that in the `Route` object for
home produced by the `Router` in `ApiMain.startApi()`, a route handler was set using the `handler()` method and by 
passing in a [Method Reference](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html), which is a 
type of lambda or anonymous function. Down below, you can see a `handleHome()` method that takes in a `RoutingContext`
from Vert.x, which is an object containing all of the request info we need. We use that to construct a response by 
getting a new response object from the `RoutingContext`, setting the status code, putting header information, and 
setting CORS info. Finally, the interaction is ended with the `HttpServerResponse.end()` method.

So in the `ApiMain#startApi()` method, near the end, you can see an example of how we went about creating a new route
(the home route described above). We'll go into more detail below, but here's an example of what that looks like:

```java
public void startApi() {
  // This fits into the example above where the ... is.
  ...

  Route home = router.route("/");
  home.handler(this::handleHome);

  ...
}

/**
 * Handles evaluating the request provided in the {@link RoutingContext} and returning a response
 * for the home route. We could end this response using {@link IRouter}'s {@link
 * IRouter#end(HttpServerResponse, int, String, String)}, but we'd like to show you what's going
 * on under the hood here.
 *
 * @param ctx An object containing request data provided by a Vertx route handler.
 */
private void handleHome(RoutingContext ctx) {
  HttpServerResponse response =
      ctx.response()
          // Set the status code (200 = SUCCESS).
          .setStatusCode(200)
          // Sets the 'Content-Type' of this response
          .putHeader("Content-Type", "text/plain")
          // Sets the CORS to the same thing we did above above for the response
          .putHeader("Access-Control-Allow-Origin", "*")
          .putHeader("Access-Control-Allow-Methods", "DELETE, POST, GET, OPTIONS")
          .putHeader(
              "Access-Control-Allow-Headers",
              "Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With");

  // Returns 'Hello Jumpstarters!' as a response
  response.end("Hello Jumpstarters!");
}
```

Since that's a lot of stuff to set when finishing up a response, we've provided some `end()` methods for you in the 
`com.codeforcommunity.rest.IRouter` interface, which will add a lot of that for you! A lot of that was done manually in 
the `handleHome()` method to show what it looks like.

#### Static Routing

Let's go through the exercise of creating a new route. We'll need to start out by creating a new `Route` object from the
`Router`. First, create a new `Route` object right after the home route's handler is set. We'll do that using 
a register route method. You'll need to pass in the `Router` object that was created earlier in the method. 
Let's create a route `/hi`.

```java 
home.handler(this::homeHandler);

// Create a new Route object from the Router, and do it for a GET request.
this.registerGetHiRoute(router);

...
// Down below in a new method.
private void registerGetHiRoute(Router router) {
  // Set the route up to respond to GET method requests.
  Route hiRoute = router.get("/hi");
  ...
```

Next we'll set the handler for that request method and finish up the `registerGetHiRoute` method.

```java 
private void registerGetHiRoute(Router router) {
  // Set the route up to respond to GET method requests.
  Route hiRoute = router.get("/hi");
  // Note: The hiRouteHandler method doesn't exist yet...
  hiRoute.handler(this::hiRouteHandler);
}
```

Finally, we'll define the `hiRouteHandler` method to return some data for the route. We'll make sure to do it using our 
`IRouter.end` method. It's been statically imported, so you should just be able to call `end()` with your data.
Also, since a string is valid JSON data, we don't need to set the `Content-Type` to `text/plain`, we can just leave it
as `application/json`.

```java 
private void hiRouteHandler(RoutingContext ctx) {
  String responseBody = "Hey Jumpstarter!";
  // Don't forget to set the response status!
  end(ctx.response(), 200, responseBody);
}
```

And now you should be able to access that route at `http://localhost:8081/hi` using your API client when you restart 
your server.

For reference, the final example looks something like this:

```java 
  ...

  // In startApi().
  home.handler(this::handleHome);

  // Create a new Route object from the Router, and do it for a GET request.
  this.registerGetHiRoute(router);

  // Start the server and listen on port :8081
  // (you can access this locally at http://localhost:8081)
  server.requestHandler(router).listen(defaultPort);

  // Let the user know the server has started
  System.out.println("Hey! The server has started on port " + defaultPort);
}

private void registerGetHiRoute(Router router) {
  // Set the route up to respond to GET method requests.
  Route hiRoute = router.get("/hi");
  // Note: The hiRouteHandler method doesn't exist yet...
  hiRoute.handler(this::hiRouteHandler);
}

private void hiRouteHandler(RoutingContext ctx) {
  String resp = "Hey Jumpstarter!";
  // Don't forget to set the response status!
  end(ctx.response(), 200, resp);
}

private void handleHome(RoutingContext ctx) {
...
```

#### Route Parameters

What if you wanted to have a route parameter, that is, what if you wanted to have *dynamic* routes? Dynamic routes are
routes that don't have to be explicitly declared. These can be in paths like `/jumpstart/hi`, `/<your_name>/hi`, or
`/c4c/hi`, and then we can also have them return something like "hey <whatever you put in before 'hi'>". That can 
actually be done pretty easily. Building off of the example above, this is what we would do.

In the `registerGetHiRoute` method, we'll want to adjust the route to add the param.

```java 
private void registerGetHiRoute(Router router) {
  // Set the route up to respond to GET method requests.
  // Notice how we added a colon before the 'name' parameter.
  Route hiRoute = router.get("/:name/hi");
  // Note: The hiRouteHandler method doesn't exist yet...
  hiRoute.handler(this::hiRouteHandler);
}
```

And now that Vert.x knows to look for a parameter called 'name' that will be provided in the `RoutingContext`. We can now
change `hiRouteHandler`. We've also provided the `getRequestParameterAsString` and `getRequestParameterAsInt` methods in 
the `com.codeforcommunity.rest.RequestUtils` class to make getting that information a little easier (you'll probably 
have to import it to `ApiMain`). Check it out if you're interested in how it works though!

```java
private void hiRouteHandler(RoutingContext ctx) {
  // Request the 'name' param from the request
  String name = getRequestParameterAsString(ctx.request(), "name");
  String responseBody = "Hey " + name + "!";
  // Don't forget to set the response status!
  end(ctx.response(), 200, responseBody);
}
```

So now the route should respond "Hey Jumpstarter!" when you navigate to `http://localhost:8081/Jumpstarter/hi`

#### Query Parameters

Query parameters are another kind of parameter that can be included. They are (sometimes optionally) included at the 
end of the path and don't have to be included when creating the `Route` object from the `Router`. To be able to access 
them, you can use `getRequestParameterAsString`. They're written in a path by starting with a `?` and chaining multiple
together with `&`. It's very similar to the JSON `object` type we described earlier, where items are referened by a key.

Here are a some examples: 

- `/some/path?param1=value1&param2=value2&param3=value3`
    - Multiple values with different keys.
- `/some/path?param1=value1&param1=value2&param1=value3`
    - Multiple values with the same key (this is allowed here, while in an `object` for JSON you'd have to have a 
    single key relate to a list of values).
- `/posts/15?sort=score&asc=true&username=jumpstart1234`
    - An example of a path '/posts/15' that has params like `{ "sort": "score", "asc": true", "username": "jumpstart1234" }`

The way 
that we currently have it set up, the `getRequestParameterAsString` cannot have an empty value, so if you would like to 
include query params then you should create a new method which allows empty values.

Here's an example of a dissected URL with query parameters if you're interested in seeing what it looks like.

![complex-url](./img/complex_url.png)

*https://doepud.co.uk/blog/anatomy-of-a-url*

#### Creating a Sub-Router

A subrouter is a custom router which handles routing for a group of routes with the same path prefix. For example, if 
you have a bunch of defined routes for `/user/info/...`, then you can create a subrouter for `/user/info/`, and every
route created in the subrouter will automatically be prefixed with that. This is not only useful because it lets you 
type less by removing the need to copy the same prefix for similar routes, but it also allows you to separate out 
the functionality of similar methods. For example, if you have a subrouter for something such as **_posts_** *(hint, 
hint)*, then all functionality in that subrouter should be related to posts, keeping you more organized.

To create a subrouter, it's not hard at all. All you need to do is call the `Router.mountSubRouter()` method in your
`ApiMain` and set a few things up in your subrouter class.

```java 
// Again, in ApiMain BEFORE calling server.requestHandler().
// Initialize a router (that was probably passed in).
router.mountSubRouter("/path_prefix", subRouter.initializeRouter(vertx));

server.requestHandler(router).listen(defaultPort);
```

And then in your subRouter class, create a new router, set up routes, and return the router.

```java 
public class SubRouter implements IRouter {
  private final ISomethingProcessor processor;

  public SubRouter(ISomethingProcessor processor) {
    this.processor = processor;
  }

  @Override
  public Router initializeRouter(Vertx vertx) {
    // Create a new Router object in our subrouter.
    Router router = Router.router(vertx);

    this.registerRouteA(router);
    this.registerRouteB(router);
    ...
    
    return router;
  }

// Other methods for doing route work.
...
}
```

### Processor Interfaces

Since the api shouldn't know anything about the database, a lot of the time processing needs to be done in the service
directory/module. That makes things complicated though, since the api module needs to be able to call these methods 
when a route is accessed. This can be accomplished by creating an interface for the processor in the api module and 
implementing it in the service module. In that way, the service module is able to implement the methods api requires 
while not needing to know exactly **how** it's implemented. You'll see an example of this in the starter code given
to you for this week.