# Agenda
- React Router
    - What is React Router?
    - Set Up React Router
    - Using React Router
- Async
- Api Specification
    - The Request
    - The Response
- Axios
    - What is Axios?
    - Promises
    - AxiosInstance
    - Creating Request
    - Default vs. Named Exports
    - Using Request In Application

# Week 7: Advanced Frontend

Hey guess what? You got a working frontend!! Now how do we get some data from the backend? We will 
learn that and more this week.

## React Router

So, we have a landing page, but there is no way for us to navigate off of the landing page. In
order to add this functionality we need to use React Router: a framework that helps us add routes
to our frontend application.

### What is React Router?

React Router is just what it sounds like: a third-party framework that adds routing to an
application. Routing is the capacity to show different pages to the user. That means the 
user can move between different parts of an application by entering a URL or clicking on an element
such as a button.


### Setting up React Router

First we need to set up our routing in our app. We usually set up the routing in our `App.tsx` file.
In order to do that, we are going to use three components:
- `BrowserRouter`
- `Switch`
- `Route`

>Side note: we can rename default exports that we import. For example, we can change
>the naming of BrowserRouter to Router with this import statement: 
>`import { BrowserRouter as Router } from "react-router-dom";`
>This means that BrowserRouter just takes on a different name/alias. It is the same component
>as before but referenced differently.

The `BrowserRouter` component, which we import as `Router`, defines the scope of the router. 
Everything that we want to include in routing needs to be contained between the `Router` tag. This
is what we have so far:

```
<Router>
</Router>
```

Next, we use the `Switch` component to house all of our routes. Any `Route` that is in the `Switch` 
tag will be included in routing. All the `Swtich` component does is tell the React Router to load 
only one route at a time. Now we can add the `Switch` component:

```
<Router>
    <Switch>
    </Switch>
</Router>
```

Lastly we add the meat of the router: the `Route` component. The `Route` component is just a
self-closing tag, signifying a route in our application. There are some important
props we must pass to `Route` in order for routing to work well. First we must add the `path`
prop. This prop is the name of the path for the route. This name can be anything (although you might
want to make it relevant to your applicaiton). Let's make a route to the `CreateBlogPost` component:

```
<Router>
    <Switch>
      <Route path="/posts/create"/>
    </Switch>
</Router>
```

We named our path "/posts/create". This means that our `CreateBlogPost` component can be found at
the path "/posts/create" from the domain. When running the application on your own computer, the full
link will look like this: "localhost:3000/posts/create". If you type that in to the browser (with 
the applciation running) you will go to that location, but you will not see the `CreateBlogPost`
component. In order to render the `CreateBlogPost` component at the given path we need to include 
it in the prop `component={}`. Now our `Route` looks like this:

```
<Router>
    <Switch>
      <Route path="/posts/create" component={CreateBlogPost}/>
    </Switch>
</Router>
```

Now if you type the correct path you should be able to see the component! However, if you add 
something to the end of the path, you would expect it to not show the `CreateBlogPost` component
anymore because it is not the correct path, right? Well, it looks like that 
"localhost:3000/posts/create/4" still renders the `CreateBlogPost` component when it should not. To
combat this error we need to add the `exact` prop to the `Route` component. This just tells the 
`Route` that the path "localhost:3000/posts/create" is the only path to `CreateBlogPost`. The path
has to be "exact". Our router now looks like this:

```
<Router>
    <Switch>
      <Route path="/posts/create" component={CreateBlogPost} exact />
    </Switch>
</Router>
```

> Since the prop `exact` is a boolean prop, we do not need to assign it anything. Including a 
>boolean prop is the same as making the prop true, while not including the prop is the same as
>making the prop false. 

### Using React Router in our application

There are many ways to add routing with React Router, but we are just going to focus on using the
`Link` component. First we need to import the `Link` component:

```
import { Link } from 'react-router-dom';
```

For this example we are going to use a Material UI button. When the button is clicked we are going
to go to the `CreateBlogPost` component. We first start by defining our button:

```
<Button size="large"
    className={classes.navButtons}>
    Add a Post
</Button>
```

This `Button` is just a large button that says "Add A Post" on it. In order to make it be able to 
route to a component we need to add two props to the component. Those props are `to="""` and
`component={}`. All the `component={}` prop does is says that the current component should be
the type of component you pass to the prop. It is similar to casting in Java. In this case
`component={Link}` because we want our `Button` component to act as a `Link` component. The `to=""`
prop just defines which path we should route to. In this case our button's purpose is to route
to the `CreateBlogPost` component, thus we should pass in the route to the `CreateBlogPost`
component that we defined when setting up the router. Now our `Button` component looks like this:

```
<Button size="large"
    className={classes.navButtons}
    to="/posts/create"
    component={Link}
    >Add A Post
</Button>
```

That is all there it is to it!

## Async
Javascript is a fundamentally synchronous language. This means that all of its commands are executed one after another.

But this raises an issue. Let's say we want to do something that takes a significant amount of time (orders of magnitude longer than it takes to execute a line of code). For example, imagine we wanted to get information from another resource on the internet. Because of physical limitations (the time it takes for data to travel through wires that connect the web), this request will take thousands of times longer to execute than a normal line of code. This is an issue because we don't want our program to simply freeze while this execution is happening -- we want the rest of our code to continue executing in the background.

The solution is asynchronous Javascript. Instead of completely pausing our code, asynchronous functions can be run in the background of our code. The `async` features enable us to "wait it out." This gives us the corresponding keyword, `await`. Let's see how they can be used together.

Let's say we're using a library that has an asynchronous `request()` function, and we wanted to get some response from it. Here's our first attempt:

```
const response = request('https://someresource.com');
console.log(response);
```

If we ran this code, `undefined` would appear in the console, even though the request we're trying to make should return data. This is because `request()` is an asynchronous function, and Javascript will just keep executing code without awaiting the results of any asynchronous calls. So, the `console.log()` line will be called immediately after the `request()` line, so the response will still be undefined -- the request has not yet been received.

To fix this, we use the `await` keyword:

```
const response = await request('https://someresource.com');
console.log(response);
```

Now, the console should correctly log the response. This works because Javascript will wait for our request to complete before moving on to the next line.

So where does `async` come in? Imagine further that we defined a function that wrapped this request:

```
function getResponse() {
    const response = await request('https://someresource.com');
    return response.data;
}
```

If we try to compile this code, we'll get an error: <em>await is only valid in async function</em>. This makes sense: if our `getResponse()` function isn't asynchronous itself, then it won't be able to fully execute because it contains asynchronous calls within it. So, any function that contains `await` must be declared `async`, like this:

```
async function getResponse() {
    ...
}
```

Now, we must use `await` to call `getResponse()` because it is asynchronous. 

You may worry that this will ultimately make all our code asynchronous because only asynchronous functions can use `await`. Fortunately, we can still call asynchronous functions inside synchronous ones:

```
function doSomething() {
    getResponse();
}
```

This <em>will</em> still call `getResponse()`, even though it's `async`. It just won't wait for the call to return a value.

## API Specification
Up to now, we've only been worried about displaying our data. We've never really thought about how or where we get that data from. That's where the API comes in.

API stands for Application Programming Interface. In general, it refers to any way of programmatically communicating with another application. In the context of web development, API usually refers to the connection between a frontend and backend.

The good thing about a completely separate frontend and backend is that we don't need to know anything about how our backend works to be able to communicate with it. The API just defines several commands we can use and the responses we can expect to receive -- we don't care how or where the backend gets it data. (If you've learned Java, this might remind you of an `interface`. That's basically what it is.)

An API specification just refers to the definition of this interface. It tells us, in very precise terms, the following things:

1. The different commands we can send to the backend
2. What inputs those commands need so that the backend can make sense of them
3. What, if anything, we should expect to receive as a response to each command we send

We'll start with the first two.

#### The Request
Commands sent over the internet are usually referred to as <em>requests</em>. (You may hear them referred to as HTTP requests -- HTTP here just refers to the protocol used, and it's not really important to us.)

First, every request has a <em>method</em>. The method doesn't change anything fundamental about the request -- it just tells us more about its nature. For example, a `GET` request is normally used to get some resource or data. `POST` is generally used to create data, and `DELETE` to delete. For now, there are only five methods we'll need to know about:

- `GET`
- `POST`
- `DELETE`
- `PUT` - used to replace a resource
- `PATCH` - used to modify a resource

Next, every request has a URL, or Uniform Resource Locator. This just points to some place on the internet. If our API is hosted at https://api.mywebsite.com/, then all of our requests will begin with this URL -- this is often called a base URL. From here, our API spec will define different paths that map to various commands. 

For example, we might have a `GET` request to https://api.mywebsite.com/comments, which retrieves comments. Or, we might have a `DELETE` request to https://api.mywebsite.com/comments that deletes a particular comment. We might also have a `POST` request to https://api.mywebsite.com/authors that creates an author. Notice that changing the method makes the request completely different! In other words, a request is uniquely identified by two things: its method and its URL. Changing either completely changes the command that you're asking the backend to perform.

Next, we have different inputs that a request can take to get more information from the client (our frontend). First are URL parameters. URL parameters are just strings of text (or numbers) that we can insert into our URL to send important data, like ids. For example, an API specification might list this request:

`GET /comments/:id`

(We've ommitted the base URL, as most API specs do, because it's implicit.)

This request probably gets the comment with the given id. We know that id is a parameter because of the colon (:) in front of it. So, when we send the request, how do we also send this id? Easy -- we just replace :id with the actual id, like this:

`GET /comments/42` gets the comment with id 42.

Note that request parameters are required. `GET /comments` is a completely different request from `GET /comments/42`, because the former matches `GET /comments`, whereas the latter matches `GET /comments/:id`. (A request can only match one specification.)

We can also have multiple params, like this: `GET /articles/:year/:authorId`. 

But this may get a bit unwieldy if we have a lot of parameters, and especially if those parameters are arrays or objects. It's infeasible to send complex data structures over plaintext. For example, imagine you wanted to `POST` a new article, with `name`, `author`, `tags`, and `text`, where `tags` is an array of strings. Sending this using URL params would quickly become unmanageable. 

The solution to this problem is the request <em>body</em>. Like params, a body is not essential to a request. (In fact, `GET` requests can have a body, but they usually shouldn't.) A request body can really be any kind of textual data, but many APIs (including ours) will communicate using JSON, JavaScript Object Notation. JSON allows us to sent our familiar Javascript objects and arrays in our request body, so we could `POST` a new article more easily:

```
{
    "name": "How to Write an API",
    "author": "C4C Jumpstart",
    "tags": ["education", "web dev"],
    "text": "...text as long as you want..."
}
```

(Notice that the body is one object, so it's wrapped in curly brackets.)

Once our backend receives this request, it will be able to easily parse the JSON.

#### The Response
Now we know how to send requests. Fortunately, the response is much easier, because it only contains two important things (for our purposes): a status and a body.

The status is just an integer that tells us what the API did with the request. For example, a response of 200 literally means "OK" -- everything went fine. Or, a server might return the infamous 404 if a requested resource was not found. In general, 200-level responses are good and expected; 400 level responses are caused by client error (like bad inputs), and 500-level responses are caused by server errors. You can read more about HTTP responses [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status).

The body, like in the request, is entirely optional. Fortunately, our API spec will tell us if we should expect the server to respond with a body or not, and if it does, what it should contain. For example, if we `GET` comments, the response body might look like this:

```
{
    "comments": [
        {
            "name": "jjones",
            "content": "What's up everyone!",
        },
        {
            "name": "msmith",
            "content": "Wow, this was really helpful."
        }
    ]
}
```

(Again, like request bodies, the response body is one object, so it's wrapped in curly brackets.)

With this, we know all the fundamental components that make up an API request and response.

## Axios

Weird name right? Do not worry, it is a lot easier than it sounds.

### What is Axios

Axios is an open source library that allows us to easily make HTTP requests. There are built in ways
to make HTTP requests in Javascript, but axios is better. In our project, we need to use axios to 
fetch data from our backend so that we can display it on the beautiful frontend we made.

### Promises

Promises are not specific to axios, but they are important to understand 
since they allows us to perform asynchronous queries for information.

Promises are a way for us to deal with return values for asynchronous actions. By definition, 
a promise is an object that may produce a single value some time in the future: either a 
resolved value, or an error with the reason that it’s not resolved (e.g., a network error occurred).
We deal with the result of the promise by "resolving it" with `.then()`. We put a callback function 
(anonymous function usually) in the `.then()` to handle what the promise returned.

> Promises are not lazy, meaning they will start doing their operation as soon as you create it.

##### Promise States

There are 3 different states that a promise can be in. They are:

- Fulfilled
- Rejected
- Pending

*Fulfilled* means that the promise operation returned successfully. *Rejected* means that the 
promise operation failed. *Pending* is the state a promise is in while it waits for the operation
to be completed (we do not know if it was successful or not).

> All of your promises should have a `.then()` after them so they know what to do with the promise
>when it fails or succeeds. 

### AxiosInstance

We are going to be following proper practice. By proper practice, I mean what *Jack Blanc* said was
proper practice. The first step to making an axios request is to first create an instance of axios
that we can base all of our calls off of. This is not necessary for making an axios request, but it
is more informative and allows us to control our requests more. Our instance is going to be super 
simple. Here it is:

```
import axios from 'axios';

const AxiosInstance = axios.create({
  baseURL: process.env.REACT_APP_API_DOMAIN,
  timeout: 5000,
  headers: {
    'Content-Type': 'application/json',
  },
});

export default AxiosInstance;
``` 

Let's take a look at what all of this means. We first start by importing axios like we would any
other outside package. Next, we make a constant using `.create()` in axios. All this does is allow
us to create a custom configuration for our axios. We specify the *baseURL*, which is the url axios
bases all requests off of. Then we set a *timeout*, which gives the request 5000 milliseconds (or 
5 seconds) to respond. If no response is made then we fail the promise. The last part of our custom
configuration specifies that we want our request to be returned in JSON format. 

> JSON stands for Javascript Object Notation. It is just a to standardize a file format for
>web requests (although JSON files are not specific to the web). JSON allows us to store a "human-
>readable" version of our objects in an attribute-value pair.

### Creating Requests

Now that we set up our `AxiosInstance`, we can use that instance to formulate requests to our backend.
Let's just jump right into it! As you may have recalled from the previous weeks, there are four 
basic types of request in a REST structured api. We have a `GET` request (gets something from the 
backend), a `POST` request (adds something to backend), a `PUT` request (updating something from the 
backend), and a `DELETE` request (removes something from the backend). 

>Quick question: do we need all four of these requests for our blog? Note that we are not supporting 
>changing a blog post or comment after it is made.

For our site, we do not need to make a `PUT` request because we do not need to update any 
information, however we will be making `GET`, `POST`, and `DELETE` requests.

We will now use the `AxiosInstance` we created in a file called AxiosInstance.tsx in our api file
we will call api.tsx. Here is what the first part of the file will look like:

```
import AxiosInstance from './AxiosInstance';
import { 
    Post, 
    Comments,
    CreatePost,
    BlogPost,
    CreateComment
} from '../Types';

const POSTS = "/posts";

//more things below this
``` 

We start by importing our `AxiosInstance` we made. Then we want to import our types so that we can 
use the types to type our axios requests. Lastly, we make a constant called `POSTS`, which is just
a short and consistent way of referring to the "/posts" route. "/posts" is a location we will use 
when requesting information. Having a constant of part of the location will save us time.


##### Get Request

What can we use a `GET` request for? That's right, getting our posts and comments. Let's look at how
to get posts. We want to have a way to get one post, and a way to get all the posts currently in
the backend. Here is how to make a get request: 

```
export const getAllPosts = async (): Promise<Post[]> => 
    AxiosInstance.get(`${POSTS}`)
        .then((response) => response.data.posts);
```

Let's go through what this all means together. First, we make a constant that is going to be
assigned to an anonymous asynchronous function. The function is then going to make a get request
using our `AxiosInstance` we created previously. `.get()` requires one argument, which is the url
to make the get request at. As you can recall, the `AxiosInstance` already has our `baseURL` to our
backend, so all we need is to go to that url plus "/posts", which we can use the constant we 
described. We follow up all of our get requests with `.then()` in order to break down the response
into the data we are requesting. To do this we make an anonymous function that takes in a response
(what the promise returned), and then dissect the response into to what we want.

Here is another get request example:

```
export const getPost = async (id: number): Promise<BlogPost> => 
    AxiosInstance.get(`${POSTS}/${id}`)
        .then((response) => response.data);
```

In this call we need to take in the specific post number as an argument to the anonymous function
so that we can use it in the `get()` call in the function body.

> Notice that we are giving types to the arguments of the anonymous functions, the return type of 
>the anonymous function, and the argument portion of the anonymous function.


##### Post Request

Now, what can we use a `POST` request for? That is right! We can use it to create a post/comment and 
clap a post/comment. Let's take a look at how to make a `POST` request:

```
export const createPost = async (post: CreatePost): Promise<void> => 
    AxiosInstance.post(`${POSTS}`, post);

export const clapPost = async (id: number): Promise<void> => 
    AxiosInstance.post(`${POSTS}/${id}/clap`);
```

Just like before, we have an anonymous function with an `AxiosInstance` to make the request. The
difference is that this time the function takes in an argument, which will be the
data that we want to post to the backend. Now, to make a post request, `post()` takes in the url 
location of where to `POST` the data, and the data to post at that location in the form 
`post(location,data)`. Let's look specifically at `createPost`. We know that we want to "post" the 
blog post to the backend. So, we take in a post as an argument. In the `post()` call we give the 
url (which in this case is `"/posts"`), and the data we want to `POST` (the argument we took in the 
anonymous function). 

That's all there is to it.   

##### Delete Request

The `DELETE` request will be the last type of request that we will use. What do you think we can use
it for? Right again! We can use the `DELETE` request to delete a post/comment. Let's take a look at
how to do that:

```
export const deletePost = async (id: number) => 
    AxiosInstance.delete(`${POSTS}/${id}`);
``` 

Same as before. We have an anonymous function that takes in the post number to delete. We then use
our `AxiosInstance` to make a call to delete the id from the specific url. It is similar to our 
`GET` request except we are not deleting instead of getting.

### Default Exports vs Named Exports

You may have noticed that we export each axios call separately by name instead of just using
`export default` at the end of the file. Exporting an individual item is called a named export,
while exporting a whole file is called a default export. The difference between the two is how we 
import them into our other files. A named export has to be imported with curly braces like so:

```
import { namedExport } from 'things';
```

Our default exports do not need the curly braces:

```
import defaultExport from 'otherThing';
```

> What imports that we use currently are named exports? What about default exports?


### Calling Requests in our application

Using our request is very simple. Let's see how to use our get our `getAllPosts`.

```
getAllPosts().then((p: Post[])=>{
    setPosts(p);
})
```

We first start by importing request we want from the api file. For the `getAllPosts` request our
import would look like this:

```
import { getAllPosts } from '../api/api';
```

>We have the curly braces because it is a named export

Next we just use the import like a function (because it is one!). All of our requests that we made
in the `api.tsx` file are just axios promises. We now need to further resolve those promises using
`.then()`. Here we are simply use `.then()` to set the posts' state to the posts that we get back
from the request!

It is as simple as that.


## Other Resources

- [Axios](https://www.npmjs.com/package/axios)
- [React Router](https://www.freecodecamp.org/news/a-complete-beginners-guide-to-react-router-include-router-hooks/)

