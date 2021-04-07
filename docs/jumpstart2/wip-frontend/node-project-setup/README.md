  

# Welcome to C4C Jumpstart-Frontend!

This repository is for the fronted component of the Jumpstart program. The code housed in here will be what is rendered to the user. 

Below are a few things you should know before you get started.
  
## Make Sure npm Is Installed

Make sure you have `npm` installed before running the application as it is needed to assist in running the application. `npm` stands for Node Package Manager which is a command-line utility for interacting with said repository that aids in package installation, version management, and dependency management.

  
  

> If you need to install `npm`, that can be found [here](https://nodejs.org/en/)

  

After cloning the repository, make sure to run `npm install` in order to install npm into the project.

## Development Environments

A **Development Environment** is where you can edit your files in a clean and readable manner, basically where you do your coding. We recommend using [Visual Studio Code (VSCode)](https://code.visualstudio.com/Download) or [IntelliJ](https://www.jetbrains.com/idea/download/). Although, there are many more development environments out there that you can explore, but we recommend these as they are intuitive and resourceful.

> VSCode has a lot of neat extensions to help your in developing. Learn more about them [here](https://code.visualstudio.com/docs/introvideos/extend)
  

## Available Scripts
  

In the project directory (in terminal/git bash), you can run:

  

### `npm start`

  

Runs the app in the development mode. Use `npm start` in order to preview your application on a browser. This script is going to be the one that you use the most.<br  />


Open [http://localhost:3000](http://localhost:3000) to view it in the browser.


The page will reload if you make edits.<br  />
  

You will also see any lint errors in the console.
  


### `npm test`

  

Launches the test runner in the interactive watch mode.<br  />

  

See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

  

### `npm run build`

  

Builds the app for production to the `build` folder. This command is usually only ran after initial devlopment is over.<br  />
  

It correctly bundles React in production mode and optimizes the build for the best performance.
  

The build is minified and the filenames include the hashes.<br  />


Your app is ready to be deployed!

  

  

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

  

  

### `npm run eject`

  

  

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

  

  

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.
  

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.
  

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.


### `npm run lint` and `npm run lint-fix`

These will check and perform formatting and syntax rules to your code. `npm run lint`
will only check your code, while `npm run lint-fix` will make the required changes.

### `npm run prettier` and `npm run prettier-fix`

This does the same thing as `npm run lint` and `npm run lint-fix`. Usually, these commands
are both run to make sure everything is properly formatted.

### Learn More About Scripts and Other React Commands

  

You can learn more in the # [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started). This will help you understand other commands that can be useful in developing your application.


## Installed Dependencies

  

Dependencies are almost like add-ons that can help with your application. Some dependencies help build out your application, add functionality to your application, deploy your application and many more. All dependencies are usually installed through `npm` right into your project. All of your installed dependencies are located in the `package.json` under dependencies.

The dependencies that are already installed in your project are:
- `react` & `react-dom`
	: The default and needed packages for `react` 
- `react-router-dom`
	: Adds routing (the ability to navigate) to a react application
- `redux`
	: Helps with data storage and distribution
- `react-redux`
	: Installs more useful `redux` packages
- `axios`
	: Connects the front-end to the back-end and adds some request functionality
- `material-ui`
	: An open source library that has pre-built components which make styling UI cleaner and easier
- `eslint`
    : A library that deals with syntax checking and formatting your code
- `prettier`
    : Another library that deals with syntax checking and formatting your code


## Layout Of Project

### `node_modules` directory

This directory houses all of the node_modules installed in the project. You do not need to do anything in/with this directory.

### `public` directory

This directory is a separate place to store unique files and images such as manifest files, HTML files, or image files. What is important is that images and fav-icons are stored in this folder for easy reference/access.

### `src` directory

This directory is the meat of your project. This houses all of your react components, views, tests and code for your application. If a file is outside the `src` directory then an error will be thrown as all files that wish to be rendered must be in this directory.

### `README.md` & `.gitignore`

These two files are ones courtesy of git. The `README.md` files is what is displayed on the repository view in git hub. You are actually reading this project's `README.md` now. `.md` is the extension of  a markdown file. The `.gitignore` is used when you do not want to commit a certain file/directory to your repository. All you do is add the file/directory to the `.gitignore` and **git will ignore** the file.

### `.eslintignore`, `.eslintrc.json`, `.prettierrc`, and `.tslint.json`

Linting is the process of cleaning up and formatting your code. The `.eslintignore` file
is like a `.gitignore`, but for the linting, where it will ignore files that are listed.
`.eslintrc.json`, `.tslint.json`, and `.prettierrc` are files which describe which linting settings to use for 
linting.

### `tsconfig.json`

This is a file which describes the settings to be used in Typescript. 

### `package.json` and `package-lock.json`

These files tell NPM, or Node Package Manager, how to run your project and which packages and
dependencies to download and include. `package.json` is a file that you'll end up editing, and 
`package-lock.json` is a file that is created for you when you run `npm install` to deal with 
dependency versions and depdendencies of dependencies.

### `.travis.yml`

This file tells [Travis-CI](https://travis-ci.com/) how to build your code and what to do
with it in CI/CD (Continuous Integration/Continuous Deployment). It's not something you'll need
to use during this course.
