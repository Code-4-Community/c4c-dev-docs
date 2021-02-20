# Jumpstart Back End
The backend for the C4C Jumpstart project.

Hello Jumpstarters! We're really excited to be working on this project with you!
In this project, we're going to be making a blog clone in order to give you
experience with working on a separated front-end and back-end, external dependencies, 
web requests, and the organization and feel of what a project you may
work on during Co-op is like.

## Project Setup

### Dependencies
First thing we need to do is make sure you have everything downloaded to set
up the back end. Please make sure you've downloaded 
(please do these in order, you may also need to restatrt your terminal after
installing some of these):
- [ ] [Java 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
  - If you're not sure if you already have this, try out the following commands
    - For Mac/Linux: `echo $JAVA_HOME`
    - For Windows: `echo %JAVA_HOME%`
  - If it's installed, it should output a filepath
    - That filepath should look something like 
    `C:\Program Files\AdoptOpenJDK\jdk-someVersion\` or
    `/Library/Java/JavaVirtualMachines/jdkSomeVersion/other/stuff`
    - Make sure that `someVersion` is 11
    - It's okay if after you install Java 11, your JAVA_HOME doesn't change --
    we'll fix that below
- [ ] [Git](https://git-scm.com/downloads)
  - If you're not sure if you have this, try running `git --version`
- [ ] [Maven](https://maven.apache.org/download.cgi)
  - If you're not sure if you have this, try running `mvn --version`
  - Once you've downloaded it, go through the 
  [install instructions](https://maven.apache.org/install.html)
- [ ] [IntelliJ](https://www.jetbrains.com/idea/)
  - You don't have to use IntelliJ if you don't want to, but some of the instructions
  we provide may be IntelliJ specific. For most of them, the workarounds will
  be pretty trivial though.
  - The full version is free if you're a student!
- [ ] [PostgreSQL and PGAdmin](https://www.postgresql.org/download/)
  - We'd recommend using a version >10 (we're using v12)
  - We recommend doing it by the "Interactive installer by EDB" if you're on Mac or Windows
    - Just step through the installer and make sure you get both `PostgreSQL Server` and `pgAdmin 4`
  
### Configurations
- [ ] Make sure your JAVA_HOME is set correctly
  - If you had to install Java 11 as one of the steps above, rerun
  that `echo` command above and see if the path has `someVersion` as Java 11
  - If it's still not Java 11, follow these steps for 
  [Windows](https://maven.apache.org/install.html#windows-tips) or
  [Mac/Linux](https://maven.apache.org/install.html#unix-based-operating-system-linux-solaris-and-mac-os-x-tips)
  - Once you do that, restart your terminal (and your text editor if you were using
  that as your terminal), and run `mvn --version`. Make sure the Java version is 11.
- [ ] If you're on Windows, we're going to set your in-IntelliJ terminal to use *Git Bash*
Instead of the command prompt (working with bash is more pleasant in our opinion.
also, a lot of commands carry over, and it should be easy to figure out the equivalent from Google.)
  - Go to `File > Settings > Tools > Terminal` and replace your shell path with
  `C:\Program Files\Git\bin\sh.exe`
  
## Building and Running the Back End

### Building your project
Run `mvn clean spotless:apply install` to build your project.
- There's a configuration set up in the root pom that will autocomplete
the above command if you just type `mvn`, but try it out both ways!

Let's break that command down.
- `mvn ...` calls the [Maven tool](https://maven.apache.org/index.html), whose
job it is to manage building and performing certain operations on your project
  - Run `mvn --help` to see other operations it can do.
- `clean` cleans up the project after a build. Usually this involves deleting all
of the 'target' directories within the project.
  - If you're having issues building your project, and `mvn clean` doesn't
  get rid of your target directories, try manually deleting them
- `spotless:apply` applies a formatter using the Google style guide to all of your Java code. 
  - This includes formatting, getting rid of unused dependencies, ordering imports, 
  and trimming whitespace.
  - If you're getting errors running this (or during this stage), you probably have a 
  syntax error somewhere, or you added a comment in the imports area of your file. Usually
  a hint will be provided about which file is having issues, so check in that file first.
  - There's also a `spotless:check` function, which just provides checking without cleanup.
- `install` compiles your project, performs validation and tests, and 'installs' your project
into the current directory.
  - This command actually ends up calling all of the commands above it in 
  [this table](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#default-lifecycle-bindings-packaging-maven-plugin)
  and then installs whatever has been built into the current directory

If you're having issues, read the logs. They are a bit intimidating and complicated,
but usually they can provide some hints as to what went wrong during the build. It's usually
a syntax error or typo that causes issues that would cause Maven to fail a build.

### Running Your Project
You have two options for running your project. We prefer running it in IntelliJ,
since it's pretty simple (and the debugger is really nice), but you can use whichever way you prefer.

#### Running Using IntelliJ
1. Build your project (run `mvn`)
2. Open to the `ServiceMain` file in `service/src/main/java/com.codeforcommunity/`
3. Click on one of the green start arrows
  - To just run the program, click the green arrow in the dropdown
  - To debug your program, select the debugger line instead
    - To set breakpoints that your debugger will hit, just click next to a line number,
    and a red circle should appear letting you know your program will pause when you hit
    that line
4. Wait until a message letting you know the server has been started in the console
5. Congrats! You have a working server!
6. Hit the red square when you're ready to stop your program

#### Running Using the Command Line
1. Build your project (run `mvn`)
2. Run the following command
`java -jar service/service-1.0-SNAPSHOT-jar-with-dependencies.jar`
  - If you changed your version number, then substitute `1.0-SNAPSHOT` with
  that other version
3. Wait until a message letting you know the server has been started in the console
4. Congrats! You have a working server!
5. Press `ctrl + c` when you're done
    - P.S. `ctrl + c` is for both Windows *and* Mac

## A Quick Test
To run a quick test to show that your program is working. Run the following command in a new terminal window
and you should get a response.
`curl http://localhost:8081`

P.S. [curl](https://curl.haxx.se/) is a command line tool for transferring data with URLs. We won't
really go into that in this project, but it's definitely something worth checking out and knowing about.