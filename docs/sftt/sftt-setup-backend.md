# Setting up the Backend
This is a comprehensive overview of the steps required and everything needed to run the database locally. 

### Download and Install
1. Clone the [Github repo](https://github.com/Code-4-Community/speak-for-the-trees-backend)
2. Download [PostgreSQL](https://www.postgresql.org/download/) (Make sure it comes with pgAdmin4)
3. Download [Java JRE 8](https://www.oracle.com/java/technologies/javase-jre8-downloads.html)
4. Download [IntelliJ](https://www.jetbrains.com/idea/download/) (Community is fine, Ultimate available through student account)

### Setting JAVA_HOME to JDK 8
1. Download [Java JDK 8](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
2. Follow [these steps](https://mkyong.com/java/how-to-set-java_home-on-windows-10/) for Windows
3. Follow [these steps](https://mkyong.com/java/how-to-set-java_home-environment-variable-on-mac-os-x/) for Mac OS
4. Once you have completed the steps, check that it worked by running `java -version` in the command line on Windows and `echo $JAVA_HOME` on Mac OS. This should return something saying the version is 1.8.xxx.

### Installing Maven
1. Download [Maven](https://maven.apache.org/download.cgi)
2. Follow the directions on the [install page](https://maven.apache.org/install.html)

### Start a Local Postgres Database
1. Start pgAdmin4
2. Create a database named `speak-for-the-trees`. Anything else will throw an error later on

### Update the Secret Files
1. Open the repo in IntelliJ
2. Navigate to `common/src/main/resources/properties` and make a copy of every file, including contents
3. Remove the `.example` extension from every copy
4. In the file `db.properties` make sure the username is `postgres` (or what you set it to) and the password is the password you set up for the database. If you didn't choose a password leave this field blank

### Compile the Code Base
1. Run `mvn clean install` from the root directory
2. If the code does not compile after this, remove the target directories from the folders api, common, persist and service, then try again.
3. Run `mvn spotless:apply` to apply code formatting corrections to your code if your build is failing because of `spotless:check`

### Running the API
The ServiceMain.java class has a main method for running the API. Once running, the API is accessible at `http://localhost:8081`. All routes have the `/api/v1` prefix to them. For example, the HTTP request to get all the teams would be `GET http://localhost:8081/api/v1/teams`.