# SFTT API Specification
This is the official API specification for the SFTT back end. The back end is implemented as is described in the documentation below and should correspond exactly with how the back end behaves. If you find any inconsistencies please create a PR to amend the error.

!!! info "JSON Web Tokens (JWT)"
    In every request header is a JWT. The JWT will contain the user's id value and the user's privilege level.
    It is assumed that the user making the request (as specified in the JWT header) is the person the action is being performed by.

## Enums

### Privilege Level

The privilege level enum is all the possible privilege levels a user can have. This is stored in the users table as the privilege_level field. The possible options are:

- `STANDARD` - A regular member of the platform and usually a volunteer for Speak for the Trees.
- `ADMIN` - A more senior SFTT volunteer, might be in charge of a neighborhood or someone that works for SFTT. They are able to mark blocks for QA, change normal or admin user privilege levels and other activities that require elevated privileges.
- `SUPER_ADMIN` - A super user with elevated privileges such as being able to import data into the database.

### Site Owner

The site owner enum is all of the possible groups that a site can be owned by. This is stored in the sites table as the owner field. The possible options are:

- `ROW` - A Right of Way (Street) site. It is planted, owned, and maintained by the city of Boston.
- `Park` - A Park site. It is planted, owned, and maintained by the city of Boston.
- `State` - A site owned by the state (usually on MBTA or DCR land).
- `Federal` - A site owned by the federal government.
- `Private` - A site owned by a private company or resident.

### Team Role

The team role enum is all the possible states a use can be in in relation to a team. This is stored in the users_teams table on the backend as the team_role field. The possible options are:

- `NONE` - The user is not on the team. This is possible after a user is rejected from a team or leaves a team.
- `MEMBER` - The is a member of the team.
- `LEADER` - The user is a leader of the team. This grants them special privileges such as being able to accept or reject pending members and being able to disband the team.
- `PENDING` - The user has applied to the team but has not been accepted or rejected yet. If they're accepted they become a member, if they're rejected their status is set to `NONE`

A user is said to be on a team if their role is `MEMBER` or `LEADER`. 

### Reservation Action

The reservation action enum is all the possible action types a reservation can have. This is how blocks are marked as open, complete, reserved, etc. It is stored in the reservation tables as the action_type field. The possible options are:

- `RESERVE` - Marks a block as reserved.
- `COMPLETE` - Marks a block as completed.
- `RELEASE` - Marks a block as released, meaning a user cancelled their reservation and the block is now open again. Anyone can reserve it again now.
- `UNCOMPLETE` - Marks a block as uncomplete, meaning an admin opened the block back up from its previous state. Anyone can reserve it again now.
- `QA` - Indicates a block needs QA. It will remain in this state until an admin either approves or denies the completion. If approved, the block will be marked `COMPLETE`. If denied, the block will be marked `UNCOMPLETE`.


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
  "freshAccessToken" : JWT,
}
```

##### `401 Unauthorized`
The refresh token is invalid.

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

### Create Secret Key

!!! missing "This route still needs to be implemented"

`GET api/v1/user/create_secret/:user_id`

Used to create secret keys for users.

#### Request Body

None. The `user_id` parameter is the id of the user the secret key should be linked to.

#### Responses

##### `200 OK`
A secret key was created and stored for the given user.

##### `400 BAD REQUEST`
The `user_id` is not associated with a user.

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

## Users Router

This router is used for routes that affects user accounts. It can only be called when a user is authenticated, as opposed to the auth router which is public.

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

### Change Email

`POST api/v1/protected/user/change_email`

Allows a user to change their email address. The new email address cannot be in use already. Also sends the user a confirmation email.

#### Request Body

```json
{
  "newEmail": STRING,
  "password": STRING
}
```

#### Responses

##### `200 OK`
The email address change was successful.

##### `400 BAD REQUEST`
If the request was malformed.

##### `401 Unauthorized`
The password does not match the calling user's current password.

##### `409 Conflict`
The given `newEmail` is already in use.

### Get User Data

`GET api/v1/protected/user/data`

Get the calling user's data.

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "firstName": STRING,
  "lastName": STRING,
  "email": EMAIL,
  "username": STRING
}
```

##### `400 BAD REQUEST`
If the calling user does not exist.

### Get User Teams

`GET api/v1/protected/user/teams`

Get all the teams the calling user is part of.

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "teams": [
    {
      "teamId": INT,
      "teamName": STRING
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`
If the calling user does not exist.

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

If the `user_id` is not associated with a user.

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

##### `401 Unauthorized`

The user does not have a high enough privilege level to change the given users privilege level.

- An 'ADMIN' user tries to change the privilege level of a 'SUPER_ADMIN'
- An 'ADMIN' user tries to give another user 'SUPER_ADMIN' privilege

### Create Child Account (Admin Only)

`POST api/v1/protected/user/create_child`

Allows admins to create a child user linked to the admin's account through the parent_accounts table.

#### Request Body
(Request fields are for the child account)
```json
{
  "username" : STRING,
  "email" : EMAIL,
  "password" : STRING,
  "firstName" : STRING,
  "lastName" : STRING
}
```

#### Responses

##### `201 Created`
The username and email are still available, and a child user has been successfully created.
 
##### `400 Bad Request`
Malformed request body.

##### `401 Unauthorized`

The user does not have a high enough privilege level to create a child user.

##### `409 Conflict`
The given email or username is already in use.

```json
"Error creating new child user, given email %s already used"
```

```json
"Error creating new child user, given username %s already used"
```

### Get Child User Data

`GET api/v1/protected/user/child_data`

!!! missing "This route still needs to be implemented"

Get the user data for all of the calling user's child accounts, including their user IDs and first and last names.

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "childData": [
    {
      "userId": INT,
      "firstName": STRING,
      "lastName": STRING
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the calling user does not exist.

## Map Router


This router is used to retrieve the data necessary to render blocks, neighborhoods and sites (plantings sites and trees). It makes a call to the backend, which returns the required data from the database in GeoJSON format. The router is public since much of this information is also rendered on the home page.

### Get All Blocks in GeoJSON

`GET api/v1/map/blocks`

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

`GET api/v1/map/neighborhoods`

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

`GET api/v1/map/sites`

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
        "common_name": STRING,
        "planting_date": TIMESTAMP,
        "adopter_id": INT,
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

### Import Blocks (Super Admin Only)

`POST api/v1/protected/import/blocks`

Used to import blocks into the database. Since blocks reference neighborhoods with a foreign key make sure all neighborhoods are imported first.

#### Request Body

```json
{
  "blocks": [
    {
      "blockId": INT,
      "neighborhoodId": INT,
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

### Import Neighborhoods (Super Admin Only)

`POST api/v1/protected/import/neighborhoods`

Used to import neighborhoods into the database. Must be called before importing blocks since blocks reference neighborhoods.

#### Request Body

```json
{
  "neighborhoods": [
    {
      "neighborhoodId": INT,
      "name": STRING,
      "sqmiles": DOUBLE,
      "lat": LONG,
      "lng": LONG,
      "geometry": STRING,
      "canopyCoverage": DOUBLE,
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

### Import Reservations (Super Admin Only)

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

### Import Sites (Super Admin Only)

`POST api/v1/protected/import/sites`

Used to import sites into the database. See below for a description of each ambiguous field.

`status`: "Alive" or "Dead but standing"<br/>
`confidence`: Confidence in species or genus identification<br/>
`coverage`: Percent of tree that is green<br/>
`pruning`: Amount of pruning seen in tree<br/>
`condition`: Tree condition ("Good", "Fair", "Poor", "Dead")<br/>
`discoloring`: Discoloring of leaves<br/>
`leaning`: Tree is leaning<br/>
`constricting_grate`: Metal grate constricting tree trunk growth<br/>
`wounds`: Trunk wounds<br/>
`pooling`: Pooling water in tree pit<br/>
`stakes_with_wires`: Wooden stakes near tree WITH wires around tree<br/>
`stakes_without_wires`: Wooden stakes near tree WITHOUT wires<br/>
`lights`: Lights in tree<br/>
`bicycle`: Bicycle locked to tree trunk<br/>
`bag_empty`: Watering bag that is empty<br/>
`bag_filled`: Watering bag that has water in it<br/>
`tape`: Tape around trunk<br/>
`site_type`: "Pit", "Continuous/Lawn" or "Planter"<br/>
`material`: Material in site ("Plain dirt", "mulch", "grass", etc.)<br/>
`fence`: Do you see a fence or perimeter guard?<br/>
`trash`: Do you see loose trash?<br/>
`wires`: Wires overhead?<br/>
`grate`: Do you see a metal grate, either with or without a tree?<br/>
`melnea_cass_trees`: Part of a program, not used anymore, only in database<br/>
`mcb_number`: Melnea Cass number, not used anymore, only in database<br/>
`tree_dedicated_to`: Tree dedication, not used currently<br/>

#### Request Body

```json
{
  "sites": [
    {
      "siteId": INT,
      "blockId": INT,
      "lat": LONG,
      "lng": LONG,
      "city": STRING,
      "zip": STRING,
      "address": STRING,
      "neighborhoodId": INT,
      "deletedAt": TIMESTAMP,
      "userId": INT,
      "username": USERNAME,
      "updatedAt": TIMESTAMP,
      "qa": BOOLEAN,
      "treePresent": BOOLEAN,
      "status": STRING,
      "genus": STRING,
      "species": STRING,
      "commonName": STRING,
      "confidence": STRING,
      "multistem": BOOLEAN,
      "diameter": DOUBLE,
      "circumference": DOUBLE,
      "coverage": STRING,
      "pruning": STRING,
      "condition": STRING,
      "discoloring": BOOLEAN,
      "leaning": BOOLEAN,
      "constrictingGrate": BOOLEAN,
      "wounds": BOOLEAN,
      "pooling": BOOLEAN,
      "stakesWithWires": BOOLEAN,
      "stakesWithoutWires": BOOLEAN,
      "light": BOOLEAN,
      "bicycle": BOOLEAN,
      "bagEmpty": BOOLEAN,
      "bagFilled": BOOLEAN,
      "tape": BOOLEAN,
      "suckerGrowth": BOOLEAN,
      "siteType": STRING,
      "sidewalkWidth": STRING,
      "siteWidth": STRING,
      "siteLength": STRING,
      "material": STRING,
      "raisedBed": BOOLEAN,
      "fence": STRING,
      "trash": STRING,
      "wires": STRING,
      "grate": STRING,
      "stump": STRING,
      "treeNotes": STRING,
      "siteNotes": STRING,
      "melneaCassTrees": STRING,
      "mcbNumber": INT,
      "treeDedicatedTo": STRING
    },
    ...
  ]
}
```

#### Responses

##### `200 OK`

Sites imported successfully.

##### `400 BAD REQUEST`

If the request was malformed.

##### `401 UNAUTHORIZED`

If the user is not a super admin.

## Neighborhoods Router

The neighborhoods router is used to handle all the neighborhoods.

### Edit Canopy Coverage (Admin Only)

`POST api/v1/protected/neighborhoods/:neighborhood_id/edit_canopy`

Used to edit the specified neighborhood's `canopy_coverage`.

#### Request Body

```json
{
  "canopyCoverage": DOUBLE
}
```

#### Responses

##### `200 OK`

Neighborhood's `canopy_coverage` successfully edited to `canopyCoverage`.

##### `400 BAD REQUEST`

If the request body is malformed or if the given `canopyCoverage` is negative or greater than one.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Send Email (Admin Only)

`POST api/v1/protected/neighborhoods/send_email`

Sends an email with the given message to users in certain neighborhoods. If `neighborhoods` is an empty list, send the email to everyone.

#### Request Body
```json
{
  "neighborhoodIDs": [
    INT,
    INT,
    ...
  ],
  "emailBody": STRING
}
```

#### Responses

##### `200 OK`

Email successfully sent to users in the specified neighborhoods.

##### `400 BAD REQUEST`

If the request body is malformed.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

## Sites Router

The sites router is used to handle all the sites and create new ones. A site can be either a planting site without a tree or there can be a tree present. They are differentiated by the field `tree_present` in the `site_entries` table. 

### Add Site

`POST api/v1/protected/sites/add`

Used to create a new site. Will create two entries in the database. One in the `sites` table to record the permanent information (location, address, block_id) and one in the `site_entries` table to record the state of the site (species, foliage, leaning, trash, etc.). Every field besides `lat`, `lng`, `city`, `zip` and `address` is allowed to be `NULL`.
`NULL` `BOOLEAN`s will be treated as `false`.

All measurements should be given in inches.
Note that while some measurements are accepted as strings to work with existing data, new 
measurements should be given as numbers and assumed to be in inches.

#### Request Body

```json
{
    "blockId": INT | NULL,
    "lat": LONG,
    "lng": LONG,
    "city": STRING,
    "zip": STRING,
    "address": STRING,
    "neighborhoodId": INT,
    "treePresent": BOOLEAN | NULL,
    "status": STRING | NULL,
    "genus": STRING | NULL,
    "species": STRING | NULL,
    "commonName": STRING | NULL,
    "confidence": STRING | NULL,
    "diameter": DOUBLE | NULL,
    "circumference": DOUBLE | NULL,
    "multistem": BOOLEAN | NULL,
    "coverage": STRING | NULL,
    "pruning": STRING | NULL,
    "condition": STRING | NULL,
    "discoloring": BOOLEAN | NULL,
    "leaning": BOOLEAN | NULL,
    "constrictingGrate": BOOLEAN | NULL,
    "wounds": BOOLEAN | NULL,
    "pooling": BOOLEAN | NULL,
    "stakesWithWires": BOOLEAN | NULL,
    "stakesWithoutWires": BOOLEAN | NULL,
    "light": BOOLEAN | NULL,
    "bicycle": BOOLEAN | NULL,
    "bagEmpty": BOOLEAN | NULL,
    "bagFilled": BOOLEAN | NULL,
    "tape": BOOLEAN | NULL,
    "suckerGrowth": BOOLEAN | NULL,
    "siteType": STRING | NULL,
    "sidewalkWidth": STRING | NULL,
    "siteWidth": DOUBLE | NULL,
    "siteLength": DOUBLE | NULL,
    "material": STRING | NULL,
    "raisedBed": BOOLEAN | NULL,
    "fence": BOOLEAN | NULL,
    "trash": BOOLEAN | NULL,
    "wires": BOOLEAN | NULL,
    "grate": BOOLEAN | NULL,
    "stump": BOOLEAN | NULL,
    "treeNotes": STRING | NULL,
    "siteNotes": STRING | NULL
}
```

#### Responses

##### `200 OK`

Site successfully added.

##### `400 BAD REQUEST`

If the request body is malformed.

### Add Sites (Admin Only)

`POST api/v1/protected/sites/add_sites`

Used to add multiple new sites. The request body is the content of a CSV file and must contain the following columns: `blockId`, `lat`, `lng`, `zip`, `address`, and `neighborhoodId`. Every column besides `lat`, `lng`, and `neighborhoodId` is allowed to be `NULL` and optional. All columns may appear in any order. Blank cell values are treated as default or empty values (`null`, `""`, `false`, etc.).

For more information, refer to the documentation on adding a site (https://docs.c4cneu.com/sftt/sftt-api-spec/#add-a-site).

#### Request Body

```
blockId, lat, lng, city, zip, address, neighborhood, treePresent, status, genus, species, commonName, confidence, diameter, circumference, multistem, coverage, pruning, condition, discoloring, leaning, constrictingGrate, wounds, pooling, stakesWithWires, stakesWithoutWires, light, bicycle, bagEmpty, bagFilled, tape, suckerGrowth, siteType, sidewalkWidth, siteWidth, siteLength, material, raisedBed, fence, trash, wires, grate, stump, treeNotes, siteNotes
INT | NULL, LONG, LONG, STRING | NULL, STRING | NULL, STRING | NULL, STRING, BOOLEAN | NULL, STRING | NULL, STRING | NULL, STRING | NULL, STRING | NULL, STRING | NULL, DOUBLE | NULL, DOUBLE | NULL, BOOLEAN | NULL, STRING | NULL, STRING | NULL, STRING | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, STRING | NULL, STRING | NULL, DOUBLE | NULL, DOUBLE | NULL, STRING | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, BOOLEAN | NULL, STRING | NULL, STRING | NULL
...
```

#### Responses

##### `200 OK`

All sites successfully added.

##### `400 BAD REQUEST`

If the request body is malformed.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Add Potential Site

!!! missing "This route still needs to be implemented"

`POST api/v1/protected/sites/create_potential`

Creates a potential site, which is a place where the city could potentially place a planting site. Below is a description of each field.

`light_pole`: 10 feet from light pole</br>
`drive_way`: 10 feet from driveway</br>
`hydrant`: 10 feet from hydrant</br>
`intersection`: 20 feet from intersections</br>
`building_entrance`: Not in front of a building entrance

#### Request Body

```json
{
  "block_id": INT | NULL,
  "lat": LONG,
  "lng": LONG,
  "city": STRING,
  "zip": STRING,
  "address": STRING,
  "neighborhood_id": INT,
  "deleted_at": TIMESTAMP,
  "light_pole": BOOLEAN,
  "drive_way": BOOLEAN,
  "hydrant": BOOLEAN,
  "intersection": BOOLEAN,
  "building_entrance": BOOLEAN,
  "notes": TEXT
}
```

#### Responses

##### `200 OK`

Site successfully added.

##### `400 BAD REQUEST`

If the request body is malformed.

### Get Site

`GET api/v1/sites/:site_id`

Returns all the info about a specific site. This includes all the entries linked to the site, in reverse chronological order (most recent first).
Measurements are given in inches.

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "siteId": INT,
  "blockId": INT | NULL,
  "lat": LONG,
  "lng": LONG,
  "city": STRING,
  "zip": STRING,
  "address": STRING,
  "neighborhoodId": INT,
  "entries": [
    {
      "id": INT,
      "username": USERNAME,
      "updatedAt": TIMESTAMP,
      "treePresent": BOOLEAN | NULL,
      "status": STRING | NULL,
      "genus": STRING | NULL,
      "species": STRING | NULL,
      "commonName": STRING | NULL,
      "confidence": STRING | NULL,
      "diameter": DOUBLE | NULL,
      "circumference": DOUBLE | NULL,
      "multistem": BOOLEAN | NULL,
      "coverage": STRING | NULL,
      "pruning": STRING | NULL,
      "condition": STRING | NULL,
      "discoloring": BOOLEAN | NULL,
      "leaning": BOOLEAN | NULL,
      "constrictingGrate": BOOLEAN | NULL,
      "wounds": BOOLEAN | NULL,
      "pooling": BOOLEAN | NULL,
      "stakesWithWires": BOOLEAN | NULL,
      "stakesWithoutWires": BOOLEAN | NULL,
      "light": BOOLEAN | NULL,
      "bicycle": BOOLEAN | NULL,
      "bagEmpty": BOOLEAN | NULL,
      "bagFilled": BOOLEAN | NULL,
      "tape": BOOLEAN | NULL,
      "suckerGrowth": BOOLEAN | NULL,
      "siteType": STRING | NULL,
      "sidewalkWidth": STRING | NULL,
      "siteWidth": DOUBLE | NULL,
      "siteLength": DOUBLE | NULL,
      "material": STRING | NULL,
      "raisedBed": BOOLEAN | NULL,
      "fence": BOOLEAN | NULL,
      "trash": BOOLEAN | NULL,
      "wires": BOOLEAN | NULL,
      "grate": BOOLEAN | NULL,
      "stump": BOOLEAN | NULL,
      "treeNotes": STRING | NULL,
      "siteNotes": STRING | NULL,
      "adopter": USERNAME | NULL,
      "image": STRING | NULL
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the `site_id` specified is invalid.

### Edit Site (Admin Only)

`POST api/v1/protected/sites/:site_id/edit`

Used to edit the features of a site. This is done by querying the appropriate site and setting the given features. Only the `block_id` field is not required.

#### Request Body

```json
{
  "blockId": INT | NULL,
  "address": STRING,
  "city": STRING,
  "zip": STRING,
  "lat": DOUBLE,
  "lng": DOUBLE,
  "neighborhoodId": INT
}
```

#### Responses

##### `200 OK`

Features of site successfully edited.

##### `400 BAD REQUEST`

If any of the specified `site_id`, `block_id`, or `neighborhood_id` are invalid. An invalid id is a non-existent id or the id of a deleted site, block, or neighborhood.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Delete Site (Admin Only)

`POST api/v1/protected/sites/:site_id/delete`

Used to delete a site. This is done by setting `deleted_at` in the `sites` table to the current timestamp.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Site successfully deleted.

##### `400 BAD REQUEST`

If the `site_id` specified is invalid.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Add Site Entry

`POST api/v1/protected/sites/:site_id/update`

Used to update the state of a site. A new entry will be made in the `site_entries` table that will 
record the latest state of the site and so update the state of that site. Every field can be `NULL`. 
`NULL` `BOOLEAN`s will be treated as `false`. 

All measurements should be given in inches.
Note that while some measurements are accepted as strings to work with existing data, new 
measurements should be given as numbers and assumed to be in inches.

#### Request Body

```json
{
  "treePresent": BOOLEAN | NULL,
  "status": STRING | NULL,
  "genus": STRING | NULL,
  "species": STRING | NULL,
  "commonName": STRING | NULL,
  "confidence": STRING | NULL,
  "diameter": DOUBLE | NULL,
  "circumference": DOUBLE | NULL,
  "multistem": BOOLEAN | NULL,
  "coverage": STRING | NULL,
  "pruning": STRING | NULL,
  "condition": STRING | NULL,
  "discoloring": BOOLEAN | NULL,
  "leaning": BOOLEAN | NULL,
  "constrictingGrate": BOOLEAN | NULL,
  "wounds": BOOLEAN | NULL,
  "pooling": BOOLEAN | NULL,
  "stakesWithWires": BOOLEAN | NULL,
  "stakesWithoutWires": BOOLEAN | NULL,
  "light": BOOLEAN | NULL,
  "bicycle": BOOLEAN | NULL,
  "bagEmpty": BOOLEAN | NULL,
  "bagFilled": BOOLEAN | NULL,
  "tape": BOOLEAN | NULL,
  "suckerGrowth": BOOLEAN | NULL,
  "siteType": STRING | NULL,
  "sidewalkWidth": STRING | NULL,
  "siteWidth": DOUBLE | NULL,
  "siteLength": DOUBLE | NULL,
  "material": STRING | NULL,
  "raisedBed": BOOLEAN | NULL,
  "fence": BOOLEAN | NULL,
  "trash": BOOLEAN | NULL,
  "wires": BOOLEAN | NULL,
  "grate": BOOLEAN | NULL,
  "stump": BOOLEAN | NULL,
  "treeNotes": STRING | NULL,
  "siteNotes": STRING | NULL
}
```

#### Responses

##### `200 OK`

Site successfully updated.

##### `400 BAD REQUEST`

If the request body is malformed.
If the id specified is invalid. An invalid id is a non-existent id or the id of a deleted site.

### Edit Site Entry (Admin Only)

!!! missing "This route still needs to be implemented"

`POST api/v1/protected/sites/edit_entry/:entry_id`

Updates a site's existing site entry with the given values. Every field can be `NULL`. `NULL` `BOOLEAN`s will be treated as `false`. All measurements should be given as numbers in inches.

#### Request Body

```json
{
  "treePresent": BOOLEAN | NULL,
  "status": STRING | NULL,
  "genus": STRING | NULL,
  "species": STRING | NULL,
  "commonName": STRING | NULL,
  "confidence": STRING | NULL,
  "diameter": DOUBLE | NULL,
  "circumference": DOUBLE | NULL,
  "multistem": BOOLEAN | NULL,
  "coverage": STRING | NULL,
  "pruning": STRING | NULL,
  "condition": STRING | NULL,
  "discoloring": BOOLEAN | NULL,
  "leaning": BOOLEAN | NULL,
  "constrictingGrate": BOOLEAN | NULL,
  "wounds": BOOLEAN | NULL,
  "pooling": BOOLEAN | NULL,
  "stakesWithWires": BOOLEAN | NULL,
  "stakesWithoutWires": BOOLEAN | NULL,
  "light": BOOLEAN | NULL,
  "bicycle": BOOLEAN | NULL,
  "bagEmpty": BOOLEAN | NULL,
  "bagFilled": BOOLEAN | NULL,
  "tape": BOOLEAN | NULL,
  "suckerGrowth": BOOLEAN | NULL,
  "siteType": STRING | NULL,
  "sidewalkWidth": STRING | NULL,
  "siteWidth": DOUBLE | NULL,
  "siteLength": DOUBLE | NULL,
  "material": STRING | NULL,
  "raisedBed": BOOLEAN | NULL,
  "fence": BOOLEAN | NULL,
  "trash": BOOLEAN | NULL,
  "wires": BOOLEAN | NULL,
  "grate": BOOLEAN | NULL,
  "stump": BOOLEAN | NULL,
  "treeNotes": STRING | NULL,
  "siteNotes": STRING | NULL
}
```

#### Responses

##### `200 OK`

Specified site entry successfully edited.

##### `400 BAD REQUEST`

If the request body is malformed.
If the `entry_id` specified is not associated with an existing site entry.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Delete Site Entry (Admin Only)

!!! missing "This route still needs to be implemented"

`POST api/v1/protected/sites/delete_entry/:entry_id`

Deletes the specified site entry from the database.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Site entry successfully deleted.

##### `400 BAD REQUEST`

If the `entry_id` specified is not associated with an existing activity.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Upload Site Image (Admin and Owner only)

!!! missing "This route still needs to be implemented"

`POST api/v1/protected/sites/upload_image/:site_id`

Adds an image of a site's most recent site entry. Only users who are owners of the specified site, Admins, or Super Admins can perform this action. The `imageEncoding` field must be a valid base 64 encoding of the image to upload.

#### Request Body

```json
{
  "imageEncoding": STRING
}
```

#### Responses

##### `200 OK`

Image successfully added.

##### `400 BAD REQUEST`

If the request body is malformed.
If the specified site ID is non-existent.
If the given base64 encoding of the image is non-null and invalid.

##### `401 UNAUTHORIZED`

If a standard user that does not own the site tries to perform this action.

### Delete Site Image (Admin and Owner only)

`POST api/v1/protected/sites/delete_image/:image_id`

Deletes the site image with the given `image_id`. Only users who are owners of the specified site, Admins, or Super Admins can perform this action.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Image successfully deleted.

##### `400 BAD REQUEST`

If the `image_id` specified is not associated with an existing site image.

##### `401 UNAUTHORIZED`

If a standard user that does not own the site tries to perform this action.

### Mark Site for QA (Admin Only)

!!! missing "This route still needs to be implemented"

`POST api/v1/protected/sites/:site_id/qa`

Used to indicate that a site needs to be checked. This can be done for an individual site or can be part of marking an entire block for QA and then every site associated with that block will be marked for QA. This is done by creating a new entry in the `site_entries` table with `qa` set to `true`.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Site successfully marked for QA.

##### `400 BAD REQUEST`

If the `site_id` specified is invalid.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

### Adopt Site

`POST api/v1/protected/sites/:site_id/adopt`

Adopt the given site. Creates a record in the adopted sites table linking the user and the site.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Site successfully marked as adopted.

##### `400 BAD REQUEST`

If the `site_id` specified does not exist or is already adopted.

### Parent Adopt Site for Child

`POST api/v1/protected/sites/:site_id/parent_adopt`

Adopt the given site on behalf of a specified child account. Creates a record in the adopted sites table linking the child user and the site.

#### Request Body

```json
{
  "childUserId": INT
}
```

#### Responses

##### `200 OK`

Site successfully marked as adopted.

##### `400 BAD REQUEST`

If the `site_id` specified does not exist or is already adopted. 

##### `400 BAD REQUEST`

If the user corresponding to the childUserId either does not exist or is not a child account of the parent user.

### Unadopt Site

`POST api/v1/protected/sites/:site_id/unadopt`

Remove this site as adopted for the calling user. Removes the record from the adopted sites table.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Successfully removed site as adopted.

##### `400 BAD REQUEST`

If the `site_id` specified does not exist.

##### `400 BAD REQUEST`

If the site is not adopted by the user.

### Force Unadopt Site (Admin only)

`POST api/v1/protected/sites/:site_id/force_unadopt`

Remove this site as adopted for whoever currently adopts it. Can only be called by a user of a higher privilege level than the adopter. Removes the record from the adopted sites table.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Successfully removed site as adopted.

##### `400 BAD REQUEST`

If the `site_id` specified does not exist.

##### `401 UNAUTHORIZED`

If the user calling is not of the proper privilege level.

### Get Adopted Sites

`GET api/v1/protected/sites/adopted_sites`

Get all the adopted sites of the calling user.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Return a list of integers representing the site ID's of the users adopted sites. Can be an empty list.

```json
{
  "adoptedSites": [
    INT,
    INT,
    INT,
    ...
  ]
}
```

### Record Stewardship Activity

`POST api/v1/protected/sites/:site_id/record_stewardship`

Records a stewardship activity for the given site. Date is the day on which the activity was performed, which can be in the past if the user is adding a past activity. Indicate `True` if the activity (watered, mulched, etc.) was completed, else `False`.  At least one activity must be `True`.

#### Request Body

```json
{
  "date": DATE,
  "watered": BOOLEAN,
  "mulched": BOOLEAN,
  "cleaned": BOOLEAN,
  "weeded": BOOLEAN
}
```

#### Responses

##### `200 OK`

Activity successfully recorded.

##### `400 BAD REQUEST`

If the `site_id` specified does not exist.

##### `400 BAD REQUEST`

If all activities are `False`.

### Parent Record Stewardship Activity for Child

`POST api/v1/protected/sites/:site_id/parent_record_stewardship`

Records a stewardship activity on behalf of the specified child account. Date is the day on which the activity was performed, which can be in the past if the parent is adding a past activity for the child. Indicate `True` if the activity (watered, mulched, etc.) was completed, else `False`.  At least one activity must be `True`.

#### Request Body

```json
{
  "childUserId": INT,
  "date": DATE,
  "watered": BOOLEAN,
  "mulched": BOOLEAN,
  "cleaned": BOOLEAN,
  "weeded": BOOLEAN
}
```

#### Responses

##### `200 OK`

Activity successfully recorded.

##### `400 BAD REQUEST`

If the `site_id` specified does not exist.

##### `400 BAD REQUEST`

If the user corresponding to the childUserId either does not exist or is not a child account of the parent user.

##### `400 BAD REQUEST`

If all activities are `False`.

### Edit Stewardship Activity (Admin and Author Only)

`POST api/v1/protected/sites/edit_stewardship/:activity_id`

Edits an existing stewardship activity. Indicate `True` if the activity (watered, mulched, etc.) was completed, else `False`.  At least one activity must be `True`. `date` is the day on which the activity was performed, which can be in the past if the activity was performed in the past.

#### Request Body

```json
{
  "date": DATE,
  "watered": BOOLEAN,
  "mulched": BOOLEAN,
  "cleaned": BOOLEAN,
  "weeded": BOOLEAN
}
```

#### Responses

##### `200 OK`

Activity information successfully edited.

##### `400 BAD REQUEST`

If the `activity_id` specified is not associated with an existing activity.

##### `400 BAD REQUEST`

If all activities are `False`.

##### `401 UNAUTHORIZED`

If the calling user is not an admin or the user listed on the activity.

### Delete Stewardship Activity (Admin and Author Only)

`POST api/v1/protected/sites/delete_stewardship/:activity_id`

Deletes the stewardship activity from the database. This is in case either SFTT believes the activity was not performed or if the activity recorder made a mistake.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Activity successfully removed.

##### `400 BAD REQUEST`

If the `activity_id` specified is not associated with an existing activity.

##### `401 UNAUTHORIZED`

If the calling user is not an admin or the user listed on the activity.

### Get Stewardship Activities By Site

`GET api/v1/sites/:site_id/stewardship_activities`

Returns all the recorded stewardship activities for the indicated site. Array will be sorted by date, from most recent to least recent, and a secondary sort on id. 

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "stewardshipActivities": [
    {
      "id": INT,
      "userId": INT,
      "date": DATE,
      "watered": BOOLEAN,
      "mulched": BOOLEAN,
      "cleaned": BOOLEAN,
      "weeded": BOOLEAN
    }, 
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the `site_id` specified does not exist.

### Name Site Entry

`POST api/v1/protected/sites/:site_id/name_entry`

Names the latest site entry of the given site. If the entry is already named, renames it. If passed an empty string, resets the name.

#### Request Body

```json
{
  "name": STRING
}
```

#### Responses

##### `200 OK`

Site entry successfully named.

##### `400 BAD REQUEST`

If the `site_id` specified does not exist or if `name` is more than 60 characters long or `name` is `NULL`.

##### `401 UNAUTHORIZED`

If the user is not the site's adopter.

### Calculate Tree Benefits

!!! missing "This route still needs to be implemented"

`GET api/v1/sites/:site_id/calculate_benefits`

Calculates and returns the environmental impacts of the latest site entry of the site with the given `site_id`. This includes the following values: energy conserved, stormwater filtered, air quality improved, carbon dioxide removed, and carbon dioxide stored, as well as the amount of money saved for each category.

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "energy": DOUBLE,
  "energyMoney": DOUBLE,
  "stormwater": DOUBLE,
  "stormwaterMoney": DOUBLE,
  "airQuality": DOUBLE,
  "airQualityMoney": DOUBLE,
  "co2Removed": DOUBLE,
  "co2RemovedMoney": DOUBLE,
  "co2Stored": DOUBLE,
  "co2StoredMoney": DOUBLE
}
```

##### `400 BAD REQUEST`

If there is no site with the given `site_id`.

### Filter Sites (Admin Only)

`GET api/v1/protected/sites/filter_sites`

!!! missing "This route still needs to be implemented"

Return data on sites matching all of the given criteria: species of tree, range of adopted date, range of last stewardship activity recorded date, and neighborhood. A criterion is ignored (i.e. sites are not filtered on a criterion) if its value is `NULL`. See below for a description of each criterion.

A site matches the criterion if:

- `treeSpecies`: the species of the site's latest site entry is in the list
- `adoptedStart`: the site has been adopted by a user on or after this date
- `adoptedEnd`: the site has been adopted by a user on or before this date
- `lastActivityStart`: the site's latest stewardship activity was recorded on or after this date
- `lastActivityEnd`: the site's latest stewardship activity was recorded on or before this date
- `neighborhoodIds`: the site is located in a neighborhood in the list

#### Request Body

```json
{
  "treeSpecies": [
    STRING,
    ...
  ] | NULL,
  "adoptedStart": DATE | NULL,
  "adoptedEnd": DATE | NULL,
  "lastActivityStart": DATE | NULL,
  "lastActivityEnd": DATE | NULL,
  "neighborhoodIds": [
    INT,
    ...
  ] | NULL
}
```

#### Responses

##### `200 OK`

In the JSON below, `adopterId` is the adopter's user ID, `adopterActivityCount` is the number of stewardship activities performed within the given `lastActivity` range, if applicable, and `lastActivityWeeks` is the number of weeks since the last stewardship activity.

```json
{
  "filteredSites": [
    {
      "siteId": INT,
      "address": STRING | NULL,
      "adopterId": INT,
      "adopterName": STRING,
      "dateAdopted": DATE,
      "adopterActivityCount": INT,
      "neighborhoodId": INT,
      "lastActivityWeeks": INT | NULL
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the request body is malformed.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

## Report Router

The report router is used to get reports on the status of trees and adoptions.

### Get Community Stats

`GET api/v1/report/stats`

Get statistics on trees and adoptions available to the public.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Returns the number of current adopters, trees currently adopted, and all stewardship activities ever performed.

```json
{
  "communityStats": {
    "adopterCount": INT,
    "treesAdopted": INT,
    "stewardshipActivities": INT
  }
}
```

### Get Adoption Report (Admin Only)

`GET api/v1/protected/report/adoption`

Get information about adopted sites.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Returns a list of information about adopters and stewardship activities for each currently adopted site, grouped by user and in alphabetical order.

```json
{
  "adoptionReport": [
    {
      "siteId": INT,
      "address": STRING,
      "name": STRING,
      "email": STRING,
      "dateAdopted": TIMESTAMP,
      "activityCount": INT,
      "neighborhood": STRING
    },
    ... 
  ]
}
```

##### `401 UNAUTHORIZED`

If the user is not an admin.

### Get Adoption Report as CSV (Admin Only)

`GET api/v1/protected/report/csv/adoption?previousDays=INT`

Get information about adopted sites as a CSV.

#### Query Params

##### previousDays: INT

The number of days in the past that the report covers. The report only includes information about 
sites adopted in the past `previousDays`. This value should be specified, by default it is set to 
include all adopted sites.

#### Responses

##### `200 OK`

Returns a list of information about adopters and stewardship activities for each currently adopted site, 
grouped by user and in alphabetical order, as a CSV formatted as a string.

```
Site ID, Address, Name, Email, Date Adopted, Activity Count, Neighborhood
1, 123 Real St, Jane Doe, janedoe@email.com, 2021-01-31, 1, East Boston
```

##### `400 BAD REQUEST`

If the `previousDays` parameter is not an integer, such as a decimal or a non-numeric value.

##### `401 UNAUTHORIZED`

If the user is not an admin. 

### Get Stewardship Report (Admin Only)

`GET api/v1/protected/report/stewardship`

Get information about all stewardship activities.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Returns a list of information about adopters and the actions performed for each stewardship activity ever performed, grouped by `site_id` and in reverse chronological order (most recent first).

```json
{
  "stewardshipReport": [
    {
      "siteId": INT,
      "address": STRING,
      "name": STRING,
      "email": STRING,
      "datePerformed": TIMESTAMP,
      "watered": BOOLEAN,
      "mulched": BOOLEAN,
      "cleaned": BOOLEAN,
      "weeded": BOOLEAN,
      "neighborhood": STRING
    },
    ...
  ]
}
```

##### `401 UNAUTHORIZED`

If the user is not an admin.

### Get Stewardship Report as CSV (Admin Only)

`GET api/v1/protected/report/csv/stewardship?previousDays=INT`

Get information about stewardship activities as a CSV.

#### Query Params

##### previousDays: INT

The number of days in the past that the report covers. The report only includes information about 
stewardship activities performed in the past `previousDays`. This value should be specified, by default it is set to 
include all stewardship activities.

#### Responses

##### `200 OK`

Returns a list of information about adopters and the actions performed for each stewardship activity 
ever performed, grouped by `site_id` and in reverse chronological order (most recent first), 
as a CSV formatted as a string.

```
Site ID, Address, Name, Email, Date Performed, Watered, Mulched, Cleaned, Weeded
1, 123 Real St, Jane Doe, janedoe@email.com, 2021-01-31, TRUE, FALSE, FALSE, FALSE
```

##### `400 BAD REQUEST`

If the `previousDays` parameter is not an integer, such as a decimal or a non-numeric value.

##### `401 UNAUTHORIZED`

If the user is not an admin. 

---

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

If there is not a block associated with the given `block_id`.

##### `400 BAD REQUEST`
 
If there is not a team associated with the given `team_id`.

##### `400 BAD REQUEST`

If the block does not have status 'open'.

##### `401 UNAUTHORIZED`

If the user is not a member of the given team.

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

If the `block_id` specified is not associated with an existing block.

##### `400 BAD REQUEST`

If the `team_id` specified is not associatd with an existing team.

##### `400 BAD REQUEST`

If the block does not have status 'reserved'.

##### `401 UNAUTHORIZED`

If the user is not a member of the given team.

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

If the `block_id` specified is not associated with an existing block.

##### `400 BAD REQUEST`

If the block does not have status 'reserved'.

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

If the `block_id` specified is not associated with an existing block.

##### `400 BAD REQUEST`

If the block does not have status 'complete'.

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

If the `block_id` specified is not associated with an existing block.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

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

If the `block_id` specified is not associated with an existing block.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

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

If the `block_id` specified is not associated with an existing block.

##### `401 UNAUTHORIZED`

If the calling user is not an admin.

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
  "finished": BOOLEAN,
  "createdAt": TIMESTAMP,
  "deletedAt": TIMESTAMP | NULL,
  "members": [ // To add
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
      "progress": INT, // To add
      "start_date": TIMESTAMP,
      "complete_by": TIMESTAMP,
      "completion_date": TIMESTAMP | NULL
    },
    ...
  ]
}
```

##### `400 BAD REQUEST`

If the `team_id` specified is not associated with an existing team.

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

If the goal is negative.

##### `400 BAD REQUEST`

If the `complete_by` date is before the `start_at` date.

##### `400 BAD REQUEST`

If the `team_id` specified is not associated with an existing team.

##### `401 UNAUTHORIZED`

If the calling user is not team leader.

### Delete a Goal

Team leader only. Deletes a goal from this team's list of goals. Simply removes the record from the table.

`POST api/v1/protected/teams/:team_id/delete_goal/:goal_id`

#### Request Body

No request body.

##### `400 BAD REQUEST`

If the `goal_id` specified is not associated with an existing goal.

##### `400 BAD REQUEST`

If the `team_id` specified is not associated with an existing team.

##### `401 UNAUTHORIZED`

If the calling user is not team leader.

### Invite a User

`POST api/v1/protected/teams/:team_id/invite`

Invite someone to join a team. Will send an email to all specified people that includes a link. Link will direct them to the team page where they can join once they are authenticated. If one of the email addresses is invalid or the user is already on the team that invite will not be send out, the other ones will be and a `200 OK` response is returned.

!!! Still in progress, emails for this route are not implemented.

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

If the `team_id` specified is not associated with an existing team.

##### `401 UNAUTHORIZED`

If the calling user is not team leader.

### Get Applicants

`GET api/v1/protected/teams/:team_id/applicants`

Get the userIds for anyone that has requested to join this team as a map from userId to the "PENDING" team status

#### Request Body

No request body.

#### Responses

##### `200 OK`

```json
{
  "users": {
    STRING: "PENDING",
    STRING: "PENDING",
    ...
  },
}
```

##### `400 BAD REQUEST`

If the `team_id` specified is not associated with an existing team.

### Apply to a Team

`POST api/v1/protected/teams/:team_id/apply`

Apply to join this team. Any member can apply to join a team that they are not currently on. They will have to be approved by the team leader before becoming an actual member of the team.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Applied successfully.

##### `400 BAD REQUEST`

If the `team_id` specified is not associated with an existing team.

##### `400 BAD REQUEST`

If the user is already on the team or the user's status on this team is "PENDING".

### Approve a User

`POST api/v1/protected/teams/:team_id/applicants/:user_id/approve`

Team Leader only. Approve this applicant's request to join the team. The user_id will be the same as the id returned in the GET applicants API call.

#### Request Body

No request body.

#### Responses

##### `200 OK`

This member has joined the team.

##### `400 BAD REQUEST`

If the `team_id` specified is not associated with an existing team.

##### `400 BAD REQUEST`

If the `user_id` specified is not associated with a user with status "PENDING" on this team.

##### `400 BAD REQUEST` 

If the user that created the request no longer exists.

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

If the `team_id` specified is not associated with an existing team.

##### `400 BAD REQUEST`

If the `user_id` specified is not associated with a user with status "PENDING" on this team.

##### `400 BAD REQUEST` 

If the user that created the request no longer exists.

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

If the `team_id` specified is not associated with an existing team.

##### `400 BAD REQUEST`

If the user is no longer on the team.

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

If the `team_id` specified is not associated with an existing team.

##### `400 BAD REQUEST`

If the user is not on the team.

##### `400 BAD REQUEST`

If the user is the leader of the team.

### Disband a Team

`POST api/v1/protected/teams/:team_id/disband`

Leader only. Disband this team. The `deleted_at` field will be set to the current timestamp.

#### Request Body

No request body.

#### Responses

##### `200 OK`

Successfully deleted team.

##### `400 BAD REQUEST`

If the `team_id` specified is not associated with an existing team.

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

If the `user_id` specified is not associated with a user.

##### `400 BAD REQUEST`

If the given user is not on the team.

##### `401 UNAUTHORIZED`

If the requesting user is not the team leader.


## Leaderboard Router

The leaderboard router is used to get the information displayed on the leaderboard, which is blocks completed per user. It is public so that a future release can include a public leaderboard page. Since none of the information here is strictly private, the consequences of making it public are minimal.

A completed block is a block for which the last entry is either `complete` or `qa`. The user and team that are referenced in that last entry are the user and team that should be credited with the completion. If the user is `NULL`, no user will be credited with that completion, and the same goes for a `NULL` team field. The tie breaker for users or teams with the same number of completed blocks is whatever order the database returns the values in.

### Get Users Leaderboard

`GET api/v1/leaderboard/users?previousDays=INT`

Returns a list of the top 100 users with counted blocks, of usernames and the blocks those users counted, in order of the number of blocks they counted from most to least. All users with `SUPER_ADMIN` privilege level are excluded.

#### Query Params

##### previousDays: INT

The number of days in the past the leaderboard is representing. Only blocks completed within the last specified number of days will be counted towards the leaderboard. This value should be specified, by default it is set to 100 days.

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

`GET api/v1/leaderboard/teams?previousDays=INT`

Returns a list of the top 100 team's names and the blocks those teams counted, in order of the number of blocks they counted from most to least. Only teams with blocks counted will be shown.

#### Query Params

##### previousDays: INT

The number of days in the past the leaderboard is representing. Only blocks completed within the last specified number of days will be counted towards the leaderboard. This value should be specified, by default it is set to 100 days.

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
