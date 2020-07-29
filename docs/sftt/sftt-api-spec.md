# SFTT API Specification

In every request header is a JWT. The JWT will contain the user's id value and the user's privilege level.

## Privilege Levels
- `0` = untrusted user
- `1` = a regular user
- `2` = an admin user

It is assumed that the user making the request (as specified in the JWT header) is the person the block is being reserved / completed by.

# Block Status Updates

## `POST api/v1/protected/blocks/reserve`

Open -> Reserved

Must be called on open blocks.

1. Update DB, add blocks to a user's assigned blocks
2. Call map API and update status of blocks to reserved


## `POST api/v1/protected/blocks/finish`

Reserved -> Done

Whoever had the block in it's current reserve is the one who is credited with completion.

Must be called by an admin or by the person holding the block.

## `POST api/v1/protected/blocks/release`

Reserved -> Open

Must be called by an admin or by the person holding the block.


## `POST api/v1/protected/blocks/reset`

Done -> Open

Admin Only

### reserve, finish, release, and reset share the same request bodies and possible responses.

### Request Body
```json
{
  "blocks": [
    "block_id STRING",
    ...
  ]
}
```

The block id will correspond to the fid of a block stored in the ArcGIS map.

### Responses
Partial successes are possible.

Reasons a block may fail:
- The block was not in the state that it should've been
- The user is not allowed to modify that block's state
- The fid doesn't match any block

##### `200 OK`
```json
{
  "successes": [
    "block_id STRING",
    ...
  ],
  "failures": [
    "block_id STRING",
    ...
  ]
}
```

##### `401 Unauthorized`
The calling user was not an admin (reset only)

## `POST api/v1/protected/blocks/reserve/admin`

Open -> Reserved

Admin Only

Can specify a specific user that a block is being assigned to.

### Request Body
```json
{
  "assigned_to": "user_id STRING",
  "blocks": [
    "block_id STRING",
    ...
  ]
}
```

### Responses

##### `200 OK`
```json
{
  "successes": [
    "block_id STRING",
    ...
  ],
  "failures": [
    "block_id STRING",
    ...
  ]
}
```

##### `400 BAD REQUEST`
The body was malformed or the specified user doesn't exist.

##### `401 Unauthorized`
The calling user was not an admin.





# Team Management

Members of a team have roles specified in the following table:

| Role Name      | teamRole |
|----------------|----------|
| NONE           | 0        |
| MEMBER         | 1        |
| LEADER         | 2        |
| PENDING        | 3        |


## `POST /teams`

Create a team. The team will only contain the member that created it who is now specified as the team leader.

### Request

```json
{
  "name": STRING,
  "bio": STRING,
  "goal": INT
  "goalCompletionDate": DATE,
  "inviteEmails": [
    STRING,
    ...
  ]
}
```

Goal is a number of blocks the team would like to complete by the goalCompleteDate. inviteEmails are a list of emails that that should be sent an invitation email to join the team. The team leader's email should not be included. This acts functionally the same as the `/teams/:team_id/invite` route.

### Response

##### `200 OK`

```json
{
  "id": INT,
  "name": STRING,
  "bio": STRING,
  "goal" INT,
  "goalCompleteDate": DATE,
  "blocksCompleted": INT,
  "blocksReserved": INT,
  "applicantsToReview": BOOLEAN,
  "members": [
    {
      "id": INT,
      "username": STRING,
      "blocksCompleted": INT,
      "blocksReserved": INT,
      "teamRole": INT
    },
    ...
  ],
}
```

This is the same as the JSON returned for `GET /teams/:team_id`


## `POST /teams/:team_id/invite`

Invite someone to join a team. Will send an email to all specified people that includes a link. Link will direct them to the team page where they can join once they are authenticated.

### Request

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

## `POST /teams/:team_id/apply`

Apply to join this team. Any member can apply to join a team that they are not currently on. They will have to be approved by the team leader before becoming an actual member of the team.

### Request

No request body.

## `GET /teams/:team_id/applicants`

Team Leader only.

Get the info for anyone that has requested to join this team in a list.

### Responses

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

## `POST /teams/:team_id/applicants/:user_id/approve`

Team Leader only.

Approve this applicant's request to join the team. The user_id will be the same as the id returned in the GET applicants API call.

### Request

No Request body

### Responses

##### `200 OK`

This member has joined the team.

##### `400 BAD REQUEST`

If the team or request specified in the id is invalid OR the user that had created the request no longer exists.



## `POST /teams/:team_id/applicants/:user_id/reject`

Team Leader only.

Reject this applicant's request to join the team. The user_id will be the same as the id returned in the GET applicants API call.

### Request

No Request body

### Responses

##### `200 OK`

This applicant has been removed from the applicant's list.

##### `400 BAD REQUEST`

If the team or request specified in the id is invalid OR the user that had created the request no longer exists.





## `POST /teams/:team_id/leave`

Leave this team that you are a part of. Cannot be called by the leader of the team.

### Request

No request body.

## `POST /teams/:team_id/disband`

Disband this team. Leader only. The team will be removed from the public team list and all other members will now be free to join or create a different team.

### Request

No request body.

## `POST /teams/:team_id/members/:member_id/kick`

Kicks a member off this team. Leader only. That member is then allowed to join or create any team now.

### Request

No request body.

## `POST /teams/:team_id/transfer_ownership`

Update the team leader. Can only be called by the current leader of the given team.

### Request

```json
{
    "newLeaderId": INT
}
```

### Responses

##### `200 OK`

Success.

##### `400 Bad Request`

If the given user ID does not exist, or if the given user is not on the team.

##### `401 Unauthorized`

If the requesting user is not the team leader.


# Team and Leaderboard Data

## `GET /teams`

Gets a list of teams, names, member count.

### Responses

##### `200 OK`

```json
{
  "teams": [
    {
      "id": INT,
      "name": STRING,
      "memberCount": INT,
      "userTeamRole": TEAM_ROLE
    },
    ...
  ],
  "rowCount": INT
}
```

## `GET /teams/admin`

Gets a list of teams with their goal data.

### Responses

##### `200 OK`

```json
{
  "teams": [
    {
      "id": INT,
      "name": STRING,
      "goalCompletionDate": TIMESTAMP,
      "blocksCompleted": INT,
      "blocksReserved": INT,
      "goal": INT
    },
    ...
  ],
  "rowCount": INT
}
```

## `GET /teams/:team_id`

Gets the information for this specific team. Including the members with how many blocks each one has completed and reserved.

### Responses

##### `200 OK`

```json
{
  "id": INT,
  "name": STRING,
  "bio": STRING,
  "goal" INT,
  "goalCompleteDate": DATE,
  "blocksCompleted": INT,
  "blocksReserved": INT,
  "userTeamRole": TEAM_ROLE,
  "applicantsToReview": BOOLEAN,
  "members": [
    {
      "id": INT,
      "username": STRING,
      "blocksCompleted": INT,
      "blocksReserved": INT,
      "teamRole": INT
    },
    ...
  ],
}
```

The `members` list is sorted in descending order of number of blocks completed.

`teamRole` is an indicator of the member's role on the team. Currently there are only two roles: general member and team leader.

`applicantsToReview` is a flag that there are people that have applied to the team that the team leader has to review. This will always be false if a non-leader calls this route.


## `GET /api/v1/protected/teams/export`

Export Teams and User information. This is an admin-only route.
Returns a `text/cvs` response that includes
every team name, the goal and goal completion date of the team,
the time the team was created, every member of each team,
the team role of each member, the number of blocks
reserved and completed by each user on a team.

### Responses

##### `200 OK`

```text
STRING,STRING,STRING,...
STRING,STRING,STRING,...
...
```


## `GET /blocks`

Gets blocks done, in progress, todo for all of Boston

### Responses

##### `200 OK`

```json
{
  "blocksCompleted": INT,
  "blocksReserved": INT,
  "blocksOpen": INT
}
```

## `GET /blocks/leaderboard`

Gets blocks completed leaderboard for both teams and individuals. Will include up-to the top 10 teams and the top 10 individuals

### Responses

##### `200 OK`

```json
{
  "teams": [
    {
      "id": INT,
      "name": STRING,
      "blocksCompleted": INT,
      "blocksReserved": INT
    },
    ...
  ],
  "individuals": [
    {
      "id": INT,
      "username": STRING,
      "blocksCompleted": INT,
      "blocksReserved": INT
    },
    ...
  ]
}
```

## `GET api/v1/protected/blocks/reserved`

Gets all blocks that are `assigned_to` the current user that have a status of
`RESERVED`. Including the optional query parameter `done=true` will _also_
return all blocks that have a status of `DONE`. Returns a list of `block.fid`.

### Responses

##### `200 OK`

```json
[
    STRING,
    STRING,
    ...
]
```

## `GET api/v1/protected/blocks/reserved/admin`

Gets all blocks that have a status of `RESERVED`, in descending order of reservation date. Can only be called by admins.

### Responses

##### `200 OK`

```json
{
    "blocks": [
        {
            "fid": STRING,
            "username": STRING,
            "dateUpdated": TIMESTAMP
        }
    ],
    ...
}
```

## `GET api/v1/protected/blocks/done/admin`

Gets all blocks that have a status of `DONE`, in descending order of completion date. Can only be called by admins.

### Responses

##### `200 OK`

```json
{
    "blocks": [
        {
            "fid": STRING,
            "username": STRING,
            "dateUpdated": TIMESTAMP
        }
    ],
    ...
}
```

## `GET /api/v1/protected/blocks/export`

Export Block and User/Team information. This is an admin-only route.
Returns a `text/csv` response that includes
every block number, the status of the block,
the user that is associated with it (first name, last name, email, username),
the time it last updated, and the names of the teams the user is currently on.

### Responses

##### `200 OK`

```text
STRING,STRING,STRING,...
STRING,STRING,STRING,...
...
```
