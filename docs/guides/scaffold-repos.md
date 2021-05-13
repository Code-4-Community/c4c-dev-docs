# Scaffold Repositories

Code4Community uses two scaffold, or template, repositories called `frontend-scaffold` and `backend-scaffold`. These projects serve as a shared foundation for all Code4Community projects, allowing easy code reuse, and the standardization of best practices, across our many project-specific repos.  

## Frontend Scaffold

The frontend scaffold started as a [`create-react-app`](https://create-react-app.dev) template, specifically the [TypeScript CRA template](https://create-react-app.dev/docs/adding-typescript/#installation). So, this template came with React + TypeScript, and we added a few things: 
- eslint + prettier
- Ant Design Component Library
- Redux
- React Router

## Backend Scaffold

The backend scaffold is a Java Vert.X application, using a PostgreSQL Database. 

## Merging Changes

You can update a project-specific repository by following these steps:

1. Start on the project-specific master branch. Pull in changes from the project repo's origin, you need to be fully up to date with the project repo's master branch. 
2. If you haven't done this before in this repo, you need to add the scaffold repository as an origin on your local machine. You can do this by running `git remote add upstream https://github.com/Code-4-Community/frontend-scaffold.git` for the frontend scaffold, or `git remote add upstream https://github.com/Code-4-Community/backend-scaffold.git` for the backend scaffold. This command sets the scaffold repository as a remote which your local repository can access. 
3. Fetch changes from the upstream repository by running `git fetch upstream`
4. Switch to a new branch, from `project/master`, called `[initials].merge-upstream.[describe changes]`
5. Run `git merge upstream/master` while on your new branch. If there are no merge conflicts, push your branch and make a PR. Otherwise, resolve the conflicts to the best of your ability, reaching out to others where appropriate, then make a PR. 

