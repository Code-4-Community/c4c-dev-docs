# HATS Report API Specification

## Notes

The field `libraryStatus` is represented by an enum that can have the following values:

- `"UNKNOWN"`
- `"EXISTS"`
- `"DOES_NOT_EXIST"`

The field `assignedPersonRole` is represented by an enum that can have the following values:

- `"PART_TIME"`
- `"FULL_TIME"`
- `"NONE"`

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

The field `gradesAttended` is represented by an enum that can have the following values:

- `KINDERGARTEN`
- `FIRST_GRADE`
- `SECOND_GRADE`
- `THIRD_GRADE`
- `FOURTH_GRADE`
- `FIFTH_GRADE`
- `SIXTH_GRADE`
- `FORM_ONE`
- `FORM_TWO`
- `FORM_THREE`
- `FORM_FOUR`
- `FORM_FIVE`

The field `timetable`:

- May be `null`
- When non-null, has two **required** fields: `year` and `month`
- All other fields are **optional**
- Any other field present must be the camel case representation of a grade (e.g. `kindergarten`, `firstGrade`)
- All grade fields must be of the following format: `"GRADE": { "DAY_OF_MONTH": INT_COUNT }`
- For example, consider the follow `timetable` field:

```json
{
  "timetable": {
    "year": 2021,
    "month": 7,
    "kindergarten": {
      "2": 19
    },
    "firstGrade": {
      "2": 13,
      "4": 30
    }
  }
}
```

This above `timetable` can be interpreted to mean that:

- On July 2, 2021: there were 19 check-ins from the Kindergarten class
- On July 2, 2021: there were 13 check-ins from the First Grade class
- On July 4, 2021: there were 30 check-ins from the First Grade class

## Protected Routes

All of these routes must be called by a user that is already authenticated.

## Add School Report With Library

Used to create a new report for a specified school that has a library.

`POST` request to `/api/v1/protected/schools/:school_id/reports/with-library` with body:

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
  "parentSupport": "Parent Support Example",
  "visitReason": "A Visit Reason Example",
  "actionPlan": "A short action plan!",
  "successStories": "Some success story here",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name",
  "timetable": {
    "year": 2021,
    "month": 7,
    "kindergarten": {
      "2": 19,
      "16": 12,
      "19": 10
    },
    "firstGrade": {
      "1": 10,
      "3": 30
    }
  }
}
```

Responses:

`201 OK`: returns the report with library that was successfully created for the school.

```json
{
  "id": 2,
  "createdAt": "Mon May 10 12:01:42 EDT 2021",
  "updatedAt": "Mon May 10 12:01:42 EDT 2021",
  "schoolId": 439,
  "userId": 1,
  "numberOfChildren": 20,
  "numberOfBooks": 10,
  "mostRecentShipmentYear": 2019,
  "libraryStatus": "EXISTS",
  "visitReason": "A Visit Reason Example",
  "actionPlan": "A short action plan!",
  "successStories": "Some success story here",
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
  "parentSupport": "Parent Support Example",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name",
  "timetable": {
    "year": 2021,
    "month": 7,
    "kindergarten": {
      "2": 19,
      "16": 12,
      "19": 10
    },
    "firstGrade": {
      "1": 10,
      "3": 30
    }
  }
}
```

## Add School Report Without Library

Used to create a new report for a specified school that des not have a library.

`POST` request to `/api/v1/protected/schools/:school_id/reports/without-library` with body:

```json
{
  "numberOfChildren": 20,
  "numberOfBooks": 10,
  "mostRecentShipmentYear": 2019,
  "reasonWhyNot": "Funding",
  "wantsLibrary": true,
  "hasSpace": false,
  "currentStatus": "Found a space",
  "readyTimeline": "YEAR_AFTER_NEXT",
  "visitReason": "A Visit Reason Example",
  "actionPlan": "A short action plan!",
  "successStories": "Some success story here",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name"
}
```

Responses:

`201 OK`: returns the report that was successfully created for library in progress for the school.

```json
{
  "id": 1,
  "createdAt": "Mon May 10 11:59:28 EDT 2021",
  "updatedAt": "Mon May 10 11:59:28 EDT 2021",
  "schoolId": 439,
  "userId": 1,
  "numberOfChildren": 20,
  "numberOfBooks": 10,
  "mostRecentShipmentYear": 2019,
  "libraryStatus": "DOES_NOT_EXIST",
  "visitReason": "A Visit Reason Example",
  "actionPlan": "A short action plan!",
  "successStories": "Some success story here",
  "wantsLibrary": true,
  "hasSpace": false,
  "currentStatus": "Found a space",
  "reason": "Funding",
  "readyTimeline": "YEAR_AFTER_NEXT",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name"
}
```

## Get Paginated Reports For One School

Used to return a list of all reports for the given school.

`GET` request to `/api/v1/protected/schools/:school_id/reports?p=#`

The `p=#` query parameter is **required**. The first page of the response starts at `p=1`.

Responses:

`200 OK`: returns a list of reports where `count` indicates the **total number of reports** available for this school,
**NOT** the number of reports returned in this response. Each page contains **at most 10 reports**.

```json
{
  "count": 4,
  "reports": [
    {
      "id": 1,
      "createdAt": "Mon May 10 11:53:31 EDT 2021",
      "updatedAt": "Mon May 10 11:53:31 EDT 2021",
      "schoolId": 439,
      "userId": 1,
      "numberOfChildren": 20,
      "numberOfBooks": 10,
      "mostRecentShipmentYear": 2019,
      "libraryStatus": "EXISTS",
      "visitReason": "check-in",
      "actionPlan": "A short action plan!",
      "successStories": "Some success story here",
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
      "parentSupport": "Parent Support Example",
      "timetable": null
    },
    {
      "id": 1,
      "createdAt": "Mon May 10 11:59:28 EDT 2021",
      "updatedAt": "Mon May 10 11:59:28 EDT 2021",
      "schoolId": 439,
      "userId": 1,
      "numberOfChildren": 20,
      "numberOfBooks": 10,
      "mostRecentShipmentYear": 2019,
      "libraryStatus": "DOES_NOT_EXIST",
      "visitReason": "A Visit Reason Example",
      "actionPlan": "A short action plan!",
      "successStories": "Some success story here",
      "wantsLibrary": true,
      "hasSpace": false,
      "currentStatus": "Found a space",
      "reason": null,
      "readyTimeline": "YEAR_AFTER_NEXT"
    },
    {
      "id": 2,
      "createdAt": "Mon May 10 12:01:42 EDT 2021",
      "updatedAt": "Mon May 10 12:01:42 EDT 2021",
      "schoolId": 439,
      "userId": 1,
      "numberOfChildren": 20,
      "numberOfBooks": 10,
      "mostRecentShipmentYear": 2019,
      "libraryStatus": "EXISTS",
      "visitReason": "A Visit Reason Example",
      "actionPlan": "A short action plan!",
      "successStories": "Some success story here",
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
      "parentSupport": "Parent Support Example",
      "timetable": null
    },
    {
      "id": 2,
      "createdAt": "Mon May 10 12:02:39 EDT 2021",
      "updatedAt": "Mon May 10 12:02:39 EDT 2021",
      "schoolId": 439,
      "userId": 1,
      "numberOfChildren": 20,
      "numberOfBooks": 10,
      "mostRecentShipmentYear": 2019,
      "libraryStatus": "DOES_NOT_EXIST",
      "visitReason": "A Visit Reason Example",
      "actionPlan": "A short action plan!",
      "successStories": "Some success story here",
      "wantsLibrary": true,
      "hasSpace": false,
      "currentStatus": "Found a space",
      "reason": null,
      "readyTimeline": "YEAR_AFTER_NEXT"
    }
  ]
}
```

## Get Latest Report For One School

Used to return the most recent report for the given school. Report type given by `libraryStatus`

`GET` request to `/api/v1/protected/schools/:school_id/report`

Responses:

`200 OK`: returns the latest report without library if `libraryStatus` is `"EXISTS"`:

```json
{
  "id": 1,
  "createdAt": "Mon May 10 11:53:31 EDT 2021",
  "updatedAt": "Mon May 10 11:53:31 EDT 2021",
  "schoolId": 439,
  "userId": 1,
  "numberOfChildren": 20,
  "numberOfBooks": 10,
  "mostRecentShipmentYear": 2019,
  "libraryStatus": "EXISTS",
  "visitReason": "check-in",
  "actionPlan": "A short action plan!",
  "successStories": "Some success story here",
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
  "parentSupport": "Parent Support Example",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name",
  "timetable": {
    "year": 2021,
    "month": 7,
    "thirdGrade": {
      "16": 12,
      "19": 10
    },
    "formOne": {
      "1": 10,
      "3": 30
    }
  }
}
```

`200 OK`: returns the latest report without library if `libraryStatus` is `"DOES_NOT_EXIST"`:

```json
{
  "id": 2,
  "createdAt": "Mon May 10 12:02:39 EDT 2021",
  "updatedAt": "Mon May 10 12:02:39 EDT 2021",
  "schoolId": 439,
  "userId": 1,
  "numberOfChildren": 20,
  "numberOfBooks": 10,
  "mostRecentShipmentYear": 2019,
  "libraryStatus": "DOES_NOT_EXIST",
  "visitReason": "A Visit Reason Example",
  "actionPlan": "A short action plan!",
  "successStories": "Some success story here",
  "wantsLibrary": true,
  "hasSpace": false,
  "currentStatus": "Found a space",
  "reason": null,
  "readyTimeline": "YEAR_AFTER_NEXT",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name"
}
```

`404 No Report Found`: Report not found for school with id

## Update School Report With Library

Used to update a new report for a specified school that has a library.

`PUT` request to `/api/v1/protected/schools/:school_id/reports/with-library/:report-id` with body:

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
  "parentSupport": "Parent Support Example",
  "visitReason": "Visit Reason Example",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name",
  "timetable": null
}
```

Responses:

`200 OK`: If update was successful

## Update School Report Without Library

Used to update a new report for a specified school that des not have a library.

`PUT` request to `/api/v1/protected/schools/:school_id/reports/without-library/:report-id` with body:

```json
{
  "numberOfChildren": 20,
  "numberOfBooks": 10,
  "mostRecentShipmentYear": 2019,
  "reasonWhyNot": "Funding",
  "wantsLibrary": true,
  "hasSpace": false,
  "currentStatus": "Found a space",
  "readyTimeline": "YEAR_AFTER_NEXT",
  "visitReason": "A Visit Reason Example",
  "actionPlan": "A short action plan!",
  "successStories": "Some success story here",
  "gradesAttended": [
    "KINDERGARTEN",
    "FIRST_GRADE"
  ],
  "userName": "John Doe",
  "schoolName": "Example School Name",
  "timetable": null
}
```

Responses:

`200 OK`: If update was successful

## Get Report As CSV

Used to return a given report as a CSV

`GET` request to `/api/v1/protected/schools/reports/with-library/:report_id`

OR

`GET` request to `/api/v1/protected/schools/reports/without-library/:report_id` depending on whether the school has a
library or not

Responses (data columns are in random order but the headers match the data):

`200 OK`: returns the report as a CSV formatted as a String (with library)

```
Report ID,Created At,Updated At,School ID,User ID,Number of Children,Number of Books,Most Recent Shipment Year,Library Status,Visit Reason,Is Shared Space,Has Inviting Space,Assigned Person Role,Assigned Person Title,Apprenticeship Program,Trains Mentors and Apprentices,Has Checkin Timetables,Has Book Checkout System,Number of Student Librarians,Has Sufficient Training,Teacher Support,Parent Support
1,Mon Mar 15 00:23:44 EDT 2021,Mon Mar 15 00:23:44 EDT 2021,1,1,1,1,1,EXISTS,Visit Reason Example,true,true,PART_TIME,LIBRARIAN,OTHER,true,true,true,0,true,Teacher Support Example,Parent Support Example
```

`200 OK`: returns the report as a CSV formatted as a String (without library)

```
Report ID,Created At,Updated At,School ID,User ID,Number of Children,Number of Books,Most Recent Shipment Year,Library Status,Visit ReasonWants Library,Has Space,Current Status,Reason,Ready Timeline
1,Tue Mar 23 17:25:04 EDT 2021,Tue Mar 23 17:25:04 EDT 2021,1,1,1,1,1,DOES_NOT_EXIST,hi,true,true,hi,,UPCOMING_SCHOOL_YEAR
```

`404 No Report By Id Found`: Report not found for report with id