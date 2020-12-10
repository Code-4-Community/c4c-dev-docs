# HATS School Contacts API Specification

## Protected Routes
All of these routes must be called by a user that is already authenticated.


### Create School Contact
Used to return a list of all schools.

`POST` request to `/api/v1/protected/schools/:school_id/contacts`

Responses:

`401 Unauthorized`: the user is not authenticated

 