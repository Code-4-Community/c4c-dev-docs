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

This router is used to authenticate users. It handles logins, logouts, signups but also things like resetting passwords and secret keys. 

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


## NoteSchema
 
 Represents the database schema for a note. This can be one of the following types:
  -- Comment: a general comment made for an entry or whole timesheet.
  -- Report: a specific report to reflect an incident that happens and requires admin attention, e.g. no-show or late attendance

#### Request body

```json
{
  "Type": ENUM(["Comment", "Report"]),
  "AuthorUUID": STRING.UUOD,
  "DateTime": NUMBER,
  "Content": STRING,
  "State": ENUM(["Active", "Deleted"]),
}
```


## ScheduleEntrySchema

Represents the database schema for a schedule shift entry, made by a supervisor or admin

#### Request body

```json
{
  "StartDateTime": NUMBER,
  "EndDateTime": NUMBER,
}
```


## TimeEntrySchema

Represents the database schema for a clockin/clockout pair in epoch

#### Request body

```json
{
  "StartDateTime": NUMBER,
  "EndDateTime": NUMBER,
}
```


## StatusSchema

 Represents the database schema for the status of a timesheet. This could be one of the following types:
  -- HoursSubmitted (Associate has submitted their hours worked)
  -- HoursReviewed (Supervisor has reviewed and approved the associate-submitted hours)
  -- ScheduleSubmitted (Supervisor has submitted the scheduled hours)
  -- Finalized (Admin has approved the submitted hours and schedule, and resolved any issue necessary)
 
  SubmittedDate reflects the time of last submission, whether from associate, supervisor, or admin.

#### Request body

```json
{
  "StatusType": ENUM([
    "HoursSubmitted",
    "HoursReviewed",
    "ScheduleSubmitted",
    "Finalized",
  ]),
  "SubmittedDateTime": NUMBER,
    
}
```


## TimesheetEntrySchema

Represents the database schema for a single shift or entry in the weekly timesheet.

#### Request body

```json
{
  "AssociateTimes": TimeEntrySchema.optional(),
  "SupervisorTimes": TimeEntrySchema.optional(),
  "AdminTimes": TimeEntrySchema.optional(),
  "Note": NoteSchema.optional(),
    
}
```


## Timesheet

Represents the database schema for a weekly timesheet. A timesheet is defined as one week’s worth of timesheet entries, Sunday through Saturday. A unique timesheet is defined as a timesheet with a unique UserID and TimesheetID pair. There can be multiple timesheets per user.

#### Request body

```json
{
  "TimesheetID": NUMBER,
  "UserID": STRING.UUID,
  "StartDate": NUMBER,
  "StatusList": ARRAY(StatusSchema),
  "CompanyID": STRING,
  "HoursData": ARRAY(TimesheetEntrySchema),
  "ScheduleData": ARRAY(ScheduleEntrySchema),
  "WeekNotes": ARRAY(NoteSchema),
    
}
```


## CompanySchema

Represents the database schema for a company object, including list of users that belong to the company

#### Request body

```json
{
  "CompanyID": STRING,
  "CompanyName": STRING,
  "AssociateIDs": ARRAY(STRING.nonempty()),
  "SupervisorIDs": ARRAY(STRING.nonempty()),  
}
```


## UserCompaniesSchema

Represents the database schema for a user company object, which contains the user ID and the company IDs they belong to

#### Request body

```json
{
  "UserID": STRING,
  "AssociateCompanyIDs": ARRAY(STRING),
  "SupervisorCompanyIDs": ARRAY(STRING), 
}
```