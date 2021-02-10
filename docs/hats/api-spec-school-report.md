# HATS School Report API Specification
## Protected Routes
All of these routes must be called by a user that is already authenticated.

## Add School Report with Library
Used to create a new report for a specified school that has a library.

`POST` request to `/api/v1/protected/schools/:school_id/reports`
 
 Responses:
 
 `200 OK`: returns the report with library that was successfully created for the school.
 ```json
{
    "numberOfChildren": 150,
    "numberOfBooks": 1000,
    "mostRecentShipmentYear": 2018,
    "isSharedSpace": true,
    "hasInvitingSpace": true,
    "assignedPersonRole": "part_time",
    "assignedPersonTitle": "librarian",
    "apprenticeshipProgram": "nep",
    "trainsAndMentorsApprentices": true,
    "hasCheckInTimetables": true,
    "hasBookCheckoutSystem": true,
    "numberOfStudentLibrarians": 100,
    "reasonNoStudentLibrarians": "",
    "hasSufficientTraining": true,
    "teacherSupport": "teacherSupport",
    "parentSupport": "parentSupport"
}
```
 
 ## Add School Report With Library In progress
 Used to create a new report for a specified school that has a library in progress.
 
 `POST` request to `/api/v1/protected/schools/:school_id/reports/in-progress`
  
  Responses:
  
  `200 OK`: returns the report that was successfully created for a school that has a library in progress.
 ```json
{
  "numberOfChildren": 150,
  "numberOfBooks": 1000,
  "mostRecentShipmentYear": 2018,
  "isSharedSpace": false,
  "hasInvitingSpace": true,
  "assignedPersonRole": "part_time",
  "apprenticeTitle": "apprentice",
  "trainsAndMentorsApprentices": false
}
```
 ## Add School Report Without Library
 Used to create a new report for a specified school that des not have a library.
 
 `POST` request to `/api/v1/protected/schools/:school_id/reports/without-library`
 
 
  Responses: 
  `200 OK`: returns the report that was successfully created for library in progress for the school.
   ```json
  {
      "numberOfChildren": 150,
      "numberOfBooks": 1000,
      "mostRecentShipmentYear": 2018,
      "reasonWhyNot": "Does not have space",
      "wantsLibrary": true,
      "hasSpace": false,
      "currentStatus": "in planning",
      "readyTimeline": "soon"
  }
  ```