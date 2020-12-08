# HATS School API Specification

## Protected Routes
These routes are called by a user that is not yet authenticated.


### Get All Schools 
Used to return a list of all schools.

`GET` request to `/api/v1/protected/schools`

Responses:

`200 OK`: returns a list of schools and the count of schools returned: 
```json
{
    "count": 2,
    "schools": [
        {
            "id": 1,
            "name": "Northeastern University",
            "address": "360 Huntington Ave",
            "country": "UNITED_STATES"
        },
        {
            "id": 4,
            "name": "School DEF",
            "address": "Some Place in Grenada",
            "country": "GRENADA"
        }
    ]
}
```

`401 Unauthorized`: the user is not authenticated


### Get One School
Used to return information about a single school.

`GET` request to `/api/v1/protected/schools/:school_id`

Responses:

`200 OK`: returns information about the requested school:
```json
{
  "id": 1,
  "name": "Northeastern University",
  "address": "360 Huntington Ave",
  "country": "UNITED_STATES",
  "hidden": false
}
```


### Create One School
Used to create a single school record. Before creating the school,
we check if a school with this name, address, and country already exists.
If yes, and the school was previously deleted or hidden, we un-hide
and un-delete the school.

`POST` request to `/api/v1/protected/schools` with body:
```json
{
    "name": "New School Name",
    "address": "Address 01",
    "country": "DOMINICA",
    "hidden": false
}
```

Responses:

`201 Created`: the school was successfully created / un-hidden / un-deleted

`409 Conflict`: the given school already exists:
```
School 'New School Name' already exists in 'DOMINICA'
```


### Update One School
Used to update the given school to have a new name, address, and country.

`PUT` request to `/api/v1/protected/schools/:school_id` with body:
```json
{
  "name": "Northeastern University",
  "address": "360 Huntington Ave",
  "country": "UNITED_STATES",
  "hidden": false
}
```

Responses:

`200 OK`: the school was successfully updated.


## Delete One School
Used to delete the specified school.

`DELETE` request to `/api/v1/protected/schools/:school_id`

Responses:

`200 OK`: the school was successfully deleted.


## Hide One School
Used to mark the specified school as "hidden".

`PUT` request to `/api/v1/protected/schools/:school_id/hide`

Responses:

`200 OK`: the school was successfully updated.


## Un-Hide One School
Used to mark the specified school as no longer "hidden".

`PUT` request to `/api/v1/protected/schools/:school_id/unhide`

Responses:

`200 OK`: the school was successfully updated.
 