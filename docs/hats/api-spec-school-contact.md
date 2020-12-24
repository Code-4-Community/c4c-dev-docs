# HATS School Contacts API Specification

## Protected Routes
All of these routes must be called by a user that is already authenticated.


### Get School's Contacts
Used to return a list of all contacts associated with the specified school.

`GET` request to `/api/v1/protected/schools/:school_id/contacts`

Responses:

`200 OK`: returns the list of contacts for the specified school
```json
{
    "count": 2,
    "schoolContacts": [
        {
            "id": 1,
            "schoolId": 1,
            "name": "Contact Two",
            "email": "two@gmail.com",
            "address": "Two Address",
            "phone": "1112223333"
        },
        {
            "id": 2,
            "schoolId": 1,
            "name": "Contact One",
            "email": "one@gmail.com",
            "address": "One Address",
            "phone": "6012223456"
        }
    ]
}
```

`401 Unauthorized`: the user is not authenticated

### Get One School Contact
Used to return a single school contact.

`GET` request to `/api/v1/protected/schools/:school_id/contacts/:contact_id`

Responses:

`200 OK`: returns the list of contacts for the specified school:
```json
{
  "id": 1,
  "schoolId": 2,
  "name": "John Doe",
  "email": "john.doe@gmail.com",
  "address": "Boston",
  "phone": "6015550000"
}
```

`401 Unauthorized`: the user is not authenticated


### Create School Contact
Used to create a new contact for the specified school.

`POST` request to `/api/v1/protected/schools/:school_id/contacts` with body:
```json
{
  "name": "John Doe",
  "email": "john.doe@gmail.com",
  "address": "Boston",
  "phone": "6015550000"
}
```

Responses:

`201 Created`: the school contact was successfully created:
```json
{
  "id": 1,
  "schoolId": 2,
  "name": "John Doe",
  "email": "john.doe@gmail.com",
  "address": "Boston",
  "phone": "6015550000"
}
```

`401 Unauthorized`: the user is not authenticated


### Update School Contact
Used to update the specified contact.

`PUT` request to `/api/v1/protected/schools/:school_id/contacts/:contact_id` with body:
```json
{
  "name": "John Doe",
  "email": "john.doe@gmail.com",
  "address": "Boston",
  "phone": "6015550000"
}
```

Responses:

`200 OK`: the school contact was successfully updated

`401 Unauthorized`: the user is not authenticated

 
### Delete School Contact
Used to delete the specified contact.
 
`DELETE` request to `/api/v1/protected/schools/:school_id/contacts/:contact_id`
 
Responses:
 
`200 OK`: the school contact was successfully deleted
 
`401 Unauthorized`: the user is not authenticated
 