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


### Get All Schools For One Country 
Used to return a list of all schools for the given country

`GET` request to `/api/v1/protected/schools/:country`

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
            "id": 2,
            "name": "Boston University",
            "address": "720 Commonwealth Ave",
            "country": "UNITED_STATES"
        }
    ]
}
```

`400 Bad Request`: the given country is unknown:
```
Unknown country given: america
```


`401 Unauthorized`: the user is not authenticated
