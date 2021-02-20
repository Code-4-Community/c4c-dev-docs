# Week 10: Deployment

## Agenda
- Deployment
	- What is deploying a program
	- Why do you want to deploy a program
- Heroku
	- What is Heroku
	- Why do we use Heroku
	- Deploy on Heroku
- Deploy our application
	- Deploying Frontend
	- Deploying Backend
	- Deploying Database
	
## Deployment

### What does it mean to deploy our program?

Deploying a program is the act of making a program publicly accessible on the internet through a URL. A program that is deployed is running on either a remote or local server. A server is basically a machine that is running a program waiting for requests. The server that your program is deployed on is running your program, and once the URL that maps to your program is requested, the server is in charge of performing whatever operations you programmed it to do!

### Why deploy a program?

Developers deploy a program in order to publicly showcase their application and allow people to use it. Deploying a program is a great way to showcase what you built or hopefully provide a service to others. Currently, our application is running locally on our machine. If you remember, the URL for our program was localhost followed by the port number. Other people can't access our localhost because, just like the name says, localhost is our local machine server meant just for us. We can fix that by deploying to a remote hosting service, like Heroku, which has a public URL others can access and use for your application.

## Heroku

### What is Heroku?
Heroku is a cloud platform that allows developers to quickly and easily deploy their applications for free. It's configured to handle a variety of programming languages and frameworks such as Java, Javascript, Python, Ruby, etc. For our case, Heroku is a great choice. Developers upload their project to Heroku's deployment platform and our application gets assigned to a URL. This URL is then waiting to respond to incoming requests. In traditional cloud deployment platforms, developers have to manually configure operating systems, deployment methods and procedures, and URL mappings on a remote server somewhere, which can be a long and complicated process. Thankfully, Heroku simplifies the process. All developers have to do is connect their code with Heroku, and Heroku takes care of the rest!

### Why do we use Heroku
The main reason we're going to use Heroku is because it is easier to use than traditional cloud deployment platforms, as previously mentioned, and it allows us to deploy our application for free. When you deploy your application you need some computing resources. These computing resources cost money, therefore, even to run a very simple server with minimum hardware capabilities can cost around 5-10 dollars a month. Heroku allows us to use these minimum computing resources for free, with some restrictions. Of course if you want to upgrade your computing resources to use more RAM or CPU, you can pay for those options.

### Install Heroku
The way our application is structured, our frontend and backend are separated. They are not dependent on one another and are developed in different environments and tech stacks. Therefore, we have to deploy our frontend and backend separately. Thankfully, Heroku provides great documentation as to how to deploy our React frontend and Java backend. However, before you deploy any application on Heroku, we'll need to install Heroku on our machine. First, we need to create an account on Heroku. The link to sign up is [signup.heroku.com](https://signup.heroku.com/). Once you have signed up for a Heroku account, you have to install some of its software on your machine. Because there are different installation methods depending on the machine that you are using, the download instructions for every operating system can be found [here](https://devcenter.heroku.com/articles/heroku-cli). You are installing Heroku's command line application, which will allow you to open a terminal and run Heroku commands on the terminal.

### Deploy Frontend

To deploy our frontend application, we need to navigate to its directory. Once we have navigated to the directory we have to initialize a git repository if a git repository has not been initialized yet. To initialize a git repository we run the command:
```shell
git init
```
After running our git init command, we have to add our project's files to the git repository. To add the files we type the command:
```shell
git add .
```
Once all the files are added, we can call
```shell
git commit -m 'commit message'
```

Once we have committed our code, we now have to push it into our master branch on git. To do this we run the command:

```shell
git push <branch_name>:master
```

If you have been working from the starter code from the first week, then our branch name is called starter-code. If you have created your own
git repository, make sure to replace <branch_name> with the name of your branch if you are <strong> not </strong> in the master branch already.

Once we have pushed our code, we now have to connect our downloaded Heroku application with our Heroku account. We run the command:
```shell 
heroku login
```
Then follow the instructions to enter our Heroku credentials. Once we have logged in, we now have to create a new Heroku project. 

To create a project we type the command:
```shell 
heroku create (PROJECT_NAME) --buildpack mars/create-react-app
```
By doing this, Heroku is creating a new deployment server for us and giving us a custom URL for us to use. The buildpack part is our way of asserting
that our project is a React project. Usually, Heroku automatically detects that it is a React application but sometimes it does not. We add the buildpack to make sure we avoid those errors.
This will add Heroku to our git repository which is how we will push our code to Heroku. Finally, to push our code we run the command: 
```shell
git push heroku master
```
This will push our current committed code that is in our <strong> master </strong> branch on git. If we want to deploy a branch that is not 
the master's branch we can run the command:
```shell
git push heroku <branch_name>:master
```

This will push our designated git branch, but git pretends that this branch is master.

Now that our frontend code is uploaded to Heroku, we can use the url Heroku gave us to open our application online. Now anyone with this link will be able to see our application.
To open our application we can run:
```shell 
heroku open
```
This should open a browser tab with our project's home page. 
However, this is only the user interface portion of our application. Now we have to deploy our backend code.


### Deploy backend

Before we proceed in deploying our backend we have to note one file. Under our <strong> service/src/main/java/com/codeforcommunity/util</strong> directory you will see a class called PropertiesLoader.
This class will examine our resources file under <strong> persist/src/main/resources </strong> and get our database properties. This is useful because this is how our backend service knows where 
to find our PostgreSQL database. It will also specify the port number in which our backend service will run in.  

To deploy our backend application, first we have to configure our application. Next, we have to navigate to our pom.xml 
file. We have to configure all our app's dependencies through maven so that when we export our Java project our dependencies also get exported.
A dependency is external code from other people or organizations we want to use in our application. Usually, if we did not have problems during local development, then our pom.xml file should be fine and ready to be exported.
If not, then now is the time to fix it. Potential fixes include using different versions of a dependency or getting rid of dependencies that we did not end up using.

We actually need to add a dependency ourselves to deploy our backend.

``` xml
<plugin>
    <!-- Maven's plugin for copying our dependencies below into our executable Jar file. -->
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>3.0.1</version>
    <executions>
      <execution>
        <id>copy-dependencies</id>
        <phase>package</phase>
        <goals><goal>copy-dependencies</goal></goals>
      </execution>
    </executions>
  </plugin>
```

 
This dependency is a maven dependency that copies our dependencies and puts them into a jar file. A jar file is a file that runs our Java application. We'll explain
more about jar files in a little bit. Make sure this dependency is added under the `<plugins>` tag (make sure you <strong> don't </strong> put it under `<pluginManagement>`:

``` xml
<plugins>
<!-- Insert Dependency here -->
</plugins>
```


Next, we have to create a Procfile. This is a text file that that configures Heroku to set up your Java application. Our Procfile must look like:

```ignorelang
web: java $JAVA_OPTS -cp target/classes:target/dependency/* <project main package>
```

In our case, if you download our catch-up code, our Procfile looks like:
```ignorelang
web: java $JAVA_OPTS -jar service/target/service-1.0-SNAPSHOT-jar-with-dependencies.jar
```

This Procfile specifies that our application will use the web, indicated by the "web" portion, and our "java" section specifies how to start it up. 
The last portion of the Procfile specifies how to run our jar file. A jar file is a file used to run a Java application. You can use a jar file to run your Java code anywhere. It's easily exported and
is how Heroku runs our application. <strong> Our Procfile is located in our project's root directory. </strong>

The next configuration we need is a <strong> system.properties </strong> file. This is a file that Heroku uses to define our application's properties, such as the Java and Maven versions. This properties
file is different from our <strong> service/.../resources/properties </strong> directory as this property file is only for Heroku.

Our system.properties looks like
```ignorelang
java.runtime.version=11
maven.version=3.6.2
```
<strong> Make sure this file is in our root directory with your Procfile so that Heroku can easily identify this file. </strong>

Once we have all of our configurations set up, we can begin deploying our backend. Navigate to our backend directory and run the command:

```shell
mvn clean install
```

This will clean our application and build it again.
Once we verified that our application successfully built, we follow the same steps as deploying our frontend code.

If we have not committed our changes to the master branch make so to do so with:

```shell
git add .
```
which adds all our files to our project's git repository

```shell
git commit -m
```
which commits our added files

Once we have committed our code, we now have to push it into our master branch on git. To do this we run the command:

```shell
git push <branch_name>:master
```

If you have been working from the starter code from the first week, then our branch name is called starter-code. If you have created your own
git repository, make sure to replace <branch_name> with the name of your branch if you are <strong> not </strong> in the master branch already.


We should already be logged into Heroku because we deployed our frontend code but if we're not, then we have to login again using:
```shell
heroku login
```

After logging in we run:

```shell
heroku create
```
which creates a new project on Heroku that represents our backend

```shell
git push heroku master
```
This will push our code to the Heroku server. Again, if we want to deploy a version that is not in our master branch we run the command:
```shell
git push heroku <branch_name>:master
```
This will push our designated git branch but Heroku pretends that this branch is the master branch.

After pushing our code to the Heroku server we should be given a URL. This URL is now the URL that our frontend needs to make request to.
To verify that our backend deployed successfully, we again run the command:
```shell
heroku open
```
which should open a new browser window with a message at the top saying <strong> Hello Jumpstarters! </strong>

Now that our backend and frontend are deployed, we have to configure our requests URL. 
To do this, we have to use a feature in Heroku called Heroku configuration files or Heroku secrets. Basically, this is a way for us
to write down important information such as our backend request route in a secure and inaccessible way. To do this we 
have to navigate back to our frontend project. Now we run the command:
```shell
heroku config:set REQUEST_URL=<backend_url>
```

This creates a configuration variable called REQUEST_URL which maps to the value of our backend url. Now we have to go to our React application and 
replace where we write <strong> REACT_APP_API_DOMAIN </strong> with <strong> process.env.REQUEST_URL </strong>

Therefore, everytime we make a request, our React application will look for our current processes configuration variable called REQUEST_URL, which Heroku 
stored, and make requests using that URL. Make sure that after you do this step you remove the .env.production file in the root directory.

Now that we changed our API request URL we have to re-deploy our frontend with this modified changes. To do this,
we simply navigate back to our frontend project directory and type in:
```shell
git add .
```
Which adds all our modified files.

```shell
git commit -m "<message>" .
```

Which commits our newly updated code.

```shell
git push heroku master
```

Will push the new changes to Heroku and update our current project.

Now, our frontend application will make requests to our new request URL. 

### Deploy PostgreSQL
Okay so now we have deployed our frontend and our backend and now we have to deploy our database. Similar to our frontend and backend, our database has been running locally on our machine. Therefore, no one else has access to this data.
We have to deploy our database to the cloud so that everyone that accesses our application shares the same database schema. 

Heroku provides us many ways to configure a Postgres database for our application. in fact, Heroku can offer us a Postgres database that we can connect our application to. 

First, we have to run our migration script. This is a command that will migrate our local database schema to our 
Heroku PostgreSQL database. To do this, we navigate to <strong> src/persist/resources/migrations </strong> directory.
Once we are there, there should be a couple of SQL files. These SQL files are what will migrate our local database to our Heroku
PostgreSQL database. To do this we run the command:
```shell
heroku pg:psql
```
This will start a terminal connected to Heroku's Postgres database that is provisioned to us. You should now see that our terminal says that we have connected to our PostgreSQL database server. 
Now we are running PostgreSQL commands directly on the database server.
 
To run the migrations scripts we run the command:
```shell 
\i <file_name>.sql
```
We have to run this command in order and one at a time for each file. 

Additionally, another script we can run is our seeder script. This script will give our database same base data to use so it is not 
an empty database. To run our seeder script we navigate to our seeder folder which is under <strong> src/persist/resources/seeder</strong>. Once we are in this directory we run the same commands as our migration script:
 
```shell 
\i <file_name>.sql
```
 To leave our database server we run the command:
```shell
\q
``` 
This will return us back to our backend project directory.

 
Once we ran both our migration and seeder scripts, we successfully deployed and migrated
our database to Heroku. We now have a fully functional and complete web application that is accessible by anyone.

Congratulations everyone! Everyone from the Jumpstart team hopes everyone had a lot of fun and learned a lot this semester. If you have any questions do not hesitate to reach out to us!
