# LLB Local Image Upload Guide
This is a list of the steps required to locally upload images to LLB's S3.

##Run Configurations
1. Open the LLB backend project in IntelliJ
2. Create a new Run Configuration for the ServiceMain class
3. Add the environment variables AWS_ACCESS_KEY and AWS_SECRET_ACCESS_KEY with their corresponding values (ask Jack how they should do this)

##Start the Project
1. Run the ServiceMain run configuration to start the API
2. Run the LLB frontend project (by using npm start)

##Create Admin Account
1. Go to localhost:8080
2. Create a new general member account
3. In IntelliJ open the Database tab and enter the query:

	```
	SELECT * FROM users
	```

4. Edit your account's of privilege_level to 2

You should now be able to upload images when creating events logged in with your account