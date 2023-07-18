# Share Kit

Share Kit allows Partners and their End Users the ability to share access to Guests (non-Residents) via both a Machine to Machine Server Integration or via the Latch SDK inside of a Partner App.

It is important to note that when a  Resident loses access, so will all of their dependent Guests.

## Users API

Partners can leverage the existing Users API to share access to Guests via their Machine to Machine application.
See: https://github.com/Latch/openkit/blob/main/documentation/user_kit.md#users-api.

## Latch SDK Guests

In order for Partners to allow End Users to share access via their Partner App, the following Latch SDK functions can be used.

### Invite Guests
Guests can be invited via the SDK's `inviteGuests()` function:
```
LatchSdk.inviteGuests(
  firstName: String,
  lastName:  String,   //Optional
  email:     String,
  phone:     String,   //Optional
  startTime: DateTime, 
  endTime:   DateTime, //Optional
  deviceUuids: [String], //UUID
  passcodeType: Enum,  // Permanent, Daily, DailySingleUse
): InviteGuestsResult
```

Validation is the same as the Invite User API:

| Passcode Type    | Type of Credential | Credential Details                                                                                                                                                    | Validation Notes                                                                                                                                                                                                                    | Downstream Notifications                                                                                                                                                                                                                                                        |
|------------------|--------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PERMANENT        | Mobile Access      | Access via Latch Consumer App or Partner App                                                                                                                          | Email Required, Phone Optional                                                                                                                                                                                                      | Latch will send Guest a Latch Email Invite.                                                                                                                                                                                                                                     |
| DAILY            | Doorcode           | 7 digit doorcode that works for the entire calendar day set to the timezone of the device.End time from request is not used.                                          | Either email or phone required (not both). Start time must be either on the day of the request or the next day. No start time further in advance will be allowed. Exact start time on the day not honored. Shareable must be false. | If email is provided, Latch will email the doorcode. If phone is provided, Latch will text the doorcode unless an existing User is found with a matching phone number. If a User with that phone number is found and has an email address, Latch will send an email not a text. |
| DAILY_SINGLE_USE | Doorcode           | 7 digit doorcode that works for the entire calendar day set to the timezone of the device, but expires 15 minutes after first use. End time from request is not used. | Either email or phone required (not both). Start time must be either on the day of the request or the next day. No start time further in advance will be allowed. Shareable must be false.                                          | If email is provided, Latch will email the doorcode. If phone is provided, Latch will text the doorcode unless an existing User is found with a matching phone number. If a User with that phone number is found and has an email address, Latch will send an email not a text. |

InviteGuestsResult:

| Name                                     | Description                                                                                                                        |
|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Success (guest: Guest)                   | Invite succeeded with Guest information inside .guest                                                                              |
| Uninitialized                            | SDK is not initialized                                                                                                             |
| NetworkError (reason: InviteErrorReason) | Network Error with error reason being one of:“Email is required”, “Email or phone is required”, “Start time too far in the future” |
| InternalError                            | All other errors                                                                                                                   |


### Fetch Guests
Guests for a given End User can be fetched  via the SDK's `guests()` function:

```
LatchSdk.guests(
): GuestsResult

```

GuestsResult:

| Name                                     | Description                                                                                                                        |
|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Success (guests: [Guest])                | All guests information wrapped inside .guests                                                                                      |
| Uninitialized                            | SDK is not initialized                                                                                                             |
| NetworkError (reason: InviteErrorReason) | Network Error with error reason being one of:“Email is required”, “Email or phone is required”, “Start time too far in the future” |
| InternalError                            | All other errors                                                                                                                   |

