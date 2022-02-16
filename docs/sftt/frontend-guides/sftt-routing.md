# Frontend Routing

## The Basics
Routing, essentially rendering the correct content for the current URL and user, primarily takes 
place in the App component.

Using [React Router](https://v5.reactrouter.com/web/guides/quick-start), we can render the correct 
component for the current URL easily with the `Route` component:

```html
<Route path={Routes.LANDING} exact component={Landing} />
```

Here, if the application's path is `Routes.LANDING`, then `Route` will render the `Landing` 
page. (If it isn't, it'll render null).

Now consider a simplified version of our application with four declared paths. If we just had 
four `Route`s, then if the URL matched more than one of those paths, we would render more 
than one component. However, in our App, we only want to render one page at a time. To accomplish 
this, we use React Router's `Switch` component:

```html
<Switch>
  <Route path={Routes.LANDING} exact component={Landing} />
  <Route path={Routes.LOGIN} exact component={Login} />
  <Route path={Routes.HOME} exact component={Home} />
  <Route path={Routes.ADMIN} exact component={AdminDashboard} />
</Switch>
```

`Switch` looks for a `Route` that matches the current path (in top-down order), then renders its 
component and nothing else.

## Auth Redirects

So far, so good. However, a user who isn't logged in shouldn't be able to go to the home page. 
Similarly, only admin users should have access to the admin dashboard. How can we ensure 
that users go to the appropriate pages based on their privilege level?

First, we have to get the user's privilege level using our [Redux selector](https://docs.c4cneu.com/guides/frontend-redux/).

```ts
const privilegeLevel: PrivilegeLevel = useSelector((state: C4CState) => {
    return getPrivilegeLevel(state.authenticationState.tokens);
  });
```

Then, we just use a switch statement to return the correct `Switch` and `Route`s.

```ts
switch (privilegeLevel) {
  case PrivilegeLevel.NONE:
    return (
      // routes for a user who isn't logged in  
    );
  case PrivilegeLevel.STANDARD:
    return (
      // routes for a standard logged-in user  
    );
  case PrivilegeLevel.ADMIN:
  case PrivilegeLevel.SUPER_ADMIN:
    return (
      // routes for admins  
    );
}
```

What do the routes actually look like?

Let's start with the user that isn't logged in. This user should be able to go to the Landing and 
Login pages, but not the Home or Admin Dashboard pages. If they try to go to either of these pages, 
we should redirect them to the Login page. Luckily for us, React Router has a `Redirect` component 
too. So we have:

```html
<Switch>
  <Route path={Routes.LANDING} exact component={Landing} />
  <Route path={Routes.LOGIN} exact component={Login} />
  <Redirect from={Routes.HOME} to={Routes.LOGIN} />
  <Redirect from={Routes.ADMIN} to={Routes.LOGIN} />
</Switch>
```

If a user tries to go to the home page, we'll redirect them to `Routes.LOGIN` and our second
`Route` component will render the `Login` page.

### Redirecting Back After Login

But wouldn't it be great if we could send the user back to the page they originally wanted to go to 
after they log in? Well, we can do that with React Router's `location`, which represents "where the 
app is now, where you want it to go, or even where it was" (according to the docs). Instead of 
passing `Redirect`'s `to` parameter a string (`Routes.LOGIN`), we can pass it a location.

Then, instead of
```html
<Redirect from={Routes.HOME} to={Routes.LOGIN} />
```

we'll have

```html
<Redirect 
    from={Routes.HOME} 
    to={{
      pathname: Routes.LOGIN, 
      state: {
        destination: ROUTES.HOME,
      },
    }}
/>
```

so the user will still go to `Routes.LOGIN`, but our Login page will be able to access this `state` 
property and learn that the user wanted to go `Routes.HOME`. 

We can abstract this `Redirect` with location logic into a component that we'll call
`AuthRedirect`:

```tsx
interface AuthRedirectProps {
  readonly from: string;
}

const AuthRedirect: React.FC<AuthRedirectProps> = ({ from }) => {
  return (
    <Redirect
      from={from}
      to={{
        pathname: Routes.LOGIN,
        state: {
          destination: from,
        },
      }}
    />
  );
};
```

!!! note
    
    `location.state` can have any props. We decided to name our redirect prop `destination`, which 
    we declare in  the following interface (located in App):
    ```ts
    interface RedirectStateProps {
      readonly destination: Routes;
    }
    ```

Then, after the user logs in, we can redirect the user to the home page.

```ts
// in the Login container

// get the location, which has RedirectStateProps
const location = useLocation<RedirectStateProps>(); 

// if the user was redirected
const destination: Routes = location.state
    // get where they originally wanted to go (for us, Routes.Home)
    ? location.state.destination
    // otherwise, redirect them to Home after login (which is where we wanted to go anyway!)
    : Routes.HOME;

if (loggedIn) {
  // redirect user to home page
  return <Redirect to = {'/' + query.get('destination')}/>;
} else {
  return <LoginPage />;
}
```

and finally, for our `PrivilegeLevel.NONE` user we have:

```html
case PrivilegeLevel.NONE:
  return (
      <Switch>
          <Route path={Routes.LANDING} exact component={Landing} />
          <Route path={Routes.LOGIN} exact component={Login} />
          <AuthRedirect from={Routes.HOME} />
          <AuthRedirect from={Routes.ADMIN} />
      </Switch>
  );
```

!!! note

    There are other ways to do this, namely by using path parameters and/or query parameters.
    For instance, instead of having our `AuthRedirect`, we could do something like:

    ```html
    <Redirect from={Routes.HOME} to={Routes.LOGIN + '?destination=home'} />
    ```

    Then in `Login`, we'd do something like:

    ```ts
    // in the Login container
    const history = useHistory(); // get the browser's history
    const location = useLocation(); // get the location

    // get the query parameters
    const query: = new URLSearchParams(location.search);
    
    if (loggedIn) {
      // redirect the user to the desired destination
      return <Redirect to={'/' + query.get('destination')} />;
    }
    ```

    **Why use this way?**
    
    The primary reason is that `location` isn't shared across different tabs/browsers. So if you 
    open a new Login tab, the app won't redirect you to your original destination. But if you 
    open a new Login tab with the same query param (which you would get if you copy/pasted the 
    link), it will.

    **The catch**

    Users have access to the URL–they can mess with the query params and break things. Also, 
    people tend to copy URLs directly when they share them, meaning they tend to share the query 
    params. That's fine in some cases, but not when you don't want to share the query param behavior 
    across any two random users.

## Finishing up App

Just a little more to go! Thankfully, our standard and admin-level routing is pretty straightforward: 

Standard users are redirected from the Admin Dashboard to Home:
```html
<Switch>
  <Route path={Routes.LANDING} exact component={Landing} />
  <Route path={Routes.LOGIN} exact component={Login} />
  <Route path={Routes.HOME} exact component={Home} />
  <Redirect from={Routes.ADMIN} to={Routes.HOME} />
</Switch>
```

and admins can go anywhere:
```html
<Switch>
  <Route path={Routes.LANDING} exact component={Landing} />
  <Route path={Routes.LOGIN} exact component={Login} />
  <Route path={Routes.HOME} exact component={Home} />
  <Route path={Routes.ADMIN} exact component={AdminDashboard} />
</Switch>
```

Then we just throw it all into React Router's `BrowserRouter` component that syncs the 
application to the browser's URL and put everything in App!

```tsx
const App: React.FC = () => {
  const privilegeLevel: PrivilegeLevel = useSelector((state: C4CState) => {
    return getPrivilegeLevel(state.authenticationState.tokens);
  });

  return (
  <BrowserRouter>
    {(() => {
      switch (privilegeLevel) {
        case PrivilegeLevel.NONE:
          return (
            <Switch>
              <Route path={Routes.LANDING} exact component={Landing} />
              <Route path={Routes.LOGIN} exact component={Login} />
              <AuthRedirect from={Routes.HOME} />
              <AuthRedirect from={Routes.ADMIN} />
            </Switch>
          );
        case PrivilegeLevel.STANDARD:
          return (
            <Switch>
              <Route path={Routes.LANDING} exact component={Landing} />
              <Route path={Routes.LOGIN} exact component={Login} />
              <Route path={Routes.HOME} exact component={Home} />
              <Redirect from={Routes.ADMIN} to={Routes.HOME} />
            </Switch>
          );
        case PrivilegeLevel.SUPER_ADMIN:
        case PrivilegeLevel.ADMIN:
          return (
            <Switch>
              <Route path={Routes.LANDING} exact component={Landing} />
              <Route path={Routes.LOGIN} exact component={Login} />
              <Route path={Routes.HOME} exact component={Home} />
              <Route path={Routes.ADMIN} exact component={AdminDashboard} />
            </Switch>
          );
      }
    })()}
  </BrowserRouter>
  );
};
```
