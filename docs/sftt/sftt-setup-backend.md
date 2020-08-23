# Setting up the Backend
This is a comprehensive overview of the steps required and everything needed to run the database locally. 

### Downloads
1. Clone the [Github repo](https://github.com/Code-4-Community/speak-for-the-trees-backend)
2. Download [PostgreSQL](https://www.postgresql.org/download/) (Make sure it comes with pgAdmin4)
3. Download [Java JDK 8](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)
4. Download [Java JRE 8](https://www.oracle.com/java/technologies/javase-jre8-downloads.html)
5. Download [IntelliJ](https://www.jetbrains.com/idea/download/) (Community is fine, Ultimate available through student account)

### Installing Maven
1. Download [Maven](https://maven.apache.org/download.cgi)
2. Follow the directions on the [install page](https://maven.apache.org/install.html)
3. Helpful guide on setting environment variables for Windows: [link](https://mkyong.com/java/how-to-set-java_home-on-windows-10/)
4. Make sure the JAVA_HOME enviroment variable is set to the recently downloaded Java JDK 8

### Start a Local Postgres Database
1. Start pgAdmin4
2. A local database should start automatically, by default on port 5432

### Update the Secret Files
1. First, make a copy of every file in `common/src/main/resources/properties`
2. Remove the `.example` extension from every copy
3. In the file `db.properties` make sure the username is `postgres` (or what you set it to) and the password is the password you set up for the database

### Compile the Code Base
1. Run `mvn clean install` from the root directory
2. If the code does not compile after this, remove the target directories from the folders api, common, persist and service. Then try again.
3. Run `mvn spotless:apply` to apply code formatting corrections to your code if your build is failing because of `spotless:check`

### Running the API
The ServiceMain.java class has a main method for running the API. Once running, the API is accessible at `http://localhost:8081`. All routes have the `/api/v1` prefix to them. For example, the HTTP request to get all the teams would be `GET http://localhost:8081/api/v1/teams`.