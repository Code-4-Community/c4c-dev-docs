# SFTT API Specification
This is the official API specification for the SFTT back end. The back end is implemented as is described in the documentation below and should correspond exactly with how the back end behaves. If you find any inconsistencies please create a PR to amend the error.

!!! info "JSON Web Tokens (JWT)"
    In every request header is a JWT. The JWT will contain the user's id value and the user's privilege level.
    It is assumed that the user making the request (as specified in the JWT header) is the person the action is being performed by.

## Enums

### Team Role

The team role enum is all the possible states a use can be in in relation to a team. This is stored in the users_teams table on the backend as the team_role field. The possible options are:

- `NONE` - The user is not on the team. This is possible after a user is rejected from a team or leaves a team.
- `MEMBER` - The is a member of the team.
- `LEADER` - The user is a leader of the team. This grants them special privileges such as being able to accept or reject pending members and being able to disband the team.
- `PENDING` - The user has applied to the team but has not been accepted or rejected yet. If they're accepted they become a member, if they're rejected their status is set to `NONE`

A user is said to be on a team if their role is `MEMBER` or `LEADER`. 

### Privilege Level

The privilege level enum is all the possible privilege levels a user can have. This is stored in the users table as the privilege_level field. The possible options are:

- `STANDARD` - A regular member of the platform and usually a volunteer for Speak for the Trees.
- `ADMIN` - A more senior SFTT volunteer, might be in charge of a neighborhood or someone that works for SFTT. They are able to mark blocks for QA, change normal or admin user privilege levels and other activities that require elevated privileges.
- `SUPER_ADMIN` - A super user with elevated privileges such as being able to import data into the database.

### Reservation Action

The reservation action enum is all the possible action types a reservation can have. This is how blocks are marked as open, complete, reserved, etc. It is stored in the reservation tables as the action_type field. The possible options are:

- `RESERVE` - Marks a block as reserved.
- `COMPLETE` - Marks a block as completed.
- `RELEASE` - Marks a block as released, meaning a user cancelled their reservation and the block is now open again. Anyone can reserve it again now.
- `UNCOMPLETE` - Marks a block as uncomplete, meaning an admin opened the block back up from its previous state. Anyone can reserve it again now.
- `QA` - Indicates a block needs QA. It will remain in this state until an admin either approves or denies the completion. If approved, the block will be marked `COMPLETE`. If denied, the block will be marked `UNCOMPLETE`.


## Reservations Router
This router is used to manage reservations. A reservation is when a user claims a block and thereby commits to going around this block and mapping every tree they see on their side of the block. A block is completed when they have walked around the block, mapped every tree and subsequently confirmed on the app that they did completed those activities. The way our reservation system is set is that it creates a new entry into the reservation table every time an action is performed on a block. These possible actions are:

- `reserve` - Reserve the given block for the given user.
- `complete` - Complete the given block. 
- `release`- Release the given block, meaning to cancel the reservation.
- `uncomplete` - Admin only. Used to invalidate a completed reservation for any reason (not actually completed, want to inventory the block again, etc.)
- `QA` - Admin only. Mark this block for QA, meaning that SFTT wants to go through the trees counted here and make sure everything looks okay.

If the user specifies doesn't specify a team at the time of reservation the user is solely responsible for the block. If they do specify a team, then their team can also complete this block for them. This leads to two options when completing a block.

1. The user who reserved the block completes the block, and can choose which, if any, team to credit it to. Credit goes to the reserving user and the team of their choosing.
2. A teammate completes the block. The teammate cannot specify the team, since it was reserved by another user. Credit goes to the user who completes the block, not the reserving user, and their shared team.

### Make a Reservation

`POST api/v1/protected/reservations/reserve`

Must be called on an open (not marked reserved, completed or QA) block. Will create a reservation for the user making the request for the given block. `team_id` is the team that the user wants to count this block with. This can always be left `NULL`. The purpose of specifying a team at the time of reservation is that it allows other team members to complete this block.

#### Request Body

```json
{
  "block_id": INT,
  "team_id": INT | NULL
}
```

#### Responses

##### `200 OK`
This block was reserved successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Complete a Reservation

`POST api/v1/protected/reservations/complete`

Must be called on a block reserved by the user or by a team they're on. `team_id` is the team that the user wants to credit with this block completion. This can always be left `NULL`.

#### Request Body

```json
{
  "block_id": INT,
  "team_id": INT | NULL
}
```

#### Responses

##### `200 OK`
This reservation was completed successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Release a Reservation

`POST api/v1/protected/reservations/release`

Must be called on a reservation belonging to the user or a team they're the leader of. 

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

Can only by called by admins on completed blocks. Will indicate that this block needs QA.

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

### Pass QA (Admin Only)

`POST api/v1/protected/reservations/pass_qa`

Can only by called by admins on blocks with a QA status. Will duplicate the completion entry from before it was marked for QA and add it as a new entry for the block completion. This preserves the QA as part of the blocks record and returns the block back to its previous state from before it was marked for QA.

#### Request Body

```json
{
  "block_id": INT
}
```

#### Responses

##### `200 OK`
This block has passed QA.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Fail QA (Admin Only)

`POST api/v1/protected/reservations/fail_qa`

Can only by called by admins on blocks with a QA status. Will mark the block as open by using the `UNCOMPLETE` action.

#### Request Body

```json
{
  "block_id": INT
}
```

#### Responses

##### `200 OK`
This block has failed QA.

##### `400 BAD REQUEST`
If the block id specified is invalid.

## Teams Router

This router is used to manage teams. A team consists of a group of users which work together to reach their set goals. Teams can have a leader, members and pending members. Users can also be marked as having `NONE` team role, meaning they have left the team or have been rejected.

### Create a Team

`POST api/v1/protected/teams/create`

Create a team. The team will only contain the member that created it who is now specified as the team leader. It will not have any goals.

#### Request Body

```json
{
  "name": STRING,
  "bio": STRING,
  "inviteEmails": [
    EMAIL,
    ...
  ]
}
```

#### Responses

##### `200 OK`

Returns the same response as the "Get a Team" route for the newly created team.

##### `400 BAD REQUEST`

If the team name is taken or if any of the email addresses is invalid.

### Get a Team

Used to get all the information about a given team. The `team_role` field for each user is one of the values in the team roles enum.

`GET api/v1/protected/teams/:team_id`

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "id": INT,
  "name": STRING,
  "bio": STRING,
  "members": [
    {
      "user_id": INT,
      "username": STRING,
      "team_role": TEAM_ROLE,
    },
    ...
  ],
  "goals": [
    {
      "id": INT,
      "goal": INT,
      "progress": INT,
      "start_date": TIMESTAMP,
      "complete_by": TIMESTAMP,
      "completion_date": TIMESTAMP | NULL
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the team id is invalid.

### Add a Goal

`POST api/v1/protected/teams/:team_id/add_goal`

Team leader only. Adds a goal to this team's list of goals.

#### Request Body

```json
{
  "goal": INT,
  "start_at": TIMESTAMP,
  "complete_by": TIMESTAMP
}
```

##### `400 BAD REQUEST`

If the goal is negative or the `complete_by` date is before the `start_at` date.

##### `401 UNAUTHORIZED`

If the calling user is not team leader.

### Delete a Goal

Team leader only. Deletes a goal from this team's list of goals. Simply removes the record from the table.

`POST api/v1/protected/teams/:team_id/delete_goal/:goal_id`

#### Request Body

No request body.

##### `400 BAD REQUEST`

If the goal id is invalid.

##### `401 UNAUTHORIZED`

If the calling user is not team leader.

### Invite a User

`POST api/v1/protected/teams/:team_id/invite`

Invite someone to join a team. Will send an email to all specified people that includes a link. Link will direct them to the team page where they can join once they are authenticated. If one of the email addresses is invalid or the user is already on the team that invite will not be send out, the other ones will be and a `200 OK` response is returned.

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

Users invited.

##### `400 BAD REQUEST`

If the team id is invalid.

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

If the team id is invalid.

##### `401 UNAUTHORIZED`

If the user is not a team leader

### Apply to a Team

`POST api/v1/protected/teams/:team_id/apply`

Apply to join this team. Any member can apply to join a team that they are not currently on. They will have to be approved by the team leader before becoming an actual member of the team.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Applied successfully.

##### `400 BAD REQUEST`

If the team id is invalid or if the user is already on the team"

### Approve a User

`POST api/v1/protected/teams/:team_id/applicants/:user_id/approve`

Team Leader only. Approve this applicant's request to join the team. The user_id will be the same as the id returned in the GET applicants API call.

#### Request Body

No request body.

#### Responses

##### `200 OK`

This member has joined the team.

##### `400 BAD REQUEST`

If the team or request specified in the id is invalid or the user that had created the request no longer exists.

##### `401 Unauthorized`

If the user is not a team leader.

### Reject a User

`POST api/v1/protected/teams/:team_id/applicants/:user_id/reject`

Team Leader only. Reject this applicant's request to join the team. The user_id will be the same as the id returned in the GET applicants API call. Their team role will be set to `NONE`.

#### Request Body

No request body.

#### Responses

##### `200 OK`

This applicant has been removed from the applicant's list.

##### `400 BAD REQUEST`

If the team or request specified in the id is invalid OR the user that had created the request no longer exists.

##### `401 Unauthorized`

If the user is not a team leader.

### Kick a User

`POST api/v1/protected/teams/:team_id/members/:member_id/kick`

Leader only. Kicks a member off this team. Sets their team role to `NONE`.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Successfully kicked user.

##### `400 BAD REQUEST`

If the user is no longer on the team or the team id is invalid.

##### `401 Unauthorized`

If the user is not a team leader.

### Leave a Team

`POST api/v1/protected/teams/:team_id/leave`

Leave this team that you are a part of. Cannot be called by the leader of the team. Will set team role to `NONE`.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Successfully left team.

##### `400 BAD REQUEST`

If the user is not on the team or the team id is invalid.

### Disband a Team

`POST api/v1/protected/teams/:team_id/disband`

Leader only. Disband this team. The `deleted_at` field will be set to the current timestamp.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Successfully deleted team.

##### `400 BAD REQUEST`

If the team id is invalid.

##### `401 UNAUTHORIZED`

If the user is not the team leader.

### Transfer Team Ownership

`POST api/v1/protected/teams/:team_id/transfer_ownership`

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

##### `401 UNAUTHORIZED`

If the requesting user is not the team leader.

## Auth Router

This router is used to authenticate users. It handles logins, logouts, signups but also things like resetting passwords and secret keys. All of these parts are public, as in users don't need to be authenticated yet to call these routes.

### Login

`POST api/v1/user/login`

Used for logging in.

#### Request Body

```json
{
    "email" : EMAIL,
    "password" : STRING
}
```

An EMAIL is a string representing a user's email.

#### Responses

##### `201 Created`

```json
{
  "accessToken"  : JWT,
  "refreshToken" : JWT
}
```

##### `400 Bad Request`
Malformed request.

##### `401 Unauthorized`
The username/password combination is invalid.

### Refresh Access Token

`POST api/v1/user/login/refresh`

Used for getting a new access token.

#### Request body

```json
{
  "X-Refresh-Token" : JWT
}
```

#### Responses

##### `201 Created`
The refresh token is valid and has not yet expired. Response includes a new unique access_token.

```json
{
  "accessToken" : JWT,
}
```

##### `401 Unauthorized`
The refresh token is invalid.

### Sign Up

`POST api/v1/user/signup`

Used for signing up a new user.

#### Request Body

```json
{
  "username" : STRING,
  "email" : EMAIL,
  "password" : STRING,
  "firstName" : STRING,
  "lastName" : STRING
}
```

- EMAIL: Is a valid email string.
- PASSWORD: Is a valid password string that is at least 8 characters.

#### Responses

##### `201 Created`
The username and email are still available, and an account has been successfully created.

```json
{
  "accessToken"  : JWT,
  "refreshToken" : JWT
}
```
 
##### `400 Bad Request`
Malformed request body.

##### `409 Conflict`
The given email or username is already in use.

```json
"Error creating new user, given email %s already used"
```

```json
"Error creating new user, given username %s already used"
```

### Log Out

`DELETE api/v1/user/login`

Used for logging out.

#### Request Body

```json
  X-Access-Token : JWT
  X-Refresh-Token : JWT
```

#### Responses

##### `204 No Content`
Logout successful.

### Request Password Reset

`POST api/v1/user/forgot_password/request`

Used to send a reset password email to a user if they have forgotten their password

#### Request Body

```json
{
  "email": STRING
}
```

#### Responses

##### `200 OK`

The email was sent to the user successfully

##### `400 BAD REQUEST`

The given email is not associated with any user account

### Reset Password

`POST api/v1/user/forgot_password/reset`

Used to reset a user's password after they have requested a forget password link. The secret key will be contained in the forgot password link. 

#### Request Body

```json
{
  "secretKey": STRING,
  "newPassword": STRING
}
```

Passwords should be strings with length >= 8 characters.

#### Responses

##### `200 OK`

The user's identity was confirmed and the password was changed successfully.

##### `400 BAD REQUEST`

The new password that was given is an invalid password.

##### `401 UNAUTHORIZED`

The given secret key is invalid and does not refer to an account or was created too long ago to be valid.

### Verify Secret Key

`GET api/v1/user/verify/:secret_key`

Used for confirming an account's email.

#### Request Body

None. The secret key parameter is a randomly generated key and sent to the user to verify their email.

#### Responses

##### `200 OK`

The user's secret key has been verified. 

##### `401 Unauthorized`
The secret key is invalid or expired.

### Create Secret Key

!!! missing "This route still needs to be implemented"

`GET api/v1/user/create_secret/:user_id`

Used to create secret keys for users.

#### Request Body

None. The user_id parameter is the id of the user the secret key should be linked to.

#### Responses

##### `200 OK`
A secret key was created and stored for the given user.

##### `400 BAD REQUEST`
The given user id could not be found.

## Users Router

This router is used for routes that affects user accounts. It can only be called when a user is authenticated, as opposed to the auth router which is public.

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

### Delete User

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

### Change Privilege Level (Admin Only)

`POST api/v1/protected/user/change_privilege`

Allows admins to create more admins or demote other admins.

#### Request Body

```json
{
  "targetUserEmail": STRING,
  "newLevel": STRING,
  "password": STRING
}
```

#### Responses

##### `200 OK`
The privilege level change was successful.

##### `400 Bad Request`
The requested user already has the given privilege level.

##### `400 Bad Request`
There is no user associated with the given email.

##### `400 BAD REQUEST`
If the request was malformed.

##### `401 Unauthorized`
The password does not match the calling user's current password.

## Map Router


This router is used to retrieve the data necessary to render blocks, neighborhoods and sites (plantings sites and trees). It makes a call to the backend, which returns the required data from the database in GeoJSON format. The router is public since much of this information is also rendered on the home page.

### Get All Blocks in GeoJSON

`GET api/v1/protected/map/blocks`

Returns all the blocks in GeoJSON format.

#### Request Body

No request body.

#### Responses

#####  `200 OK`

```json
{
  "type": "FeatureCollection",
  "name": "blocks",
  "features: [
    {
      "type": "Feature",
      "properties": {
        "block_id": INT,
        "lat": LONG,
        "lng": LONG,
      },
      "geometry": {
        "type": "MultiPolygon",
        "coordinates": 
        [
          [
            [
              [
                  LONG,
                  LONG
              ],
              ...
            ]
          ]
        ]
      }
    },
    ...
  ]
}
```

### Get All Neighborhoods in GeoJSON

`GET api/v1/protected/map/neighborhoods`

Returns all the neighborhoods in GeoJSON format. The `completion_perc` field in the properties is the percentage of blocks marked as completed compared to the total amount of blocks in that neighborhood.

#### Request Body

No request body.

#### Responses

#####  `200 OK`

```json
{
  "type": "FeatureCollection",
  "name": "neighborhoods",
  "features: [
    {
      "type": "Feature",
      "properties": {
        "neighborhood_id": INT,
        "name": STRING,
        "completion_perc": INT,
        "lat": LONG,
        "lng": LONG
      },
      "geometry": {
        "type": "MultiPolygon",
        "coordinates": [
          coordinates pairs...
        ]
      }
    },
    ...
  ]
}
```

### Get All Sites in GeoJSON

`GET api/v1/protected/map/sites`

Returns all the sites in GeoJSON format.

#### Request Body

No request body.

#### Responses

#####  `200 OK`

```json
{
  "type": "FeatureCollection",
  "name": "sites",
  "features: [
    {
      "type": "Feature",
      "properties": {
        "id": INT,
        "tree_present": BOOLEAN,
        "diameter": DOUBLE,
        "species": STRING,
        "updated_at": TIMESTAMP,
        "updated_by": STRING,
        "address": STRING
      },
      "geometry": {
        "type": "Point",
        "coordinates": [
          LONG,
          LONG
        ]
      }
    },
    ...
  ]
}
```

## Import Router

The import router will be used to fill the database tables that have either static information, such as the blocks and neighborhoods tables, or if already recorded information needs to be carried over. All routes can only be called by super admins. 

The `geometry` field represents the polygon, multi-polygon or other geometric shape that is associated with the the feature. This will consist of the entire `geometry` field found in a `.geojson` file. The JSON will be encoded as a String when sent to the import API. JSON example:
```json
"type": "MultiPolygon",
"coordinates": [
  [
    [
      [
          -71.02859399925286,
          42.34889700150278
      ],
      [
          -71.02912299801895,
          42.34837799993562
      ],
      [
          -71.03016800287584,
          42.348783997754865
      ],
      ...
    ]
  ]
]
```

### Import Blocks

`POST api/v1/protected/import/blocks`

Used to import blocks into the database. Since blocks reference neighborhoods with a foreign key make sure all neighborhoods are imported first.

#### Request Body

```json
{
  "blocks": [
    {
      "block_id": INT,
      "neighborhood_id": INT,
      "lat": LONG,
      "lng": LONG,
      "geometry": STRING,
    },
    ...
  ]
}
```

#### Responses

##### `200 OK`

Blocks imported successfully.

##### `400 BAD REQUEST`

If the request was malformed.

##### `401 UNAUTHORIZED`

If the user is not a super admin.

### Import Neighborhoods

`POST api/v1/protected/import/neighborhoods`

Used to import neighborhoods into the database. Must be called before importing blocks since blocks reference neighborhoods.

#### Request Body

```json
{
  "neighborhoods": [
    {
      "neighborhood_id": INT,
      "neighborhood_name": STRING,
      "sq_miles": DOUBLE,
      "lat": LONG,
      "lng": LONG,
      "geometry": STRING,
    },
    ...
  ]
}
```

#### Responses

##### `200 OK`

Neighborhoods imported successfully.

##### `400 BAD REQUEST`

If the request was malformed.

##### `401 UNAUTHORIZED`

If the user is not a super admin.

### Import Reservations

`POST api/v1/protected/import/reservations`

Used to import reservations into the database. The referenced blocks, users and teams must be in the database. If they're not an error will occur when the "invalid" reservation is reached. `userId` and `teamId` can be left blank, in which case the reservation will be attributed to the super admin calling the route. `performedAt` must be in the form `"mm\/dd\/yyyy"`.

#### Request Body

```json
{
  "reservations": [
    {
      "blockId": INT,
      "userId": INT,
      "teamId": INT,
      "actionType": STRING,
      "performedAt": STRING
    },
    ...
  ]
}
```

#### Responses

##### `200 OK`

Reservations imported successfully.

##### `400 BAD REQUEST`

If the request was malformed.

##### `401 UNAUTHORIZED`

If the user is not a super admin.

### Import Sites

`POST api/v1/protected/import/sites`

#### Request Body

```json
{
  ...
}
```

#### Responses

##### `200 OK`

Sites imported successfully.

##### `400 BAD REQUEST`

If the request was malformed.

##### `401 UNAUTHORIZED`

If the user is not a super admin.

## Leaderboard Router

The leaderboard router is used to get the information displayed on the leaderboard, which is blocks completed per user. It is public so that a future release can include a public leaderboard page. Since none of the information here is strictly private, the consequences of making it public are minimal.

A completed block is a block for which the last entry is either `complete` or `qa`. The user and team that are referenced in that last entry are the user and team that should be credited with the completion. If the user is `NULL`, no user will be credited with that completion, and the same goes for a `NULL` team field. The tie breaker for users or teams with the same number of completed blocks is whatever order the database returns the values in.

### Get Users Leaderboard

`GET api/v1/leaderboard/users`

Returns a list of the top 100 users with counted blocks, of usernames and the blocks those users counted, in order of the number of blocks they counted from most to least. All users with `SUPER_ADMIN` privilege level are excluded. The `previousDays` represent how many days in the past the leaderboard is representing. This is a required value.

#### Request Body

```json
{
  "previousDays": INT
}
```

#### Responses

##### `200 OK`

```json
{
  "users": [
    {
      "id": INT,
      "name": STRING,
      "blocksCounted": INT
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the request was malformed.

### Get Teams Leaderboard

`GET api/v1/leaderboard/teams`

Returns a list of the top 100 team's names and the blocks those teams counted, in order of the number of blocks they counted from most to least. Only teams with blocks counted will be shown. The `previousDays` represent how many days in the past the leaderboard is representing.

#### Request Body

```json
{
  "previousDays": INT
}
```

#### Responses

##### `200 OK`

```json
{
  "teams": [
    {
      "id": INT,
      "name": STRING,
      "blocksCounted": INT
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the request was malformed.

## Sites Router

The sites router is used to handle all the sites and create new ones. A site can be either a planting site without a tree or there can be a tree present. They are differentiated by the field `tree_present` in the `site_entries` table. 

### Add a Site

`POST api/v1/protected/sites/create`

Used to create a new site. Will create two entries in the database. One in the `sites` table to record the permanent information (location, address, block_id) and one in the `site_entries` table to record the state of the site (species, foliage, leaning, trash, etc.). Every field besides `block_id`, `lat`, `lng`, `city`, `zip` and `address` is allowed to be `NULL`.

#### Request Body

```json
{
  "block_id": INT,
  "lat": LONG,
  "lng": LONG,
  "city": STRING,
  "zip": STRING,
  "address": STRING,
  "tree_present": BOOLEAN,
  "status": STRING,
  "genus": STRING,
  "species": STRING,
  "common_name": STRING,
  "confidence": STRING,
  "diameter": DOUBLE,
  "circumference": DOUBLE,
  "coverage": STRING,
  "pruning": STRING,
  "condition": STRING,
  "discoloring": BOOLEAN,
  "leaning": BOOLEAN,
  "grate": BOOLEAN,
  "wounds": BOOLEAN,
  "pooling": BOOLEAN,
  "stakes_with": BOOLEAN,
  "stakes_without": BOOLEAN,
  "light": BOOLEAN,
  "bicycle": BOOLEAN,
  "bag_with": BOOLEAN,
  "bag_without": BOOLEAN,
  "tape": BOOLEAN,
  "sucker_growth": BOOLEAN,
  "site_type": STRING,
  "sidewalk_width": STRING,
  "site_width": STRING,
  "pit_length": STRING,
  "material": STRING,
  "raised_bed":,
  "fence": STRING,
  "trash": STRING,
  "wires": STRING,
  "grate": STRING,
  "stump": STRING,
  "tree_notes": STRING,
  "site_notes": STRING
}
```

#### Responses

##### `200 OK`

Site successfully added.

##### `400 BAD REQUEST`

If the request body is malformed.

### Get a Site

`GET api/v1/protected/sites/:site_id`

Returns all the info about a specific site. This includes all the entries linked to the site, in reverse chronological order (most recent first).

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "site_id": INT,
  "block_id": INT,
  "lat": LONG,
  "lng": LONG,
  "city": STRING,
  "zip": STRING,
  "address": STRING,
  "entries": [
    {
      "id": INT,
      "username": INT,
      "updated_at": TIMESTAMP,
      "tree_present": BOOLEAN,
      "status": STRING,
      "genus": STRING,
      "species": STRING,
      "common_name": STRING,
      "confidence": STRING,
      "diameter": DOUBLE,
      "circumference": DOUBLE,
      "coverage": STRING,
      "pruning": STRING,
      "condition": STRING,
      "discoloring": BOOLEAN,
      "leaning": BOOLEAN,
      "grate": BOOLEAN,
      "wounds": BOOLEAN,
      "pooling": BOOLEAN,
      "stakes_with": BOOLEAN,
      "stakes_without": BOOLEAN,
      "light": BOOLEAN,
      "bicycle": BOOLEAN,
      "bag_with": BOOLEAN,
      "bag_without": BOOLEAN,
      "tape": BOOLEAN,
      "sucker_growth": BOOLEAN,
      "site_type": STRING,
      "sidewalk_width": STRING,
      "site_width": STRING,
      "pit_length": STRING,
      "material": STRING,
      "raised_bed":,
      "fence": STRING,
      "trash": STRING,
      "wires": STRING,
      "grate": STRING,
      "stump": STRING,
      "tree_notes": STRING,
      "site_notes": STRING
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the site id specified is invalid.

### Update a Site

`POST api/v1/protected/sites/update`

Used to update the state of a site. A new entry will be made in the `site_entries` table that will record the latest state of the site and so update the state of that site. Every field can be `NULL`.

#### Request Body

```json
{
  "tree_present": BOOLEAN,
  "status": STRING,
  "genus": STRING,
  "species": STRING,
  "common_name": STRING,
  "confidence": STRING,
  "diameter": DOUBLE,
  "circumference": DOUBLE,
  "coverage": STRING,
  "pruning": STRING,
  "condition": STRING,
  "discoloring": BOOLEAN,
  "leaning": BOOLEAN,
  "grate": BOOLEAN,
  "wounds": BOOLEAN,
  "pooling": BOOLEAN,
  "stakes_with": BOOLEAN,
  "stakes_without": BOOLEAN,
  "light": BOOLEAN,
  "bicycle": BOOLEAN,
  "bag_with": BOOLEAN,
  "bag_without": BOOLEAN,
  "tape": BOOLEAN,
  "sucker_growth": BOOLEAN,
  "site_type": STRING,
  "sidewalk_width": STRING,
  "site_width": STRING,
  "pit_length": STRING,
  "material": STRING,
  "raised_bed":,
  "fence": STRING,
  "trash": STRING,
  "wires": STRING,
  "grate": STRING,
  "stump": STRING,
  "tree_notes": STRING,
  "site_notes": STRING
}
```

#### Responses

##### `200 OK`

Site successfully updated.

##### `400 BAD REQUEST`

If the request body is malformed.

### Delete Site (Admin Only)

`POST api/v1/protected/sites/:site_id/delete`

Used to delete a site. This is done by setting `deleted_at` in the `sites` table to the current timestamp. 

#### Request Body

No request body.

#### Responses

##### `200 OK`

Site successfully deleted.

##### `400 BAD REQUEST`

If the site id specified is invalid.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Mark Site for QA (Admin Only)

`POST api/v1/protected/sites/:site_id/qa`

Used to indicate that a site needs to be checked. This can be done for an individual site or can be part of marking an entire block for QA and then every site associated with that block will be marked for QA. This is done by creating a new entry in the `site_entries` table with `qa` set to `true`.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Site successfully marked for QA.

##### `400 BAD REQUEST`

If the site id specified is invalid.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.
