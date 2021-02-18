# HATS School Report API Specification

## Notes

The field `libraryStatus` is represented by an enum that can have the following values:
 - `"UNKNOWN"`
 - `"EXISTS"`
 - `"DOES_NOT_EXIST"`

The field `assignedPersonRole` is represented by an enum that can have the following values:
 - `"PART_TIME"`
 - `"FULL_TIME"`
 
The field `assignedPersonTitle` is represented by an enum that can have the following values:
 - `"LIBRARIAN"`
 - `"SCHOOL_SECRETARY"`
 - `"CLASSROOM_TEACHER"`
 - `"APPRENTICE"`
 - `"PCV"`
 - `"OTHER"`

The field `apprenticeshipProgram` is represented by an enum that can have the following values:
 - `"OECS"`
 - `"NEP"`
 - `"OTHER"`

The field `readyTimeline` is represented by an enum that can have the following values:
 - `"UPCOMING_SCHOOL_YEAR"`
 - `"YEAR_AFTER_NEXT"`
 - `"MORE_THAN_TWO_YEARS"`


## Protected Routes
All of these routes must be called by a user that is already authenticated.


## Add School Report With Library
Used to create a new report for a specified school that has a library.

`POST` request to `/api/v1/protected/schools/:school_id/reports`

Responses:

`201 OK`: returns the report with library that was successfully created for the school.
```json
{
    "numberOfChildren": 20,
    "numberOfBooks": 10,
    "mostRecentShipmentYear": 2019,
    "isSharedSpace": false,
    "hasInvitingSpace": false,
    "assignedPersonRole": "FULL_TIME",
    "assignedPersonTitle": "LIBRARIAN",
    "apprenticeshipProgram": "OECS",
    "trainsAndMentorsApprentices": true,
    "hasCheckInTimetables": false,
    "hasBookCheckoutSystem": true,
    "numberOfStudentLibrarians": 2,
    "reasonNoStudentLibrarians": null,
    "hasSufficientTraining": true,
    "teacherSupport": "Teacher Support Example",
    "parentSupport": "Parent Support Example"
}
```
 

## Add School Report Without Library
Used to create a new report for a specified school that des not have a library.

`POST` request to `/api/v1/protected/schools/:school_id/reports/without-library`

Responses:
 
`201 OK`: returns the report that was successfully created for library in progress for the school.
```json
{
    "numberOfChildren": 20,
    "numberOfBooks": 10,
    "mostRecentShipmentYear": 2019,
    "reasonWhyNot": "Funding",
    "wantsLibrary": true,
    "hasSpace": false,
    "currentStatus": "Found a space",
    "readyTimeline": "YEAR_AFTER_NEXT"
}
```

## Get Paginated Reports For One School
Used to return a list of all reports for the given school.

`GET` request to `/api/v1/protected/schools/:school_id/reports?p=#`

The `p=#` query parameter is **required**. The first page of the response starts at `p=1`.

Responses:

`200 OK`: returns a list of reports:
```json
{
    "count": 5,
    "reports": [
        {
            "id": 1,
            "createdAt": "Fri Feb 05 15:57:52 EST 2021",
            "updatedAt": "Fri Feb 05 15:57:52 EST 2021",
            "schoolId": 439,
            "userId": 1,
            "numberOfChildren": 50,
            "numberOfBooks": 15,
            "mostRecentShipmentYear": 2018,
            "libraryStatus": "EXISTS",
            "isSharedSpace": true,
            "hasInvitingSpace": false,
            "assignedPersonRole": "PART_TIME",
            "assignedPersonTitle": "CLASSROOM_TEACHER",
            "apprenticeshipProgram": "OECS",
            "trainsAndMentorsApprentices": true,
            "hasCheckInTimetables": false,
            "hasBookCheckoutSystem": true,
            "numberOfStudentLibrarians": 2,
            "reasonNoStudentLibrarians": null,
            "hasSufficientTraining": true,
            "teacherSupport": "Teacher Support Example",
            "parentSupport": "Parent Support Example"
        },
        {
            "id": 2,
            "createdAt": "Fri Feb 05 16:20:53 EST 2021",
            "updatedAt": "Fri Feb 05 16:20:53 EST 2021",
            "schoolId": 439,
            "userId": 1,
            "numberOfChildren": 20,
            "numberOfBooks": 10,
            "mostRecentShipmentYear": 2019,
            "libraryStatus": "EXISTS",
            "isSharedSpace": false,
            "hasInvitingSpace": false,
            "assignedPersonRole": "FULL_TIME",
            "assignedPersonTitle": "LIBRARIAN",
            "apprenticeshipProgram": "OECS",
            "trainsAndMentorsApprentices": true,
            "hasCheckInTimetables": false,
            "hasBookCheckoutSystem": true,
            "numberOfStudentLibrarians": 2,
            "reasonNoStudentLibrarians": null,
            "hasSufficientTraining": true,
            "teacherSupport": "Teacher Support Example",
            "parentSupport": "Parent Support Example"
        },
        {
            "id": 2,
            "createdAt": "Thu Feb 11 15:57:06 EST 2021",
            "updatedAt": "Thu Feb 11 15:57:06 EST 2021",
            "schoolId": 439,
            "userId": 1,
            "numberOfChildren": 20,
            "numberOfBooks": 10,
            "mostRecentShipmentYear": 2019,
            "libraryStatus": "DOES_NOT_EXIST",
            "wantsLibrary": true,
            "hasSpace": false,
            "currentStatus": "Found a space",
            "reason": "Funding",
            "readyTimeline": "YEAR_AFTER_NEXT"
        },
        {
            "id": 3,
            "createdAt": "Tue Feb 16 18:29:02 EST 2021",
            "updatedAt": "Tue Feb 16 18:29:02 EST 2021",
            "schoolId": 439,
            "userId": 1,
            "numberOfChildren": 20,
            "numberOfBooks": 10,
            "mostRecentShipmentYear": 2019,
            "libraryStatus": "EXISTS",
            "isSharedSpace": false,
            "hasInvitingSpace": false,
            "assignedPersonRole": "FULL_TIME",
            "assignedPersonTitle": "LIBRARIAN",
            "apprenticeshipProgram": "OECS",
            "trainsAndMentorsApprentices": true,
            "hasCheckInTimetables": false,
            "hasBookCheckoutSystem": true,
            "numberOfStudentLibrarians": 2,
            "reasonNoStudentLibrarians": null,
            "hasSufficientTraining": true,
            "teacherSupport": "Teacher Support Example",
            "parentSupport": "Parent Support Example"
        },
        {
            "id": 3,
            "createdAt": "Tue Feb 16 18:29:38 EST 2021",
            "updatedAt": "Tue Feb 16 18:29:38 EST 2021",
            "schoolId": 439,
            "userId": 1,
            "numberOfChildren": 20,
            "numberOfBooks": 10,
            "mostRecentShipmentYear": 2019,
            "libraryStatus": "DOES_NOT_EXIST",
            "wantsLibrary": true,
            "hasSpace": false,
            "currentStatus": "Found a space",
            "reason": "Funding",
            "readyTimeline": "YEAR_AFTER_NEXT"
        }
    ]
}
```


## Get Latest Reports With Library For One School
Used to return a list of all reports for the given school.

`GET` request to `/api/v1/protected/schools/:school_id/reports/with-library`

Responses:

`200 OK`: returns the latest report:
```json
{
    "id": 3,
    "createdAt": "Tue Feb 16 18:29:02 EST 2021",
    "updatedAt": "Tue Feb 16 18:29:02 EST 2021",
    "schoolId": 439,
    "userId": 1,
    "numberOfChildren": 20,
    "numberOfBooks": 10,
    "mostRecentShipmentYear": 2019,
    "libraryStatus": "EXISTS",
    "isSharedSpace": false,
    "hasInvitingSpace": false,
    "assignedPersonRole": "FULL_TIME",
    "assignedPersonTitle": "LIBRARIAN",
    "apprenticeshipProgram": "OECS",
    "trainsAndMentorsApprentices": true,
    "hasCheckInTimetables": false,
    "hasBookCheckoutSystem": true,
    "numberOfStudentLibrarians": 2,
    "reasonNoStudentLibrarians": null,
    "hasSufficientTraining": true,
    "teacherSupport": "Teacher Support Example",
    "parentSupport": "Parent Support Example"
}
```


## Get Latest Reports Without Library For One School
Used to return a list of all reports for the given school.

`GET` request to `/api/v1/protected/schools/:school_id/reports/with-library`

Responses:

`200 OK`: returns the latest report:
```json
{
    "id": 3,
    "createdAt": "Tue Feb 16 18:29:38 EST 2021",
    "updatedAt": "Tue Feb 16 18:29:38 EST 2021",
    "schoolId": 439,
    "userId": 1,
    "numberOfChildren": 20,
    "numberOfBooks": 10,
    "mostRecentShipmentYear": 2019,
    "libraryStatus": "DOES_NOT_EXIST",
    "wantsLibrary": true,
    "hasSpace": false,
    "currentStatus": "Found a space",
    "reason": "Funding",
    "readyTimeline": "YEAR_AFTER_NEXT"
}
```