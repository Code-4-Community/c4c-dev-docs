# Walkthrough

A guide to the basic structure of the frontend.

## public :file_folder:

You _could_ add assets outside the module system here (things that won't be processed by 
[webpack](https://webpack.js.org/concepts/)), but generally you'll just leave this folder alone.

_For more information, see the 
[Create React App Docs](https://create-react-app.dev/docs/using-the-public-folder)_.

### index.html :memo:

The base HTML file that the site is built off of. Here, you can set the page title (shown in the 
browser's tab), and import fonts by adding links to stylesheets. Ultimately, a `<script>` tag with
the compiled code will be added to this file automatically during the build process.

### favicon.ico :memo:

The icon displayed in the address bar of the browser.

### manifest.json :memo:

Specifies basic metadata about the application, such as its name and icons.

### robots.txt :memo:

Specifies what parts of the site web robots are allowed to crawl. We allow all web crawlers to 
access all content. Note that web crawlers can ignore this file (it won't stop any malware).

## src :file_folder:

### App.tsx :memo:

The central application component. In it, we handle [routing](../frontend-guides/sftt-routing.md). 
That is, depending on the user's privilege level and what path the user wanted to go to, we decide 
which container to render.

### store.ts :memo:

A Redux file where we create the application [store](https://redux.js.org/api/store), which holds 
the state tree of the application. In this file, we declare relevant types (ex. `C4CState`), combine 
our reducers, define our initial state, and apply thunk middleware.

### api :file_folder:

Contains the logic to access data from the backend. We use 
[Axios](https://medium.com/@MinimalGhost/what-is-axios-js-and-why-should-i-care-7eb72b111dc0) to 
make requests to our backend.

_Axios documentation to be completed._

#### apiClient.ts :memo:

Contains the routes, types, and logic to create an `ApiClient` object, through which users can make 
`PrivilegeLevel.NONE` requests to the backend.

#### protectedApiClient.ts :memo:

Contains the routes, types, and logic to create a `ProtectedApiClient` object, through which users 
can make `PrivilegeLevel.STANDARD`, `PrivilegeLevel.ADMIN`, and `PrivilegeLevel.SUPER_ADMIN` 
requests to the backend.

### assets :file_folder:

Contains image (under `images`) and text (within `content.tsx`) assets to be used throughout the 
site.

### auth :file_folder:

Contains all logic related to the authentication system.

#### [ducks](https://docs.c4cneu.com/guides/frontend-redux/)  :file_folder:

Contains all Redux ducks related to the authentication state of the application.

#### authClient.tsx :memo:

Contains the routes, types, and logic to create an `AuthClient` object, through which users can make
auth-related requests (ex. login, logout) to the backend.

#### axios.ts :memo:

Creates the application `AxiosInstance` through which all clients make requests to the backend. 
Contains logic related to adding authentication headers to requests and automatically refreshing 
the authentication tokens.

### components :file_folder:

Contains presentational components that are reused across the application.

### containers :file_folder:

Contains each page–their layouts and related thunks and logic. 

### utils :file_folder:

Contains useful types, functions, and constants that are used throughout the application.
