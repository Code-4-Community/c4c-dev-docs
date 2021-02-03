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
    "count": 1,
    "schoolContacts": [
        {
            "id": 3,
            "schoolId": 1,
            "firstName": "Johnny",
            "lastName": "Appleseed",
            "email": "principal@gmail.com",
            "address": "New Address",
            "phone": "6010001111",
            "type": "PRINCIPAL"
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
    "id": 3,
    "schoolId": 1,
    "firstName": "Johnny",
    "lastName": "Appleseed",
    "email": "principal@gmail.com",
    "address": "New Address",
    "phone": "6010001111",
    "type": "PRINCIPAL"
}
```

`401 Unauthorized`: the user is not authenticated


### Create School Contact
Used to create a new contact for the specified school.

`POST` request to `/api/v1/protected/schools/:school_id/contacts` with body:
```json
{
    "firstName": "Johnny",
    "lastName": "Appleseed",
    "email": "principal@gmail.com",
    "address": "New Address",
    "phone": "6010001111",
    "type": "PRINCIPAL"
}
```

Responses:

`201 Created`: the school contact was successfully created:
```json
{
    "id": 1,
    "schoolId": 439,
    "firstName": "Johnny",
    "lastName": "Appleseed",
    "email": "principal@gmail.com",
    "address": "New Address",
    "phone": "6010001111",
    "type": "PRINCIPAL"
}
```

`401 Unauthorized`: the user is not authenticated


### Update School Contact
Used to update the specified contact.

`PUT` request to `/api/v1/protected/schools/:school_id/contacts/:contact_id` with body:
```json
{
    "firstName": "John",
    "lastName": "Apple",
    "email": "principal@gmail.com",
    "address": "New Address",
    "phone": "6010002222",
    "type": "PRINCIPAL"
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
 