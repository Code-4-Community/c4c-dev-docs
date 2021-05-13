
# Jumpstart Week 4: React and Material-UI

Welcome to the 3rd week of the Jumpstart Program! This is the week we get into the good stuff...React! 
This word is probably one that you have heard whenever Web Development has been the topic of conversation 
and now you will get to learn about this awesome tool.


## Basic Plan & Topics Covered
- React
    - What is React?
    - Why is React something we use?
    - A look at a React file
- React Components
    - What is component?
    - How do we use components?
- React Props
    - What are props?
    - Why do we use props?
    - How do we use props?
- State Management
    - What is managing state?
    - useState Hook
- Typescript
    - Why use Typescript?
    - Typing in React
- Material UI
    - What is Material UI?
    - How to use Material UI
    - Styling in Material UI
    

## React

Our good friend John wants to make an interactive user interface for his website but does not want to go through
the whole process of creating an HTML layout and then applying a CSS stylesheet and finally adding in Javascript
functionality and liveliness. If only there was a simple library that can do all of this for us and more.......oh
wait there is. It is called React and it is dope.
 
### What is React?

React is a declarative, efficient, and flexible JavaScript library for building user interfaces. 
It lets you compose complex User Interfaces from small and isolated pieces of code called “components.” 
React will build you a website out of pure Javascript! It combines HTML tags in the form of JSX, 
CSS files and good old JS logic to produce a masterpiece. 


### Why is React better than plain old HTML/CSS/JS combo?

Well React is just a Javascript Framework, that simplifies web development. 
In addition, React makes it easy for developers to manipulate and work with the DOM. The DOM stands for the Document
Object Model and is what connects the browser and the HTML code. Lastly, React uses a type
of code called **declarative code**. 

Declarative code describes the end result, but does not act as a step-by-step 
guide of how to do it. In practice, that means declarative code is lightweight, easier to understand and change, 
and has fewer bugs. However, declarative code is error prone, which is why we use Typescript 
to help prevent these errors that may break our application.

### What a file looks like that incorporates React

Let us call this file LuckyNum.tsx (.tsx refers to the file being a typescript file).
```
import React from 'react'; 

const LuckyNum = () => {

    let myLuckyNumber = 7;

    return (
        <div>
            <h1>My Lucky Number is {myLuckyNumber}</h1>
        </div>
    );
}

export default LuckyNum;
```

At the top of our file we import the React library. We then start by building a *functional component* 
(which we will explain in just a moment) to display our lucky number to the user. Inside our component we return 
the JSX (Javascript XML) we want to be displayed inside the return statement. We then export the component for use 
in other places.


## Components

Components are the building blocks of a React application. 
To master react, it's important to understand what a component is and how to create them.

### What is a Component?

If we think of our React application as a puzzle, then a component is just a single piece. For a puzzle to be complete we need at least one piece. 
Similarly, each React application needs at least one component. The default component will typically be called 
App.js or in our case App.tsx (we are using Typescript instead of Javascript). Let us take a look at the LuckyNum.tsx
file created above.
```
import React from 'react'; 

const LuckyNum = () => {

    let myLuckyNumber = 7;

    return (
        <div>
            <h1>My Lucky Number is {myLuckyNumber}</h1>
        </div>
    );
}

export default LuckyNum;
```
Here we see the creation of a component. The component's name is LuckyNum and displays our lucky number in a `<h1>`
tag. We know this is a component because it is returning JSX to be displayed to a user. Notice how our `<h1>` is
wrapped in a `<div>`. This is because React requires us to return a contained component. JSX can only have one 
outer element. All other elements must be between the tags of that outer element. In most cases using a `<div>` is 
the best way to go. However, you can use any piece of JSX (HTML element) as a wrapper/container.

So, we have covered that a component is just a piece of a React App. As we'll see later, React lets us compose components, 
meaning we can use one component inside of another one!

### Using Components

Remember our `LuckyNum` component we made above? We are going to use this component in our Profile component below in
order to display our favorite number along with our name and favorite color.

```
import React from 'react';
import LuckyNum from './LuckyNum';

/* 
   Notice how there is no return statement. If we are only 
   returning JSX (no javascript inside of our component) 
   we can use () to wrap our component
*/

const Profile = () => (

    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is John! My favorite color is blue.</h1>
        <LuckyNum />
    </div>
);

export default Profile;
```

As you can see we import the component `LuckyNum` from the typescript file we wrote. Once the component is imported
we are able to use it in our new component. The syntax to do this is by creating a tag to indicate where we
want the imported component to be placed.

## Props

Looks like things are going well. We have successfully displayed John's profile picture, name, favorite color and
lucky number. Now John's friend Doug wants to display his lucky number on his profile. John is happy to help and
gives him the `LuckyNum` component we made. However, while John's favorite number is 7, Doug's favorite number is 10, 
so he can't use the `LuckyNum` component! John has a solution: use props.

### What are Props and why use them?

Props are a nice way of making your components reusable. Instead of hard coding all of our data and information
into our components, we can pass in props to give some malleability to our components. A great way to understand
props is to compare them to how functions work. For example, let us print a message to the console.

```
function printHi() {
    console.log("hi");
}
```

We successfully printed "hi" to the console! Now what if we wanted to print "hey" to the console. We would have
to make a new function because `printHi()` only prints "hi". We would then have to make `printHey()`.

```
function printHey() {
    console.log("hey");
}
```

We have now printed "hi" and "hey," but it is very repetitive and not efficient. If we wanted to print another
message, we would have to make a new function OR we can just add an argument to a function to print whatever we
want to the console and we would not need to make a new function each time.

```
function printMessage(message) {
    console.log("Here is your note: " + message);
}
```

Now we can reuse the function whenever we want to print something to the console! 

### Using Props

This same logic can be applied when we use props. Let us revisit the `LuckyNum` component again.

```
import React from 'react'; 

const LuckyNum = () => {

    let myLuckyNumber = 7;

    return (
        <div>
            <h1>My Lucky Number is {myLuckyNumber}</h1>
        </div>
    );
}

export default LuckyNum;
```

Right now this component only displays John's lucky number. In order to make this component more reusable so that
Doug can use it, we must add props. We add props as an argument of the function component.

```
import React from 'react'; 

const LuckyNum = (props) => {

    let myLuckyNumber = 7;

    return (
        <div>
            <h1>My Lucky Number is {myLuckyNumber}</h1>
        </div>
    );
}

export default LuckyNum;
```

`props` is an object that contains every prop passed to the component. We can access a specific prop's value
 by it's argument name. In our case, we can access the `luckyNum` field of the prop object and display that.
 Now that we have access to `props` we can simply display the luckyNum field of the prop object.

```
import React from 'react'; 

const LuckyNum = (props) => (

    <div>
        <h1>My Lucky Number is {props.luckyNumber}</h1>
    </div>

)

export default LuckyNum;
```
Our component is now reusable!! We now can use it on Doug's profile like so:

```
import React from 'react';
import LuckyNum from './LuckyNum';

const Profile = () => (

    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is Doug! My favorite color is purple.</h1>
        <LuckyNum luckyNum={1111}/>
    </div>
);

export default Profile;
```

`props` is an object that React makes for each component. When you pass parameters into component declaration 
like we did with `LuckyNum` React adds the parameter as a field of the object `props`. In order to get the field
of the object we simply access the object's field like we would any other object. 

> We can make the `Profile` component more reusable with props. Try now to add props to make `Profile` more
>generic (possible answer below). 

```
import React from 'react';
import LuckyNum from './LuckyNum';

const Profile = (props) => (

    <div>
        <img src=`${props.profileURL}`/>
        <h1>Hi, my name is {props.name}! My favorite color is {props.favoriteColor}.</h1>
        <LuckyNum luckyNum={props.luckyNum}/>
    </div>
);

export default Profile;
```

## State Management

Alright. So John is now super happy with his profile and wants to see if other people like his profile as much
as he does. To do this, John adds a like button on his profile. 

```
import React from 'react';
import LuckyNum from './LuckyNum';

const Profile = () => (

    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is John! My favorite color is blue.</h1>
        <LuckyNum />
        <button>Likes: </button>
    </div>
);

export default Profile;
```

John is now perplexed. How can I keep track of the number of likes and how can I keep the number consistent
through each render? Welcome to state management John.

### What is managing state?
React will only re-render when data changes. 
You can think of state as all the data that can change while a user is on a webpage.
In order to make your non-constant data persist we
have to manage the state of our application. We will be managing state in our components using a *react hook*: 
`useState`. There are other ways of managing state in react, but this is a simple and easy way.

### useState Hook
`useState` allows us to add and keep track of data in the state. 

In order to use this hook we must import 
it from react. We use `{}` when importing useState because it is not a default export but a named one. We will
discuss default and named exports in Week 6.

```
import { useState } from 'react';
```

Let us add that import into John's profile component.

```
import React, { useState} from 'react';
import LuckyNum from './LuckyNum';

const Profile = () => (

    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is John! My favorite color is blue.</h1>
        <LuckyNum />
        <button>Likes: </button>
    </div>
);

export default Profile;
```

Now, in order to use `useState`, we make a `const` array with a variable representing the current state, 
and a setter function which allows us to update the state. We then set this equal
to a call to useState and pass in the type of data we want to keep track of. For example if we want to keep 
track of someones message we would write this:

```const [message, setMessage] = useState("Hello World")```

We have initialized `message` to be "Hello World". If we want to change our message we would simply call 
`setMessage` function.

```setMessage("This is our new message")```

Now John can simply implement `useState` into his profile.

```
import React, { useState } from 'react';
import LuckyNum from './LuckyNum';

const Profile = () => {

    // initialize the state to 0 because we start with 0 likes
    const [profileLikes, setProfileLikes] = useState(0);

    return(
    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is John! My favorite color is blue.</h1>
        <LuckyNum />
        <button>Likes: {profileLikes}</button>
    </div>
    );
}

export default Profile;
```

Now all we have to do is create an `onClick` event to know when to increment the likes.

```
import React, { useState } from 'react';
import LuckyNum from './LuckyNum';

const Profile = () => {

    // initialize the state to 0 because we start with 0 likes
    const [profileLikes, setProfileLikes] = useState(0);

    // handles incrementing the likes of John's profile
    function handleLikes() {
        setProfileLikes(profileLikes + 1);
    }    

    return(
    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is John! My favorite color is blue.</h1>
        <LuckyNum />
        <button onClick="handleLikes">Likes: {profileLikes}</button>
    </div>
    );
}

export default Profile;
```

## Typing with Typescript

As you may have noticed, React is normally implemented with Javascript. One of the downsides of using JavaScript is 
that we do not get type enforcement.

### Why use Typescript in the first place?

When developing an application with a lot of contributors it is important to have some sort of structure to 
your code. Typing (the act of giving our code data types) prevents us from introducing errors and messing up a project 
with other contributors on it. Typescript gives us the functionality of typing our components and data! 

### How to type in React

It is as easy as it seems. For example let's add a type the `useState` that John used for likes on his profile and the
`useState` for our message example:

```
const [profileLikes, setProfileLikes] = useState<Number>(0);
const [message, setMessage] = useState<String>("Hello World")
```

When adding types to components, we have to make sure to type the props that we are taking in (if applicable). The way
we do this is with an interface with the name and the type of the prop we are expecting. For example let's 
take the `LuckyNum` component as an example.

```
import React from 'react'; 

/*
    We specify luckyNumber as readonly because we do not need to modify
    it anywhere in our component. All we need is to be able to read the value.
*/

interface LuckyNumProps {
    readonly luckyNumber: number;
}

/*
    React.FC = react functional component
*/

const LuckyNum: React.FC<LuckyNumProps> = (props) => (

    <div>
        <h1>My Lucky Number is {props.luckyNumber}</h1>
    </div>

)

export default LuckyNum;
```

## Material UI: a lifesaver

We all hate having to style our components for hours on end to make them look beautiful and actually work
properly. Do not worry, because Material UI is here to save the day!

### What is Material UI?

Material UI is a popular framework in React. It is a library filled with pre-styled components that are super
helpful. There are many different components that basically replace the need for any pure html components in
your project. Each of the pre-made components will save you a ton of time on styling and formatting.

### How can I use it?

All you have to do is import any component you want from the list found 
[here](https://material-ui.com/components/box/). For example, let us replace the html `<button>` for John's 
likes with a Material UI Button component.

```
import React, { useState } from 'react';
import LuckyNum from './LuckyNum';
import { Button } from '@material-ui/core'

const Profile = () => (

    // initialize the state to 0 because we start with 0 likes
    const [profileLikes, setProfileLikes] = useState(0);

    // handles incrementing the likes of John's profile
    function handleLikes {
        setProfileLikes(profileLikes + 1);
    }    

    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is John! My favorite color is blue.</h1>
        <LuckyNum />
        <Button onClick={()=>handleLikes}>Likes: {profileLikes}</Button>
    </div>
);

export default Profile;
```

### Styling Material UI Components

If you wanted to style the components in Material UI a little more to tailor your specific needs, you can do that!
For example, let us make the newly imported Button component on John's profile black.

We first have to import `makeStyles` from `@material-ui/core/styles`. We then make an object for each class we 
want to apply to our components. We make an anonymous function called `useStyles` that is equal to `makeStyles`
and pass in our objects to the `makeStyles` function as one big object. We then call `useStyles` in our 
component to apply in `className`.

```
import React, { useState } from 'react';
import LuckyNum from './LuckyNum';
import { Button } from '@material-ui/core'

const useStyles = makeStyles({
    
    makeButtonBlack: {
        color: 'black',
        font: '30px'
    }
})

const Profile = () => (

    // allows us to use our custom styles
    const classes = useStyles();

    // initialize the state to 0 because we start with 0 likes
    const [profileLikes, setProfileLikes] = useState(0);

    // handles incrementing the likes of John's profile
    function handleLikes {
        setProfileLikes(profileLikes + 1);
    }    

    <div>
        <img src="profileImage.jpg"/>
        <h1>Hi, my name is John! My favorite color is blue.</h1>
        <LuckyNum />
        <Button 
                className={classes.makeButtonBlack} 
                onClick={()=>handleLikes}>Likes: {profileLikes}
        </Button>
    </div>
);

export default Profile;
```
## Conclusion
John and Doug were able to build Profile Website!! However, the website is not too exciting. Next time
we will learn how to spice things up.

## Other Learning Sources

Here are some sources that should help your understanding. Also you can always search on Google or Youtube
if you need more explanation.

- [React Tutorial](https://reactjs.org/tutorial/tutorial.html)

- [Matieral UI](https://material-ui.com/) 

- [Typescript](https://www.typescriptlang.org/docs/handbook/intro.html)
