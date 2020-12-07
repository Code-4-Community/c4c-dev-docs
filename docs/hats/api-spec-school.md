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


====================================================================================================================

TODO

====================================================================================================================


`GET` request to `/api/v1/protected/schools/:school_id`: returns information about the given school

Response:
```json
{
  "id": 1,
  "name": "Northeastern University",
  "address": "360 Huntington Ave",
  "hidden": false,
  "country": "UNITED_STATES"
}
```

`POST` request to `/api/v1/protected/schools`: creates a new school

Request:
```json
{
  "name": "Northeastern University",
  "address": "360 Huntington Ave",
  "country": "UNITED_STATES"
}
```

`PUT` request to `/api/v1/protected/schools/:school_id`: updates the given school

Request:
```json
{
  "name": "Northeastern University",
  "address": "360 Huntington Ave",
  "country": "UNITED_STATES"
}
```

`DELETE` request to `/api/v1/protected/schools/:school_id`: deletes the given school

`PUT` request to `/api/v1/protected/schools/:school_id/hide`: hides the given school

`PUT` request to `/api/v1/protected/schools/:school_id/unhide`: un-hides the given school

 