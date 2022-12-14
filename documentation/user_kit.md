# User Kit

User Kit allows partners to programmatically grant users access to doors without the need of doing this via Latch Manager Web.

## Door Setup

The first step is for the customer to select which "doors" (as presented in Latch Manager Web) can be eligible to be used programmatically by a given partner. 

After selecting a door, the property manager will select the partner from the dropdown, making it eligible for partners to use it via the APIs.

<table>
<tr><td><img src="assets/user_kit_door_setup.png"></td></tr>
</table>


## Doors API

Partners can fetch a list of all the doors that are enabled for them, by using a partner-scoped token from the BE.

1. GET request from the Partner BE to the Latch BE with an empty body request

	```
	GET https://rest.latchaccess.com/access/sdk/v1/doors
	```

	HTTP Headers
	
	```
	Authorization: Bearer {{access_token}}
	```

	HTTP Request Body
	
	```
	<empty>
	```

	HTTP Response Body
	
	```
	{
	    "doors": [
	      {
	        "uuid": "<string>",
	        "name": "<string>",
	        "type": "DOOR" | "ELEVATOR",
	        "buildingUuid": "<string>"
	      },
	      ...
	    ]
	}
	```


1. If the request was successful, the Partner BE will receive an HTTP 200 with the following fields:

	* `doors`: List of "doors" and their metadata. Each entry will include:
	  * `uuid`: Unique-identifier of the door.
	  * `name`: Name of the door.
	  * `type`: Type of door. Possible values: "DOOR" or "ELEVATOR".
	  * `buildingUuid`: Unique-identifier of the building where the door is located.

	In case of an error, the API will return the following error responses:
	
	* `401 Unauthorized`: missing or invalid access token.

		⇒ Check the token hasn't expired and refresh the token if needed.
		
	* `500 Internal Server Error`: there was an unexpected error.

		⇒ Contact Latch Support

## Users API

### Get users

Partners can fetch a list of their Users. This will be done by using a partner-scoped token from the BE.

1. GET users request from the Partner BE to the Latch BE

	```
	GET https://rest.latchaccess.com/access/sdk/v1/users
	```

	HTTP Headers
	
	```
	Authorization: Bearer {{access_token}}
	```

	HTTP Request Body
	
	```
	<empty>
	```

	HTTP Response Body
	
	```
	{
	    "users": [
	      {
	        "email": "<string>",
	        "firstName": "<string>",
	        "lastName": "<string>",
	        "userUuid": "<string>"
	      },
	      ...
	    ]
	}
	```
	
1. If the request was successful, the Partner BE will receive an HTTP 200 containing a list of User objects, with the following fields:

	* `email`: Email address associated with the user.
	* `firstName`: First name of the user.
	* `lastName`: Last name of the user.
	* `userUuid`: Unique identifier of the user.

	In case of an error, the API will return the following error responses:
	
	* `401 Unauthorized`: missing or invalid access token.

		⇒ Check the token hasn't expired and refresh the token if needed.
		
	* `500 Internal Server Error`: there was an unexpected error.

		⇒ Contact Latch Support

### Create users and grant access

Partners can invite users, without the need of creating them ahead of time, and grant access to a set of doors. This will be done by using a partner-scoped token from the BE.

1. POST from the Partner BE to the Latch BE with the user and door information

	```
	POST https://rest.latchaccess.com/access/sdk/v1/users
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
	    "startTime": "<datetime>",  // e.g. "2022-09-30T15:11:02.537Z"
	    "endTime": "<datetime>",    // e.g. "2022-09-30T15:11:02.537Z"
	    "doorUuids": [
	      "<string>",
	      ...
	    ],
	    "shareable": <boolean>,
	    "passcodeType": "PERMANENT"
	}
	```
	
	HTTP Response Body
	
	```
	{
	    "userUuid": "<string>",
	    "doors": [
	      "uuid": "<string>"
	      "name": "<string>",
	      "type": "DOOR" | "ELEVATOR",
	      "buildingUuid": "<string>",
	    ]
	}
	```

1. If the request was successful, the Partner BE will receive an HTTP 200 with the following fields:

	* `userUuid`: Unique identifier of the invited user and the list of doors the user has access to:
		* `uuid`: Unique-identifier of the door.
		* `name`: Name of the door.
		* `type`: Type of door. Possible values: "DOOR" or "ELEVATOR".
		* `buildingUuid`: Unique-identifier of the building where the door is located.

	In case of an error, the API will return the following error responses:
	
	* `400 Bad Request`: missing parameters or invalid door UUIDs.

		⇒ Check all the parameters are correct and check all the given doors are valid.

	* `401 Unauthorized`: missing or invalid access token.

		⇒ Check the token hasn't expired and refresh the token if needed.

	* `500 Internal Server Error`: there was an unexpected error.

		⇒ Contact Latch Support

### Revoke access

Partners can revoke user access to given doors. This will be done by using a partner-scoped token from the BE.

1. DELETE from the Partner BE to the Latch BE with an empty body request.

	```
	DELETE https://rest.latchaccess.com/access/sdk/v1/users/:user/doors/:door
	```

	HTTP Headers
	
	```
	Authorization: Bearer {{access_token}}
	```

	HTTP Request Body
	
	```
	<empty>
	```

	HTTP Response Body
	
	```
	<empty>
	```

1. If the request was successful, the Partner BE will receive an HTTP 200 and an empty body response. In case of an error, the API will return the following error responses:

    * `404 Not Found`: invalid user or door UUIDs.

        ⇒ Check the user and door identifiers.
		
    * `401 Unauthorized`: missing or invalid access token.

        ⇒ Check the token hasn't expired and refresh the token if needed.
		
    * `500 Internal Server Error`: there was an unexpected error.

        ⇒ Contact Latch Support

## Buildings API

### Get buildings

Partners can fetch a list of their Buildings. This will be done by using a partner-scoped token from the BE.

1. GET request from the Partner BE to the Latch BE with an empty body request

   ```
   GET https://rest.latchaccess.com/access/sdk/v1/buildings
   ```

   HTTP Headers

   ```
   Authorization: Bearer {{access_token}}
   ```

   HTTP Request Body

   ```
   <empty>
   ```

   HTTP Response Body

   ```
   {
       "buildings": [
         {
            "uuid": "<string>",
            "name": "<string>",
            "address": {
              "addressLine1": "<string>",
              "addressLine2": "<string>",
              "addressLine3": "<string>",
              "city": "<string>",
              "country": "<string>",
              "postalCode": "<string>",
              "state": "<string>"
            }
         },
         ...
       ]
   }
   ```


2. If the request was successful, the Partner BE will receive an HTTP 200 with the following fields:

    * `buildings`: List of "buildings" and their metadata. Each entry will include:
        * `uuid`: Unique-identifier of the building.
        * `name`: Name of the building.
        * `address`: Location the building is located.

   In case of an error, the API will return the following error responses:

    * `401 Unauthorized`: missing or invalid access token.

      ⇒ Check the token hasn't expired and refresh the token if needed.

    * `500 Internal Server Error`: there was an unexpected error.

      ⇒ Contact Latch Support