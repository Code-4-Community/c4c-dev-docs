# HATS School API Specification

## Notes

The field `libraryStatus` is represented by an enum that can have the following values:
 - `"UNKNOWN"`
 - `"EXISTS"`
 - `"DOES_NOT_EXIST"`
 
The field `country` is represented by an enum that can have the following values:
 - `"UNITED_STATES"`
 - `"ANTIGUA_AND_BARBUDA"`
 - `"DOMINICA"`
 - `"GRENADA"`
 - `"ST_KITTS_AND_NEVIS"`
 - `"ST_LUCIA"`
 - `"ST_VINCENT_AND_THE_GRENADINES"`


## Protected Routes
All of these routes must be called by a user that is already authenticated.


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
            "country": "UNITED_STATES"
        },
        {
            "id": 4,
            "name": "School DEF",
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
    "id": 439,
    "name": "Northeastern University",
    "address": "360 Huntington Ave",
    "email": "office@northeastern.edu",
    "phone": "617-000-1234",
    "notes": "Best University",
    "area": "Greater Boston",
    "country": "UNITED_STATES",
    "hidden": false,
    "libraryStatus": "EXISTS",
    "contacts": null
}
```


### Create One School
Used to create a single school record. Before creating the school,
we check if a school with this name, address, and country already exists.
If yes, and the school was previously deleted or hidden, we un-delete the
and update the hidden field to the given value.

`POST` request to `/api/v1/protected/schools` with body:
```json
{
    "name": "Northeastern University",
    "address": "360 Huntington Ave",
    "country": "UNITED_STATES",
    "phone": "617-000-1234",
    "email": "office@northeastern.edu",
    "area": "Greater Boston",
    "notes": "Best University",
    "hidden": false,
    "libraryStatus": "EXISTS"
}
```

Responses:

`201 Created`: the school was successfully created / un-hidden / un-deleted:
```json
{
    "id": 439,
    "name": "Northeastern University",
    "address": "360 Huntington Ave",
    "email": "office@northeastern.edu",
    "phone": "617-000-1234",
    "notes": "Best University",
    "area": "Greater Boston",
    "country": "UNITED_STATES",
    "hidden": false,
    "libraryStatus": "EXISTS",
    "contacts": []
}
```

`409 Conflict`: the given school already exists:
```
School 'New School Name' already exists in 'DOMINICA'
```


### Update One School
Used to update the given school to have a new name, address, and country.

`PUT` request to `/api/v1/protected/schools/:school_id` with body:
```json
{
    "name": "Tufts Uni",
    "address": "123 Main Street",
    "country": "UNITED_STATES",
    "phone": "617-111-0000",
    "email": "office@tufts.edu",
    "notes": "Tufts Medical School",
    "area": "Greater Boston",
    "hidden": false,
    "libraryStatus": "DOES_NOT_EXIST"
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

`200 OK`: the school was successfully updated

## Get School IDs from User ID in Reports
Used to get all the schools that a user has submitted a report for.

`GET` request to `/api/v1/protected/schools/reports/users`

Responses:

`200 OK`
```json
{
    "count": 1,
    "schools": [
        {
            "id": 439,
            "name": "Northeastern University",
            "country": "UNITED_STATES"
        }
    ]
}
```
 
 