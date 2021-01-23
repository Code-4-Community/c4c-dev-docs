# Working Through a Backend Ticket

Hey there! If you're reading this, you're probably pretty new to C4C, or you're refreshing
on how to tickets. In either case, I'm glad you could stop by to read this document. Here,
we're going to go through the basic process of doing work on a ticket. A lot of this will
probably be common sense, but I still want to get it down so that there will be no confusion 
in the future. For the purpose of this document, we'll be working through a ticket on 
the Backend Scaffold. If you have any questions, _especially_ on this, __please__ reach out
and ask. Good luck on your tickets!

## Agenda

1. Reading Through Your Ticket
2. Planning
    - Project Structure
      - Model, View, Controller (MVC)
    - What will we need to do?
3. Making Changes
    - ???
4. Testing!!!
5. Pull Requests

## Reading Through Your Ticket

Okay. You've gotten your ticket, but now you need to figure out what to do. While the title
is probably a good indication of the general purpose of the ticket, there are probably 
nuances and fine-print details you won't discover until actually reading through the 
description or implementing it. Therefore, the first step you'll want to take is to 
start reading through your ticket. 

Make a note of any questions you may have, and reach
out to your tech/project lead as soon as you can. Since they might have a day off or may 
not get to Slack messages until later, you'll want to get questions out to them
as soon as you can so that they can answer them when they get a chance. Again, the sooner
you can do this, the better, since you don't want to get deep into your project only to find
out you're doing it all wrong. Remember, there are __no stupid questions__. However, if you think you 
have a pretty good idea of the ticket's requirements, don't worry about starting it. 
It's completely alright to start work you don't need answers to any questions, or the 
answers don't block progress (require an answer before you can confidently work).

For the purpose of this document, we'll assume the following is our ticket.

!!! info "Title: Create a Note!"
    __Description:__ Currently, there is no way to add a new note to the database.
    We want to be able to add a note through a REST API and have it be stored in the 
    database. 

    __Points__: 3

??? tip "Definitions"
    Here are a couple of definitions in case you haven't seen them before.
    
    - REST: A set of principles for designing web applications.
    - API: Application Programming Interface. How one application can communicate with 
      another. In the case of Jumpstart, when we mention API, we're referring to 
      the way that our frontend application can make requests to the backend. This is
      managed by the api/ module in the backend repo.
    - Database: A program that enables simpl management of atomic data (numbers, dates, text...).
    - Points: Usually when working using Agile methodologies, tickets, or tasks, are 
      assigned point values representing the amount of work that's expected. 
    - Agile/Scrum: A set of principles for developing software efficiently.


??? question "What are some questions you may have for your project/tech lead?"
    1. What is a note? What fields/properties should a note have? 
        - A note is a data type a user will be relying on to remember information. Notes
          have a date for when they were created, a title, and a body. 
    2. There's no mention of getting the note through an API, is that part of my ticket?
        - Nope, it'll be completed in a separate ticket.
    3.   When you mention date from question 1, does that mean a date or timestamp?
        - Since a timestamp contains information about the day and time, let's use that.
    4. You mention that a note has a timestamp, title, and body, but is it alright to add
       extra fields for other data I might need? Like a user id to match the note to?
        - Sure.

    More questions may come up later, so don't spend too much time trying to get every
    last question answered before starting your ticket. Again, it's alright to start
    work on the ticket as long as there are tasks which don't require answers.

## Planning

??? note "Read this if there are a lot of differences between the backend-scaffold and what we're describing"
    At the time of writing this document, the backend-scaffold's master branch is at 
    `8ecc8893415e145ddfb738151a431e3beb4842e7`. You probably won't have to worry about
    that at all, but if you're seeing major discrepancies between what we're describing
    and what you're seeing, it might make it easier to check out that commit and work
    off of that for a while. If you don't know what that means or how to do it, ask
    your supervisor to help.

To start off, let's take a look at the backend-scaffold. Here's a quick overview of what you're probably seeing.

### Project Structure

- __api/__ is a directory handling routing information and data transfer objects (DTOs).
- __common/__ is a directory contaning useful methods and classes for general use cases. This can include things
  like authorization, passwords, logging, properties, ....
- __persist/__ is a directory for handling and saving data.
- __service/__ holds the main method for your project, so you'll start it up from there. It handles
  connecting everything together and basically 'running' your project.
- __.gitattributes__ is a file with git configurations that are project-specific.
- __.gitignore__ is a file containing regular expressions to describe files we don't want Git
  to keep track of when we make commits or push to GitHub.
  >A regular expression (regex) is a syntax used to define text matching the given pattern.
  >Check out [regexr](https://regexr.com/) and [regex101](https://regex101.com/) for some
  >common regexes, descriptions, and a playground to test some out.
- __.travis.yml__ is a file for handling continuous integration and continuous deployment (CI/CD) setups 
  for our projects.
- __copy_properties.sh__ contains a shell script for copying properties during a CI/CD build.
- __deploy.py__ is a Python script also assisting with deployment after a CI/CD build.
- __Dockerfile__ is a file describing how to build an image of this program for containerization.
- __pom.xml__ is a file with project build instructions, dependencies, and other project information.
- __README.md__ is sort of like the introduction file. It's written in a format called
  [Markdown](https://en.wikipedia.org/wiki/Markdown) (the .md filetype), which allows for
  simple and easy formatting. Usually this file has descriptions of the software, instructions
  for setting it up, and any other information the developer(s) would like to provide to the
  end user.

You might not end up working with a lot of these files (mostly just the files in the four modules: api/, persist/, 
and service/), but it's still super useful to know what they do. Now let's take a look at the structure 
of a basic module.

![Example of a package's structure](./images/module_file_structure.jpg)

Firstly, you should notice that there's another pom.xml here. There is a difference between the pom.xml in the 
root of the project and the poms for each of the modules. The root pom defines project setup, dependency versions, and
plugins, while the module poms actually include the dependencies needed. Pretty much everything else you will work
with lives inside the main/ and test/ directories in src/. Going even deeper, all the actual java files are in
the com.codeforcommunity package. The resources/ directory (which isn't required), contains files that may be used
by the application, like configuration files. 

Here's a more in-depth description of what the four modules actually do.

### Model, View, Controller (MVC)

The api module is where all requests will enter through. It is the way that any external clients will interact with
and view the results of our application.

The common module is what handles authentication, properties, session management, emails, and logging. It's kind of
weird that we have an extra module for this, since all of this can probably just go in the service module, but we
pulled it out since this code __rarely__ changes between projects, unlike all other modules. 

The persist module is where all *persisting* information will live. It's the location of our jOOQ database
object relational management classes, so all interactions with the database take place in there.

The service module is what unifies everything together. Since we want to avoid making the persist and api directories
dependent on each other, there will end up being a few interfaces that are made available and implemented in service.

If you've heard about it in Object Oriented Design (OOD) before, this follows the Model, View, Controller (MVC) design
pattern. Persist is our model, api is our view, and service is our controller. For those of you who don't know, 
this basically just separates out functionality into three different main functionalities.

[You can read more about it here.](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 

- Model: deals with handling the data and functionality with processing data
- View: deals with presenting data (whether visually, textually, or in any other way) in a way that an external entity
 (like you or another program) can understand what is happening and interact with your program
- Controller: deals with linking up interactions between the model and view, and is the mastermind behind running your
 program and handling external entity interactions (like you clicking a button or something else sending requests)

### What will we need to do?

Alright so, we know that we need to add a way to create a note, but what does that actually entail? There are two
main things that are required: 1) creating a way for something to request the creation of a note, and 2) actually
storing the note in the database. Our database up to this point doesn't really have much in it, so that means
we'll also have to create a database migration so that the database knows what to expect. We also don't have any
concept of notes in our API, so we need to add the creation of a new API sub-router to our growing list of tasks. 
And since we haven't handled notes in either the API or database before, there aren't any ways to handle the note
once a request is received and pass it to the database, so we'll need to create a processor implementation. Let's list
all of this out.

1. Modify the API specification to include the new note endpoint
2. Create a new database migration
3. Create a notes sub-router API
4. Create a new notes DTO
5. Create a new notes processor
6. Create a processor method to add the note to the database
7. Create a new create note endpoint

That might seem like a lot to do, and it is relatively complex compared to writing a single method which gets a request
and inserts the new note into the database, but this allows for a much cleaner code structure. You'll also get pretty
used to doing this after a while, so you'll come to realize it's actually not that bad. Also, in most cases, you won't
need to create a new sub-router, processor, or database migration, so future tickets could be much simpler. In fact,
if you decide to create the get note endpoint as a followup ticket, you'll be able to see that for yourself.

Let's see how to actually make these changes.

## Making Changes

For clarity, we'll do this one step at a time, in a slightly different order than what we showed previously. 
We'll also add a step or two. We'll do it like this:

1. Modify the API specification to include the new note endpoint
2. Create a new database migration
3. Create a notes sub-router API
4. Create a new notes DTO
5. Create a new notes processor
6. Create a processor method to add the note to the database
7. Create a new create note endpoint

Before you even start making changes, you'll want to check out a new branch for your feature in git. You can
do that by running `git checkout -b <branch_name>`, where branch_name is usually descriptive of what you're working
on. For example, we'd probably want to run `git checkout -b add-notes` for this branch. Some organizations
also like it when you add your name or initials to the branch name, like `cn.add-notes`, `cn-add-notes`, 
`CN-add-notes`, ..., so you can do that too if you prefer. __If you haven't used git before, be sure you [check out 
the Jumpstart Git Workshop](https://learn.c4cneu.com/jumpstart/week-1.5-git/) before working on a ticket.__ 

Let's get the database migration out of the way first.

### The Database Migration

We'll be starting with the database migration first in this case because we'll be dealing with SQL 
for this task, while the remaining tasks are all in Java. In your future tickets, you can do this part 
later on if you'd prefer.

Again, the persist directory is where the model part and database interactivity parts of our application are located. 
When you open the directory, you'll notice that there is no java/ directory located within src/main/. That's because
we actually don't manually create any classes here. An external ORM dependency, [jOOQ](https://www.jooq.org/), manages
that for us. Therefore, all of our files will be in
the resources/ directory that was mentioned earlier. There's a config set somewhere that tells Flyway, another
dependency for performing migrations, to look for the migration files in the db.migration directory. Flyway will 
run migrations that haven't been performed in the order of their version numbers
the next time the project is built, and jOOQ will generate ORM classes for each table. 

!!! danger
    Remember that __you can never edit a migration once it's been committed to master__, or else environments
    that have already run that migration will either notice something has changed and *delete all information in 
    the database* (which is really dangerous), or *avoid running the change you made, which can put the database in
    a bad state and cause future migrations to behave incorrectly or fail*. Also, __do not add a migration with a 
    version number that is less than or equal to any already existing migration versions__. This will also end up
    deleting any database information, which is *very bad*.

??? tip "Definitions"
    - Migration: A SQL script defining modifications to a database. They're often done in separate steps as migrations
      rather than as one single large SQL script because you can avoid deleting information
      in the database, especially in a production environment. Therefore, each of these migrations describe the
      changes to get the database's structure updated from the previous migration. 
    - Object Relational Mapper (ORM): A library that generates a method of working with
      incompatable type systems (Java objects and SQL database tables in our case) natively from one syntax. In 
      our case, it allows us to write SQL queries using Java classes and interact with classes as if 
      they were an object.

Let's take a look at the already existing tables in the database. If you've run `mvn` or `mvn install` before, 
and the build succeeded, you should be able to see the following tables in whichever data viewer you're using.

- `blacklisted_refreshes`: Contains a list of invalid verification key hashes so that the keys can be invalidated
  when a user logs out.
- `flyway_schema_history`: The current migration status for Flyway, so that it knows which migrations should be applied
  on a build. This table isn't created in a migration file, it's created automatically for us by Flyway.
- `users`: a table containing user information.
- `verification_keys`: Session information for a user.

!!! info
    You can feel free to use whichever data viewer you would like. We recommend against using the PostgreSQL 
    console directly (unless you need to for certain tasks), and instead think you should focus on using PGAdmin, 
    the Database Viewer in IntelliJ, or the JetBrains DataGrip. 

We're going to want another table, `notes`, to store notes for a use. If you know SQL, try creating one on your
own in a `V2__Notes_Table.sql` file in the same directory that `V1__Initial_Import.sql` exists in 
(*there are __two__ underscores between V2 and Notes*). We'll have an answer in the dropdown below.

??? question "Can you write a migration for the notes table?"
    ``` postgresql
    CREATE TABLE IF NOT EXISTS notes (
        id          SERIAL          PRIMARY KEY,
        created     TIMESTAMP       NOT NULL    DEFAULT CURRENT_TIMESTAMP,
        title       VARCHAR(255)    NOT NULL,
        body        TEXT            NOT NULL,
        user_id     INTEGER         NOT NULL,
        CONSTRAINT notes_user_id_fk FOREIGN KEY (user_id)
            REFERENCES users (id)
    );
    ```
Now that we have the migration created you may be wondering two very important questions:

1. How do we run this migration in the database?
2. How do we get jOOQ to create our ORM classes?

Fortunately, the answer to both questions is easy. All you have to do is run Maven! With migrations, you'll 
want to run `mvn clean install` (a shorthand for this command is just `mvn`!), since sometimes issues can pop 
up when jOOQ is constructing your ORM classes. Try it out, and then if you go to `ServiceMain` in the service/ 
module and confirm that IntelliJ will let you import it. Your class should be named `NotesRecord`, and you'll
have to import it from the `org.jooq.generated.tables.records` package. If you ++cmd+"click"++ or ++ctrl+"click"++ 
the `NotesRecord` class, you can see a bit about the class jOOQ has generated for us. Don't forget to delete
the record class in service/ you added when you're done.

??? error "If you get an error mentioning `@Generated`, read this!"
    If you're getting the `@Generated` error, that means the wrong version of Java was used with Maven.
    To fix this, you'll want to make sure you're running Maven with the IntelliJ Maven tool. You should be 
    able to locate it at the top of the right toolbar.

    If you're still getting issues, go to File > Project Structure > Project, and make sure your language
    level is 8. You may also need to set the Project SDK to Java 11 too. If that doesn't solve it,
    reach out to your supervisor for help.

Cool, now we're done with working in persist/. Let's move on to creating the Note sub-router.

### The Notes Sub-router

Creating a new sub-router is pretty simple, but first let's take a look at what routing even means. Routing is
when an application receives a request at a certain endpoint, usually a path in a URL, and performs some associated 
functionality. In the api/ module directory, the main entrypoint is the `ApiMain` class, which is called by `ServiceMain`. 
This class handles starting up the part of the server that listens for incoming connections. Part of the `startApi()` 
method of `ApiMain` is that the `ApiRouter` class is set as the application's main router. What that means is that 
the `ApiRouter` handles actually performing the expected functionality when a route is called. 

<> TODO: find documentation describing routing and stuff

At C4C, we have created an `IRouter` interface, detailing a method routers and subrouters must implement, so that
they can be properly initialized. This interface defines a single method,`initializeRouter(Router)`, which 
handles setting up the router's information. We'll discuss this in further depth soon.

The most important thing that the `ApiRouter` does in its `initializeRouter()` method is register sub-routers for
each of the different endpoints it provides. A sub-router is a router that works mounted at a specific endpoint.
As an example, if we were to mount the notes router at /notes, then each route we register in the notes sub-router
will be registered at notes/<your_notes_route_here>. Sub-routers also allow us to register certain methods
to be called before the actual route handler, so we can perform operations before the routes in a certain
sub-router are called, like ensuring a user is logged in. Let's actually go through the process of creating a 
notes sub-router and mount it within the `ApiRouter`, so that the route can be called.

Let's create the `NoteRouter` sub-router class in the `com.codeforcommunity.rest.subrouter` package, which 
implements the `IRouter` interface. It should look something like this:

```java
public class NotesRouter implements IRouter {
  @Override
  public Router initializeRouter(Vertx vertx) {
    Router router = Router.router(vertx);
    
    return router;
  }
}
```

Pretty simple, right? All we want to do to start out is create our new sub-router for handling all things relating 
to notes. We'll also want to add this to the `ApiRouter` class so that it can be registered. To do that, we'll want
to adjust the following methods:

```java 
public class ApiRouter implements IRouter {
  private final CommonRouter;
  private final AuthRouter authRouter;
  private final ProtectedUserHandler protectedUserHandler;
  private final NotesRouter notesHandler;
  
  public ApiRouter(
      IAuthProcessor authProcessor,
      IProtectedUserProcessor protectedUserProcessor,
      JWTAuthorizer jwtAuthorizer) {
    this.commonRouter = new CommonRouter(jwtAuthorizer);
    this.authRouter = new AuthRotuer(authProcessor);
    this.protectedUserRouter = new ProtectedUserRouter(protectedUserProcessor);
    this.notesHandler = new NotesHandler();
  }
  
  ...
  
  private Router defineProtectedRoutes(Vertx vertx) {
    Router router = Router.router(vertx);
    
    router.mountSubRouter("/user", protectedUserRouter.initializeRouter(vertx));
    router.mountSubRouter("/notes", notesRouter.initializeRouter(vertx));

    return router;
  }

  ...
}
```

!!! info
    The lines we actually added stuff on are 5, 14, and 23.

As you can see in the previous example, all we did was add and instantiate a new `NotesRouter`. We'll need to create
the interface to a processor which can handle our new notes.

### Create a new notes DTO

You've probably seen the term "DTO" a couple of times now. A DTO, or Data Transfer Object, is a class whose only 
purpose is to hold information and transfer the information between different places. Let's go through the process
of creating a new Notes DTO. 

If you look in `com.codeforcommunity.dto`, you can see some of the classes that have 
been created for DTOs. You'll notice a couple of other packages for groups of DTO types, as well as an `ApiDto` abstract 
class, which deals with defining DTO validation for incoming data. The DTO we're about to create, which will deal with
handling incoming note JSON objects, will utilize `ApiDto` for ensuring some of the properties we guaranteed in
our persist/ migrations. Since the other classes we see in the `dto` package house DTOs in packages referring to 
the data type's name, let's do the same for notes; let's create a note package. In there we'll create a
`CreateNoteRequest` DTO. The following is what the `CreateNoteRequest` DTO will look like.

```java 
public class CreateNoteRequest extends ApiDto {
  private String title;
  private String body;

  private CreateNoteRequest() {
  }

  public String getTitle() {
    return this.title;
  }

  public String getBody() {
    return this.body;
  }

  public CreateNoteRequest setTitle(String title) {
    this.title = title;
    return this;
  }

  public CreateNoteRequest setBody(String body) {
    this.body = body;
    return this;
  }

  @Override
  public List<String> validateFields(String fieldPrefix) throws HandledException {
    String fieldName = fieldPrefix + "create_note_request.";
    List<String> invalidFields = new ArrayList<>();

    if (this.title == null) {
      invalidFields.add(fieldName + "title");
    }
    if (this.body == null) {
      invalidFields.add(fieldName + "body");
    }

    return invalidFields;
  }
}
```

As you can see, it basically just has getters and setters, as well as a `validate(String)` method, which 
you can use to define whether or not a given field is valid or not. If it's not valid, you can add the field name
to a list which gets returned from the method, indicating the fields which have an invalid value. 

If you remember what we had in our migration, you've probably noticed that there are a few fields missing
from the DTO that we'll need for the database. Two of them, the `id` and `date` fields, will be handled for us
by the database. The `SERIAL` value we added to modify the `id` field tells the database that it can
automatically set the value using an auto increment, so each insert will have the `id` of the previous entry
+ 1. Similarly, we manually set a `DEFAULT` on the `date` field to automatically enter the current timestamp
as a value for that field (unless explicitly specified, but we won't be doing that).

The other field, `user_id`, can be retrieved from methods provided later on, 
so it's not something that has to be included as part of the request. You also wouldn't want to include that
as part of the request because then people would be able to set notes as other people! Make sure you pay attention
to where you get user information from, and ensure you're not introducing possible bugs or security issues 
into the application.

We're all done with the DTO now, so let's take a look at the processor.

### Create a new notes processor

Now we need to create what's called a processor for notes. In C4C, we use the term processor to define
a class which deals with the interactions between an API and jOOQ type, both for incoming and outgoing data.
















Again, in the api/ module, there's an api/ package at `com.codeforcommunity` where you can define a processor's 
interface. We'll be creating the `INotesProcessor` interface right now. Since we're only planning to add a 
create-note-like route, we'll ad


