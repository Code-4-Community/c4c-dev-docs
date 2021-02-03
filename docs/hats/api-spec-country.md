# HATS School API Specification

## Notes

The field `country` is represented by an enum that can have the following values:
 - `"UNITED_STATES"`
 - `"ANTIGUA_AND_BARBUDA"`
 - `"DOMINICA"`
 - `"GRENADA"`
 - `"ST_KITTS_AND_NEVIS"`
 - `"ST_LUCIA"`
 - `"ST_VINCENT_AND_THE_GRENADINES"`

## Protected Routes
These routes are called by a user that is not yet authenticated.


### Get All Schools In Country
Used to return a list of all schools for the given country.

`GET` request to `/api/v1/protected/countries/:country/schools`

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
            "id": 2,
            "name": "Boston University",
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
