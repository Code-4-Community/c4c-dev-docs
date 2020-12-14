# SFTT API Specification
This is the official API specification for the SFTT back end. The back end is implemented as is described in he documentation below and should correspond exactly with how the back end behaves. If you find any inconsistencies please create a PR to amend the error.

!!! info "Java Web Tokens (JWT)"
    In every request header is a JWT. The JWT will contain the user's id value and the user's privilege level.
    It is assumed that the user making the request (as specified in the JWT header) is the person the action is being performed by.

## Reservations Router
This router is used to manage reservations. A reservation is when a user claims a block and thereby commits to going around this block and mapping every tree they see on their side of the block. A block is completed when they have walked around the block, mapped every tree and subsequently confirmed on the app that they did completed those actvities.

### Make a Reservation

`POST api/v1/protected/reservations/reserve`

Must be called on an open block.

#### Request Body

```json
{
  "block": INT
}
```

#### Responses

##### `200 OK`
This block was reserved successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

### Complete a Reservation

`POST api/v1/protected/reservations/complete`

Must be called on an open block.

#### Request Body

```json
{
  "block": INT
}
```

#### Responses

##### `200 OK`
This block was completed successfully.

##### `400 BAD REQUEST`
If the block id specified is invalid.

##### `401 BAD REQUEST`

### Cancel a Reservation

### Delete a Reservation (Admin Only)

## Blocks Router

### Get All Blocks

### Get All Reserved Blocks

### Get All Completed Blocks

### Get All Blocks Per Neighborhood

## Map Router

### Get All Blocks in GeoJSON

### Get All Neighborhoods in GeoJSON

### Get All Sites in GeoJSON

## Users Router

### Get a User 

## Teams Router

### Create a Team

### Add a Goal

### Delete a Goal

### Invite a User

### Get Applicants

### Apply to a Team

### Approve a User

### Reject a User

### Kick a User

### Leave a Team

### Disband a Team

### Transfer Team Ownership

## Leaderboard Router

### Get All Teams

### Get All Users

### Get a Team