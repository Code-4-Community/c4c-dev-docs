# HATS Report API Specification


## Protected Routes
All of these routes must be called by a user that is already authenticated.


## Add Book Log For a School
Used to create a new report for a specified school that has a library.

`POST` request to `/api/v1/protected/schools/:school_id/books`

Responses:

`201 OK`: returns the book log that was successfully created for the school.
```json
{
    "count": 101,
    "date": "2021-02-19T20:01:16Z",
    "notes": "First Shipment"
}
```
 
## Get Log For a School
Used to create a new report for a specified school that has a library.

`GET` request to `/api/v1/protected/schools/:school_id/books`

Responses:

`200 OK`: returns the book log that for the school.
```json
{
    "count": 1,
    "bookLogs": [
      {
        "count": 101,
        "date": "2021-02-19T20:01:16Z",
        "notes": "First Shipment"
      }
    ]
}
```