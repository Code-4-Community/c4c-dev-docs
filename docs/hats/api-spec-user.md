# HATS User API Specification 

## Protected Routes
These routes are for authenticated users

### Update User Data 
Used to update a user's country or privilege level

`PUT` request to `/api/v1/protected/user/:user_id`
```json
{
  "country": "united_states",
  "priviledgeLevel": "standard"
}
```

Response: ``` 200 OK```

### Get list of users
Used to return the list of users. Admin only route.

`GET` request to `/api/v1/protected/user/`

Response: `200 OK` 
```json
{
  "count": 3,
  "users": [
            {
              "firstName": "firstName",
              "lastName": "lastName",
              "email": "email@email.com",
              "country": "dominica",
              "priviledgeLevel": "admin"
            },
            {
              "firstName": "firstName2",
              "lastName": "lastName2",
              "email": "email@email2.com",
              "country": "united_states",
              "priviledgeLevel": "standard"
            },
            {
              "firstName": "firstName3",
              "lastName": "lastName3",
              "email": "email@email3.com",
              "country": "united_states",
              "priviledgeLevel": "standard"
            }
           ]
}
```

Also has the option to get a list of users by country
`GET` request to `/api/v1/protected/user/?country=<country_name>`

Response: `200 OK` 
```json
{
  "count": 2,
  "users": [
            {
              "firstName": "firstName2",
              "lastName": "lastName2",
              "email": "email@email2.com",
              "country": "united_states",
              "priviledgeLevel": "standard"
            },
            {
              "firstName": "firstName3",
              "lastName": "lastName3",
              "email": "email@email3.com",
              "country": "united_states",
              "priviledgeLevel": "standard"
            }
           ]
}
```


