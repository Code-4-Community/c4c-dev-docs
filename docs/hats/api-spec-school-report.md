# HATS Report API Specification

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
  "successStories": "Some success story here"
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
  "parentSupport": "Parent Support Example"
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
  "successStories": "Some success story here"
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
  "readyTimeline": "YEAR_AFTER_NEXT"
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
      "parentSupport": "Parent Support Example"
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
      "parentSupport": "Parent Support Example"
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
  "parentSupport": "Parent Support Example"
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
  "readyTimeline": "YEAR_AFTER_NEXT"
}
```

`404 No Report Found`: Report not found for school with id