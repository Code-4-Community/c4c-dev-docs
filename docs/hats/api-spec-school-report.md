# HATS School Report API Specification
## Protected Routes
All of these routes must be called by a user that is already authenticated.

## Add School Report with Library
Used to create a new report for a specified school that has a library.

`POST` request to `/api/v1/protected/schools/:school_id/reports`
 
 Responses:
 
 `201 OK`: returns the report with library that was successfully created for the school.
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

 ## Add School Report Without Library
 Used to create a new report for a specified school that does not have a library.
 
 `POST` request to `/api/v1/protected/schools/:school_id/reports/without-library`
 
 
  Responses: 
  `201 OK`: returns the report that was successfully created for library in progress for the school.
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

 ## Update School Report Without Library (Admin or report creator only)
 Used to update a new report for a specified school that does not have a library.
 
 `PUT` request to `/api/v1/protected/schools/:school_id/reports/without-library/:report_id`

  
``` json
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
 
  Responses: 
  `201 OK`: When report was successfully called



## Update School Report with Library (Admin or report creator only)
Used to update a report for a specified school that has a library.

 `PUT` request to `/api/v1/protected/schools/:school_id/reports/with-library/:report_id`
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
 
 Responses: `201 OK`: returns the report with library that was successfully created for the school.

## Update School Book Log (Admin or report creator only)
Used to update a book log report for a given school

`PUT` request to `/api/v1/protected/schools/:school_id/books/:book_id`

```json
{
  "count": 100,
  "date": Timestamp,
  "notes" : "Book Log notes"

}
```

