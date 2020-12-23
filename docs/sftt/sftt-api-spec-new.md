# SFTT API Specification
This is the official API specification for the SFTT back end. The back end is implemented as is described in the documentation below and should correspond exactly with how the back end behaves. If you find any inconsistencies please create a PR to amend the error.

!!! info "JSON Web Tokens (JWT)"
    In every request header is a JWT. The JWT will contain the user's id value and the user's privilege level.
    It is assumed that the user making the request (as specified in the JWT header) is the person the action is being performed by.

## Reservations Router
This router is used to manage reservations. A reservation is when a user claims a block and thereby commits to going around this block and mapping every tree they see on their side of the block. A block is completed when they have walked around the block, mapped every tree and subsequently confirmed on the app that they did completed those activities. The way our reservation system is set is that it creates a new entry into the reservation table everytime an action is performed on a block. These possible actions are:

- `reserve` - Reserve the given block for the given user.
- `complete` - Complete the given block. 
- `release`- Release the given block, meaning to cancel the reservation.
- `uncomplete` - Admin only. Used to invalidate a completed reservation for any reason (not actually completed, want to inventory the block again, etc.)
- `QA` - Admin only. Mark this block for QA, meaning that SFTT wants to go through the trees counted here and make sure everything looks okay.

### Make a Reservation

`POST api/v1/protected/reservations/reserve`

Must be called on an open (not marked reserved, completed or QA) block. Will create a reservation for the user making the request for the given block. `team_id` is the team that the user wants to count this block with. This can always be left `NULL`.

#### Request Body

```json
{
  "block_id": INT,
  "team_id": INT
}
```

#### Responses

##### `200 OK`
This block was reserved successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Complete a Reservation

`POST api/v1/protected/reservations/complete`

Must be called on a block reserved by the user. `team_id` is the team that the user wants to credit with this block completion. This can always be left `NULL`.

#### Request Body

```json
{
  "block_id": INT,
  "team_id": INT
}
```

#### Responses

##### `200 OK`
This reservation was completed successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Release a Reservation

`POST api/v1/protected/reservations/release`

Must be called on a reservation belonging to the user. 

#### Request Body

```json
{
  "block_id": INT
}
```

#### Responses

##### `200 OK`
This reservation was cancelled successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Uncomplete a Reservation (Admin Only)

`POST api/v1/protected/reservations/uncomplete`

Can only by called by admins. Will invalidate the last completion for the given block and opens the block back up for inventory.

#### Request Body

```json
{
  "block_id": INT
}
```

#### Responses

##### `200 OK`
This reservation was marked as incomplete successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Mark for QA (Admin Only)

`POST api/v1/protected/reservations/qa`

Can only by called by admins. Will indicate that this block needs QA. 

#### Request Body

```json
{
  "block_id": INT
}
```

#### Responses

##### `200 OK`
This block has been selected for QA.

##### `400 BAD REQUEST`
If the block id specified is invalid.

## Teams Router

This router is used to manage teams. A team consists of a group of users which work together to reach their set goals. Below is a simple overview of how each role is denoted in the back end.

| Role Name      | teamRole |
|----------------|----------|
| NONE           | 0        |
| MEMBER         | 1        |
| LEADER         | 2        |
| PENDING        | 3        |

### Create a Team

`POST api/v1/protected/teams`

Create a team. The team will only contain the member that created it who is now specified as the team leader. It will not have any goals.

#### Request Body

```json
{
  "name": STRING,
  "bio": STRING,
  "inviteEmails": [
    STRING,
    ...
  ]
}
```

#### Responses

##### `200 OK`

!!! missing "Should return the same as the get team route"

##### `400 BAD REQUEST`

!!! missing "Should be an error here if the team name is already taken"

### Get a Team

!!! missing "This route needs to be reimplemented due to the change in goals"

`GET /teams/:team_id`

#### Request Body

No request body.

#### Responses

##### `200 OK`

!!! missing "JSON body"

### Add a Goal

`POST api/v1/protected/teams/:team_id/add_goal`

Adds a goal to this team's list of goals.

!!! missing "This route has not been implemented yet"

### Delete a Goal

Deletes a goal from this team's list of goals. Simply removes the record from the table.

`POST api/v1/protected/teams/:team_id/delete_goal`

!!! missing "This route has not been implemented yet"

### Invite a User

`POST api/v1/protected/teams/:team_id/invite`

Invite someone to join a team. Will send an email to all specified people that includes a link. Link will direct them to the team page where they can join once they are authenticated.

#### Request Body

```json
{
  "invites": [
    {
      "name": STRING,
      "email" STRING
    },
    ...
  ]
}
```

#### Responses

##### `200 OK`

!!! missing "Unknown"

##### `400 BAD REQUEST`

!!! missing "Should be an error here if the team id is invalid or if the user is already on the team"

### Get Applicants

`GET api/v1/protected/teams/:team_id/applicants`

Team Leader only. Get the info for anyone that has requested to join this team.

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "applicants": [
    {
      "userId": INT,
      "username": STRING,
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

!!! missing "Should be an error here if the team id is invalid or if the user is not a team leader"

### Apply to a Team

`POST api/v1/protected/teams/:team_id/apply`

Apply to join this team. Any member can apply to join a team that they are not currently on. They will have to be approved by the team leader before becoming an actual member of the team.

#### Request Body

No request body.

#### Responses

##### `200 OK`

!!! missing "Unknown"

##### `400 BAD REQUEST`

!!! missing "Should be an error here if the team id is invalid or if the user is already on the team"

### Approve a User

`POST api/v1/protected/teams/:team_id/applicants/:user_id/approve`

Team Leader only. Approve this applicant's request to join the team. The user_id will be the same as the id returned in the GET applicants API call.

#### Request Body

No request body.

#### Responses

##### `200 OK`

This member has joined the team.

##### `400 BAD REQUEST`

If the team or request specified in the id is invalid OR the user that had created the request no longer exists.

##### `401 Unauthorized`

!!! missing "Should be an error here if the user is not a team leader"

### Reject a User

`POST /teams/:team_id/applicants/:user_id/reject`

Team Leader only. Reject this applicant's request to join the team. The user_id will be the same as the id returned in the GET applicants API call.

#### Request Body

No request body.

#### Responses

##### `200 OK`

This applicant has been removed from the applicant's list.

##### `400 BAD REQUEST`

If the team or request specified in the id is invalid OR the user that had created the request no longer exists.

##### `401 Unauthorized`

!!! missing "Should be an error here if the user is not a team leader"

### Kick a User

`POST api/v1/protected/teams/:team_id/members/:member_id/kick`

Leader only. Kicks a member off this team. That member is then allowed to join or create any team now.

#### Request Body

No request body.

#### Responses

##### `200 OK`

!!! missing "Unknown"

##### `400 BAD REQUEST`

!!! missing "Should be an error here if the user is no longer on the team or the team id is invalid"

##### `401 Unauthorized`

!!! missing "Should be an error here if the user is not a team leader"

### Leave a Team

`POST api/v1/protected/teams/:team_id/leave`

Leave this team that you are a part of. Cannot be called by the leader of the team.

#### Request Body

No request body.

#### Responses

##### `200 OK`

!!! missing "Unknown"

##### `400 BAD REQUEST`

!!! missing "Should be an error here if the user is no longer on the team or the team id is invalid"

### Disband a Team

`POST api/v1/protected/teams/:team_id/disband`

Leader only. Disband this team. The team will be deleted from the database. 

#### Request Body

No request body.

#### Responses

##### `200 OK`

!!! missing "Unknown"

##### `400 BAD REQUEST`

!!! missing "Should be an error here if the team id is invalid"

##### `401 Unauthorized`

!!! missing "Should be an error here if the user is not the team leader"

### Transfer Team Ownership

`POST /teams/:team_id/transfer_ownership`

Leader only. Makes the current team leader a regular team member and the specified user the team leader.

#### Request Body

```json
{
    "newLeaderId": INT
}
```

#### Responses

##### `200 OK`

Success.

##### `400 BAD REQUEST`

If the given user ID does not exist, or if the given user is not on the team.

##### `401 Unauthorized`

If the requesting user is not the team leader.

## Users Router

### Login

`POST api/v1/user/login`

Used for logging in.

#### Request Body

#### Responses

### Sign Up

!!! missing "This route needs still needs documentation"

#### Request Body

#### Responses

### Log Out

!!! missing "This route needs still needs documentation"

#### Request Body

#### Responses

### Delete User

!!! missing "This route has not been implemented yet"

`POST api/v1/protected/user/delete`

Sets this users deleted_at timestamp to now, thereby marking this account as deleted in the database.

#### Request Body

```json
{
  "password": STRING
}
```

#### Responses

##### `200 OK`

Successfully deleted this user.

##### `400 BAD REQUEST`

If the given user ID does not exist.

##### `401 Unauthorized`

If the password is wrong.

### Change Password

`POST api/v1/protected/user/change_password`

Allows a user to change their password when already authenticated. Passwords should be strings with a length of at least 8 characters.

#### Request Body

```json
{
  "currentPassword": STRING,
  "newPassword": STRING
}
```

#### Responses

##### `200 OK`

The password change was successful.

##### `400 BAD REQUEST`

If the request was malformed.

##### `401 Unauthorized`

The currentPassword does not match the calling user's current password.

### Change Username

`POST api/v1/protected/user/change_username`

Allows a user to change their username. The new username must not be in use already.

#### Request Body

```json
{
  "newUsername": STRING,
  "password": STRING
}
```

#### Responses

##### `200 OK`
The username change was successful.

##### `400 BAD REQUEST`
If the request was malformed.

##### `401 Unauthorized`
The password does not match the calling user's current password.

##### `409 Conflict`
The given `newUsername` is already in use.

### Request Password Reset

!!! missing "This route needs still needs documentation"

### Reset Password

!!! missing "This route needs still needs documentation"


## Blocks Router

!!! missing "This router has not been implemented yet"

### Get All Blocks

### Get All Reserved Blocks

### Get All Completed Blocks

### Get All Blocks Per Neighborhood

## Map Router

!!! missing "This router has not been implemented yet"

### Get All Blocks in GeoJSON

### Get All Neighborhoods in GeoJSON

### Get All Sites in GeoJSON

## Leaderboard Router

!!! missing "This router has not been implemented yet"

### Get All Teams

### Get All Users