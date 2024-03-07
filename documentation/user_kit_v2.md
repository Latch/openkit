## Users API

### Create users and grant access

Partners can invite users, without the need of creating them ahead of time, and grant access to a set of doors. This will be done by using a partner-scoped token from the BE.

1. POST from the Partner BE to the Latch BE with the user and door information

   ```
   POST https://rest.latchaccess.com/access/sdk/v2/users
   ```

   HTTP Headers

   ```
   Authorization: Bearer {{access_token}}
   ```

   HTTP Request Body

   ```
   {
       "firstName": "<string>",
       "lastName": "<string>",
       "email": "<string>",
       "phone": "<string>",
       "startTime": "<datetime>",  // e.g. "2022-09-30T15:11:02.537Z"
       "endTime": "<datetime>",    // e.g. "2022-09-30T15:11:02.537Z"
       "doorUuids": [
         "<string>",
         ...
       ],
       "shareable": <boolean>,
       "passcodeType": "PERMANENT" | "DAILY" | "DAILY_SINGLE_USE",
       "role": "RESIDENT" | "NON_RESIDENT",
       "shouldNotify": <boolean>
   }
   ```

   HTTP Response Body

    ```
    {
       "email": "<string>",
       "firstName": "<string>",
       "lastName": "<string>",
       "userUuid": "<string>",
       "phone": "<string>"
       "accesses": [
         {
           "doorUuid: "<string>",
           "passcodeType": "<string>",
           "shareable": <boolean>,
           "startTime": "<string>",
           "endTime": "<string>",
           "granter": {
             "type": "<string>",
             "uuid": "<string>",
           },
           "role": "<string>",
           "doorcode": {
             "code": "<string>"          // e.g. "1234567"
             "description": "<string>"
           }
         },
         ...
       ]
    }
    ```

#### Validation

| Passcode Type    | Type of Credential | Credential Details                                                                                                                                                    | Validation Notes                                                                                                                                                                                                                    | Downstream Notifications                                                                                                                                                                                                                                                        |
|------------------|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PERMANENT        | Mobile Access      | Access via Latch Consumer App or Partner App                                                                                                                          | Email Required, Phone Optional                                                                                                                                                                                                      | Latch will send Guest a Latch Email Invite.                                                                                                                                                                                                                                     |
| DAILY            | Doorcode           | 7 digit doorcode that works for the entire calendar day set to the timezone of the device.End time from request is not used.                                          | Either email or phone required (not both). Start time must be either on the day of the request or the next day. No start time further in advance will be allowed. Exact start time on the day not honored. Shareable must be false. | If email is provided, Latch will email the doorcode. If phone is provided, Latch will text the doorcode unless an existing User is found with a matching phone number. If a User with that phone number is found and has an email address, Latch will send an email not a text. |
| DAILY_SINGLE_USE | Doorcode           | 7 digit doorcode that works for the entire calendar day set to the timezone of the device, but expires 15 minutes after first use. End time from request is not used. | Either email or phone required (not both). Start time must be either on the day of the request or the next day. No start time further in advance will be allowed. Shareable must be false.                                          | If email is provided, Latch will email the doorcode. If phone is provided, Latch will text the doorcode unless an existing User is found with a matching phone number. If a User with that phone number is found and has an email address, Latch will send an email not a text. |

#### Doorcode Results in API Response

| Passcode Type    | Role         | Door Accessibility Type | Door Connection Status         | API Response                                                                                                                                                                                                 |
|------------------|--------------|-------------------------|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PERMANENT        | NON_RESIDENT | COMMUNAL and/or PRIVATE | CONNECTED                      | A single 7 digit common doorcode for all communal doors that works for the entire duration of access.<br/><br/>A unique 7 digit doorcode for each private door that works for the entire duration of access. |
| PERMANENT        | NON_RESIDENT | COMMUNAL and/or PRIVATE | NOT CONNECTED                  | Same response as above.<br/><br/>NOTE: Permanent doorcodes will not unlock a non-connected door until a sync is performed. Daily doorcodes will work on non-connected doors w/o a sync.                      |
| PERMANENT        | RESIDENT     | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | No doorcodes.<br/><br/>Exposing resident doorcodes is not supported.                                                                                                                                         |
| DAILY            | NON_RESIDENT | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | A unique 7 digit doorcode for each door that works for the entire calendar day set to the timezone of the door.                                                                                              |
| DAILY            | RESIDENT     | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | No doorcodes.<br/><br/>Exposing resident doorcodes is not supported.                                                                                                                                         |
| DAILY_SINGLE_USE | NON_RESIDENT | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | A unique 7 digit doorcode for each door that works for the entire calendar day set to the timezone of the door.<br/><br/>However, it expires 15 minutes after first use.                                     |
| DAILY_SINGLE_USE | RESIDENT     | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | No doorcodes.<br/><br/>Exposing resident doorcodes is not supported.                                                                                                                                         |

Note that the `role` in the request does not bear relevance on the validation.
The `role` field allows clients to classify their understanding of a User's role with respect to a certain Door, but does not imply a certain credential type or shareability.
We currently support two `role`'s: `RESIDENT` and `NON_RESIDENT`.

In the future though, the `role` could be used to determine what credential details the Partner Backend has the ability to see.
For example, a Partner Backend can see credential details for their own `NON_RESIDENT`'s but not for `RESIDENT`'s as that would be a privacy violation.

####  Field descriptions

- `shouldNotify` (default `true`): controls whenever email notifications are sent to the invited user. The emails
  include the welcome email and/or the Doorcode email. If set to `false` **no** emails are sent. In order to prevent
  invalid scenarios validations are put in place to prevent the following request situation since it would lead to the
  user having no way of accessing their Doorcode:
    - `shouldNotify`: `false`
    - `passcodeType`: `DAILY` or `DAILY_SINGLE_USE`
    - `role`: `RESIDENT`

#### Results

1. If the request was successful, the Partner BE will receive an HTTP 200 containing a User object, with the following fields:

    * `email`: Email address associated with the user.
    * `firstName`: First name of the user.
    * `lastName`: Last name of the user.
    * `userUuid`: Unique identifier of the user.
    * `phone`: Phone number of the user. Can be `null`.
    * `accesses`: List of doors the user has access to with the following fields:
      * `doorUuid`: Unique identifier of the door.
      * `passcodeType`: Indicates access type. Possible values are `PERMANENT`, `DAILY`, `DAILY_SINGLE_USE`.
      * `shareable`: Indicates whether user can share access to guests.
      * `startTime`: Start time of access to door.
      * `endTime`: End time of access to door.
      * `granter`: Indicates who granted access to door. Possible values are `PARTNER`, `USER`.
      * `role`: Classifies a type of user. Possible values are `RESIDENT`, `NON_RESIDENT`.
      * `doorcode`: Doorcode object with the following fields:
        * `code`: 7 digit code for guest access to unlock the door. Can be `null`.
        * `description`: A message to explain the code result with the following possible values:
          * `VALID`: Indicates a valid 7 digit doorcode is returned.
          * `COMMUNAL_DOORCODE_CONFLICT`: Indicates a user has permanent access to public doors granted by a property manager in Mission Control in a property with the common doorcodes feature enabled. The `code` field is `null`.
          * `USER_HAS_RESIDENT_ACCESS`: Indicates the user has resident access to the door granted via UserKit or Mission Control. The `code` field is `null`.
          * `USER_HAS_GUEST_ACCESS_CONFLICT`: Indicates the user already has guest permanent access to the door granted via Mission Control. The `code` field is `null`.

   In case of an error, the API will return the following error responses:

    * `400 Bad Request`: missing/invalid parameters or invalid door UUIDs.

      ⇒ Check all the parameters are correct and check all the given doors are valid.
      ⇒ Ensure at least 1 doorUuid is supplied in the request.

    * `401 Unauthorized`: missing or invalid access token.

      ⇒ Check the token hasn't expired and refresh the token if needed.

    * `500 Internal Server Error`: there was an unexpected error.

      ⇒ Contact Latch Support
