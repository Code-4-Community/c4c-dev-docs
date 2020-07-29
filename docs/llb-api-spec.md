# LLB API Specification

Any routes with the `protected` path are routes and require a valid JWT token. This token must be placed in the header with the name "access_token".

Any response of `401 UNAUTHORIZED` with the following body indicates that the user's access_token is expired. In this case the client should initiate a new login, or call the `refresh` route with a valid refresh_token JWT to recieve a new access_token.

```json
"Given access_token is expired"
```

# Table Of Contents
- [Getting Events](#getting-events)
  * [`GET api/v1/protected/events/qualified`](#-get-api-v1-protected-events-qualified-)
  * [`GET api/v1/protected/events/signed_up`](#-get-api-v1-protected-events-signed-up-)
  * [`GET api/v1/protected/events?ids=1,2,3,...`](#-get-api-v1-protected-events-ids-1-2-3--)
- [Single Event](#single-event)
  * [`GET api/v1/protected/events/:event_id`](#-get-api-v1-protected-events--event-id-)
  * [`GET api/v1/protected/events/:event_id/registrations`](#-get-api-v1-protected-events--event-id-registrations-)
  * [`POST api/v1/protected/events/`](#-post-api-v1-protected-events--)
  * [`PUT api/v1/protected/events/:event_id`](#-put-api-v1-protected-events--event-id-)
  * [`DELETE api/v1/protected/events/:event_id`](#-delete-api-v1-protected-events--event-id-)
- [Account Creation Flow](#account-creation-flow)
  * [`POST api/v1/protected/requests`](#-post-api-v1-protected-requests-)
  * [`GET api/v1/protected/requests`](#-get-api-v1-protected-requests-)
  * [`POST api/v1/protected/requests/:request_id/approve`](#-post-api-v1-protected-requests--request-id-approve-)
  * [`POST api/v1/protected/requests/:request_id/reject`](#-post-api-v1-protected-requests--request-id-reject-)
  * [`GET api/v1/protected/requests/:request_id`](#-get-api-v1-protected-requests--request-id-)
- [Site Announcements](#site-announcements)
  * [`GET api/v1/protected/announcements`](#-get-api-v1-protected-announcements-)
  * [`GET api/v1/protected/announcements/:event_id`](#-get-api-v1-protected-announcements--event-id-)
  * [`POST api/v1/protected/announcements`](#-post-api-v1-protected-announcements-)
  * [`POST api/v1/protected/announcements/:event_id`](#-post-api-v1-protected-announcements--event-id-)
  * [`DELETE /api/v1/protected/announcements/:announcement_id`](#-delete--api-v1-protected-announcements--announcement-id-)
- [Event Checkout and Registration](#checkout-registration)
  * [`POST api/v1/protected/checkout/register`](#-post-api-v1-protected-checkout-register)
  * [`PUT api/v1/protected/checkout/register/:event_id`](#-put-api-v1-protected-checkout-register--event-id-)
  * [`POST api/v1/webhooks/stripe`](#-post-api-v1-webhooks-stripe)



# Getting Events

## `GET api/v1/protected/events/qualified`

Get a list of events that the calling user is qualified for. This includes events a user is already signed up for.

For General Public: Any event happening in the next 5 days are eligible.
For Participating Families: Any event happening in the future.
For Admins: Any  event happening in the future.

### Query Params

##### start: DATE-STRING

The beginning date of when to get events from. All returned events will happen ON or after
the given date string that is given in mm/dd/yyyy format, ie: 01/19/2020

##### end: DATE-STRING

The latest date of when to get events from. All returned events will happen ON or before the
given date string that is given in mm/dd/yyyy format, ie: 12/03/2009

##### count: INTEGER

The maximum number of events to return. The route will return AT MOST count number of events.

### Responses

#### `200 OK`

The events were sent successfully.

```json
{
  "events": [
    {
      "id": ID,
      "title": STRING,
      "spotsAvailable": INT,
      "capacity": INT,
      "thumbnail": URL,
      "ticketCount": INT,
      "canRegister": BOOLEAN,
      "details": {
        "description": STRING,
        "location": STRING,
        "start": TIMESTAMP,
        "end": TIMESTAMP
      }
    },
    ...
  ],
  "totalCount": INTEGER
}
```

ticketCount is the number of tickets the calling user has reserved for this event. It will be 0 if the user is not registered for the event.

canRegister is dependent on the privilege level of the calling user. GPs are only able to register for events that are happening in the next 5 days.



## `GET api/v1/protected/events/signed_up`

Get all of the events happening in the future that the given user is signed up for.


### Query Params

##### start: DATE-STRING

The beginning date of when to get events from. All returned events will happen ON or after
the given date string that is given in mm/dd/yyyy format, ie: 01/19/2020

##### end: DATE-STRING

The latest date of when to get events from. All returned events will happen ON or before the
given date string that is given in mm/dd/yyyy format, ie: 12/03/2009

##### count: INTEGER

The maximum number of events to return. The route will return AT MOST count number of events.

### Responses

#### `200 OK`

The events were sent successfully.

```json
{
  "events": [
    {
      "id": ID,
      "title": STRING,
      "spotsAvailable": INT,
      "capacity": INT,
      "thumbnail": URL,
      "ticketCount": INT,
      "details": {
        "description": STRING,
        "location": STRING,
        "start": TIMESTAMP,
        "end": TIMESTAMP
      }
    },
    ...
  ],
  "totalCount": INTEGER
}
```

ticketCount is the number of tickets the calling user has reserved for this event. It will be 0 if the user is not registered for the event.


## `GET api/v1/protected/events?ids=1,2,3,...`

Get the event bodies for the events with the ids that are specified in the query parameter.

### Query Params

##### ids: INT-LIST

The event ids that are being requested. Ids are numbers and they should be seperated by a single comma. If this query parameter is missing, this route returns an empty list. Ignore duplicated values. Ignore ids that do not correspond to any existing event.

### Responses

Equivelent to above routes:

```json
{
  "events": [
    {
      "id": ID,
      "title": STRING,
      "spotsAvailable": INT,
      "capacity": INT,
      "thumbnail": URL,
      "ticketCount": INT,
      "details": {
        "description": STRING,
        "location": STRING,
        "start": TIMESTAMP,
        "end": TIMESTAMP
      }
    },
    ...
  ],
  "totalCount": INTEGER
}
```


# Single Event

## `GET api/v1/protected/events/:event_id`

Gets a specific event with the specified id.

### Route Params

##### event_id: ID

The id number of the event that is being queried.

### Responses

#### `200 OK`

The event was retrieved successfully.

```json
{
  "id": ID,
  "title": STRING,
  "spotsAvailable": INT,
  "capacity": INT,
  "thumbnail": URL,
  "ticketCount": INT,
  "canRegister": BOOLEAN,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```

ticketCount is the number of tickets the calling user has reserved for this event. It will be 0 if the user is not registered for the event.

canRegister is dependent on the privilege level of the calling user. GPs are only able to register for events that are happening in the next 5 days.

canRegister does not check if the calling user is already signed up for this event.


## `GET api/v1/protected/events/:event_id/registrations`

Gets a list of users registered for the event with the given id.

### Route Params

##### event_id: ID

The id number of the event that is being queried.

### Responses

#### `200 OK`

The event was retrieved successfully.

```json
{
  "registrations": [
    {
      "firstName": STRING,
      "lastName": STRING
      "email": STRING,
      "ticketCount": INT
    },
    ...
  ]
}
```

#### `400 BAD REQUEST`

```json
There does not exist an event with id: <id>
```

If the provided event id does not exist

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.

## `POST api/v1/protected/events/`

Create a new event. This route must be called by an admin.

### Request Body

```json
{
  "title": STRING,
  "spotsAvailable": INT,
  "capacity": INT,
  "thumbnail": URL,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```

There is an open question on how thumbnails should be handled.

### Responses

#### `200 OK`

The event was created successfully. Will return the event that was just created (including the generated id).

```json
{
  "id": ID,
  "title": STRING,
  "spotsAvailable": INT,
  "capacity": INT,
  "thumbnail": URL,
  "ticketCount": INT,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.

## `PUT api/v1/protected/events/:event_id`

Update an existing event. This route must be called by an admin. All fields are optional.

### Request Body

```json
{
  "title": STRING,
  "spotsAvailable": INT,
  "thumbnail": URL,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```

### Responses

#### `200 OK`

The event was updated successfully. Will return the event that was just created (including the generated id).

```json
{
  "id": ID,
  "title": STRING,
  "spotsAvailable": INT,
  "thumbnail": URL,
  "ticketCount": INT,
  "details": {
    "description": STRING,
    "location": STRING,
    "start": TIMESTAMP,
    "end": TIMESTAMP
  }
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.

#### `409 Conflict`

This response occurs when `spotsAvailable` is less than the number of users currently registered for the event.

## `DELETE api/v1/protected/events/:event_id`

Delete an existing event. This route must be called by an admin.

### Responses

#### `200 OK`

The event was deleted successfully. Returns the ID of the deleted event.

```json
{
  "id": ID
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.



# Account Creation Flow

## `POST api/v1/protected/requests`

Making a request to be upgraded from GP to PF.
Must be made by a GP user.
Potentially adding bottle nose to prevent multiple denied requests.
Allow multiple outstanding requests?

### Responses

#### `200 OK`

#### `429 TOO MANY REQUESTS`

## `GET api/v1/protected/requests`

Getting all active requests.

Must be called by an admin.

### Responses

#### `200 OK`
```json
{
  "requests": [
    {
      "id": "request_id STRING",
      "user": {
        "id": STRING,
        "firstName": STRING,
        "lastName": STRING,
        "email": STRING,
        "phoneNumber": STRING
      }
    },
    ...
  ]
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.

## `GET /api/v1/protected/requests/:request_id`

> Gets all the information associated with the account that made this request.

### Params

- `request_id` is the id number of this request.

### __Response__

#### `200 OK`

```json
{
  "mainContact": {
    "id": STRING,
    "firstName": STRING,
    "lastName": STRING,
    "dateOfBirth": TIMESTAMP,
    "phoneNumber": STRING,
    "pronouns": STRING,
    "allergies": STRING OR NULL,
    "diagnosis": STRING OR NULL,
    "medication": STRING OR NULL,
    "notes": STRING OR NULL,
  },
  "additionalContacts": [
    {
        "id": STRING,
        "firstName": STRING,
        "lastName": STRING,
        "email": EMAIL,
        "shouldSendEmails": BOOLEAN,
        "dateOfBirth": TIMESTAMP,
        "phoneNumber": STRING,
        "pronouns": STRING,
        "allergies": STRING OR NULL,
        "diagnosis": STRING OR NULL,
        "medication": STRING OR NULL,
        "notes": STRING OR NULL,
    },
    ...
  ],
  "children": [
    {
      "id": STRING,
      "firstName": STRING,
      "lastName": STRING,
      "dateOfBirth": TIMESTAMP,
      "pronouns": STRING,
      "school": STRING,
      "schoolYear": STRING,
      "allergies": STRING OR NULL,
      "diagnosis": STRING OR NULL,
      "medications": STRING OR NULL,
      "notes": STRING OR NULL,
    }
    ...
  ],
  "location": {
    "address": STRING,
    "city": STRING,
    "state": STRING,
    "zipCode": STRING
  },
  "accountType": PRIVILEGE_LEVEL
}
```

`PRIVILEGE_LEVEL` is one of "GP", "PF", or "ADMIN".

> Note that this is included so that this response matches the response for getting all of a user's information even though it will always be GP in this case.



## `POST api/v1/protected/requests/:request_id/approve`
## `POST api/v1/protected/requests/:request_id/reject`

Approving or rejecting a request by a user to become a PF.
Must be called by an admin.

### Path Params

#### `requst_id`

The id of the request that is being modified.

### Responses
#### `200 OK`

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.


## `GET api/v1/protected/requests/status`

Getting the statuses of all requests the calling user has made to become a PF.

### Responses

#### `200 OK`
```json
{
  "requests": [
   {
    "id": INT,
    "status": "APPROVED" | "REJECTED" | "PENDING",
    "created": TIMESTAMP
   },
   ...
  ]
}
```






# Site Announcements

**NOTE: Site-wide and event specific announcements have uniquely identifying ID numbers.**

## `GET api/v1/protected/announcements`

Gets a list of site-wide announcements. The announcements will be returned in chronological order, from newest to oldest.

### Query Params

##### start: DATE-STRING

The beginning date of when to get announcements from. All returned announcements are created ON or after the given date string that is given in mm/dd/yyyy format, e.g. 01/19/2020. Defaults to 3 weeks from `end` or the current date.

##### end: DATE-STRING

The end date of when to get announcements from. All returned announcements will happen ON or before the given date string that is given in mm/dd/yyyy format, e.g. 12/03/2009. Defaults to the current date.

##### count: INTEGER

The maximum number of announcements to return. The route will return AT MOST count number of announcements. Defaults to 50 or all events that exist.

### Responses

#### `200 OK`

The announcements were retrieved successfully.

```json
{
  "announcements": [
    {
      "id": ID,
      "title": STRING,
      "description": STRING,
      "created": TIMESTAMP
    },
    ...
  ],
  "totalCount": INTEGER
}
```


## `GET api/v1/protected/announcements/:event_id`

Get any announcements that are specific to a particular event. Events can have multiple announcements associated with them. Announcements should be returned in order of how recent they were created.

### Path Params

#### `event_id`

The ID of the event whose announcements are being queried

### Responses

#### `200 OK`

The announcements were retrieved successfully.

```json
{
  "announcements": [
    {
      "id": ID,
      "title": STRING,
      "description": STRING,
      "created": TIMESTAMP,
      "event_id": ID
    },
    ...
  ],
  "totalCount": INTEGER
}
```


## `POST api/v1/protected/announcements`

Creates a new site-wide announcement. Can only be accessed by admins.

### Request

Body:

```json
{
    "announcement": {
        "title": STRING,
        "description": STRING
    }
}
```

### Responses

#### `200 OK`

The announcement was created successfully.

```json
{
  "id": ID,
  "title": STRING,
  "description": STRING,
  "created": TIMESTAMP
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.

## `POST api/v1/protected/announcements/:event_id`

Creates a new announcement for the given event. Can only be accessed by admins.

### Request

Body:

```json
{
    "title": STRING,
    "description": STRING
}
```

### Responses

#### `200 OK`

The announcement was created successfully.

```json
{
  "id": ID,
  "title": STRING,
  "description": STRING,
  "created": TIMESTAMP,
  "event_id": ID
}
```

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.


## `DELETE /api/v1/protected/announcements/:announcement_id`

Deletes an announcment with the specified announcment id. This route can be used to delete site-wide or event specific because they have unique announcement identifiers. This route can only be called by an admin.

### Path Params

#### `announcment_id`

The ID of the announcement being deleted.

### Requests

#### `200 OK`

The announcement was deleted successfully.

#### `401 Unauthorized`

```json
The calling user does not have the required privilege level
```

If the calling user is not an admin.





# Event Checkout and Registration

The events in a user's cart are stored in Vuex on the frontend in the cart module.

## `POST api/v1/protected/checkout/register`

Accepts a non-empty list of events to register the user for and creates records in the USER_EVENTS table. If called by an ADMIN or a PF user, it skips anything
to do with Stripe and payments. If called by a GP, the EVENT_REGISTRATIONS records are initially persisted as "invalid" with the CheckoutSessionID of the Stripe endpoint we create. When the checkout is successfully completed, the EVENT_REGISTRATIONS records have their statuses updated to "active".

How does Stripe work?
When a GP clicks register, we call this route, which will create a Stripe
Checkout Session. The ID is returned to the frontend by this API.
The ID is used to redirect the user to a page in Stripe's application,
where they can pay. Upon a valid payment completion, Stripe triggers
our webhook, and when it receives a 200 code Stripe will redirect
to the success URL.

### Request Body

```json
{
    "lineItems": [
        {
            "eventId": INT,
            "quantity": INT
        },
        ...
    ]
}
```

### Responses

#### `200 OK`

The purchase was successful.

#### `202 ACCEPTED`

Checkout session created, response contains the ID.

#### `400 BAD REQUEST`

The request syntax is malformed, e.g. the given list of events is empty.

#### `401 UNAUTHORIZED`

The user is not eligible to sign up for one or more of the requested events.

#### `404 NOT FOUND`

One or more of the event IDs is invalid.

#### `409 CONFLICT`

The user has requested a number of tickets that exceeds the capacity for one or more of the requested events.



## `PUT api/v1/protected/checkout/register/:event_id`

Changes the number of tickets a user has for the specified event. If the user is a GP and the new number of tickets is greater than the number of tickets the user previously had, a Stripe checkout session will be created. The new number of tickets must be within the event's capacity and must be greater than or equal to 0. If a GP user decreases the number of tickets, the price of the removed tickets will be added to their account balance.

### Request Body

```json
{
    "quantity": INT
}
```

### Responses

#### `200 OK`

The change in tickets was successful.

#### `202 ACCEPTED`

Checkout session created, response contains the ID.

#### `401 UNAUTHORIZED`

The user is not eligible to sign up for the event.

#### `404 NOT FOUND`

The event ID is invalid.

#### `409 CONFLICT`

The user has requested a number of tickets that exceeds the capacity for the event.



## `POST api/v1/webhooks/stripe`

A webhook for Stripe to send events to.

See Step 4 of these docs:
https://stripe.com/docs/payments/checkout/one-time

Stripe Webhook Documentation:
https://stripe.com/docs/webhooks

### Request Body

The body we care about is a Session object as
defined by the Stripe Java library. We use only
the Checkout Session ID, marking USER_EVENTS
records that are invalid with the same Checkout
Session ID as valid.

### Responses

#### `200 OK`

Generally, a lot of problems are caused when we don't return a 200 code.
It's the equivalent of Stripe saying "OK, we authorized this payment!"
and us saying "Wait, something's wrong, don't charge them".
Idk what the actual implementation details are, but generally,
Stripe will send more types of events to our hook than
just the checkout.session.completed event that we care about.
For other events, we just return 200.

#### `400 BAD REQUEST`

This shouldn't happen...

#### `401 UNAUTHORIZED`

We're using webhook signatures to authorize this endpoint.
