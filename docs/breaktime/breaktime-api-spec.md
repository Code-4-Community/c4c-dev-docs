# Breaktime API Specification
This is the official API specification for the Breaktime back end. The back end is implemented as is described in the documentation below and should correspond exactly with how the back end behaves. If you find any inconsistencies please create a PR to amend the error.


## Enums

### Privilege Level

The privilege level enum is all the possible privilege levels a user can have. This is stored in the users table as the privilege_level field. The possible options are:

I don’t think we have these yet, leaving this here as a placeholder

### Team Role

The team role enum is all the possible states a use can be in in relation to a team. This is stored in the users_teams table on the backend as the team_role field. The possible options are:

I don’t think we have these yet, leaving this here as a placeholder.

## Auth Router

This router is used to authenticate users. It handles logins, logouts, signups but also things like resetting passwords and secret keys. All of these parts are public, as in users don't need to be authenticated yet to call these routes.

### Login



#### Request Body


#### Responses

##### `201 Created`


##### `400 Bad Request`

##### `401 Unauthorized`

### Sign Up

#### Request Body

#### Responses

##### `201 Created`
 
##### `400 Bad Request`

##### `409 Conflict`

### Log Out


#### Request Body


#### Responses

##### `204 No Content`

### Refresh Access Token



#### Request body


#### Responses

##### `201 Created`


##### `401 Unauthorized`
### Request Password Reset


#### Request Body


#### Responses

##### `200 OK`


##### `400 BAD REQUEST`



## Timesheet

Used to store a user’s timesheet data. A timesheet is defined as one week’s worth of timesheet entries, Sunday through Saturday. A unique timesheet is defined as a timesheet with a unique UserID and TimesheetID pair. There can be multiple timesheets per user.

#### Request body

```json
{
  "UserID": STRING,
  "TimesheetID": STRING,
  "Company": EMAIL,
  "StartDate": NUM,
  "Status": OBJECT,
    "StatusType": STRING,
    "SubmittedDate": NUM,
  "WeekComments": ARRAY,
      "Comment": OBJECT,
        "Type": STRING,
        "AuthorUUID": STRING,
        "Timestamp": STRING,
        "Content": STRING,
  "TableData": ARRAY,
      "StartDate": NUM,
      "EndDate": NUM,
      "Comment": OBJECT
        "Type": STRING
        "AuthorUUID": STRING,
        "Timestamp": STRING,
        "Content": STRING,
      "Duration": NUM
  "ScheduleTableData": ARRAY,
      "ScheduleEntry": OBJECT,
        "StartDate": NUM,
        "Duration": STRING,
    
}
```



