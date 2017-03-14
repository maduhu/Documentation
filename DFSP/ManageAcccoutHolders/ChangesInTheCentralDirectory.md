
### 1. Register a new DFSP in the central directory.

The registration process for a new DFSP should be improved. When a new DFSP is registered the following parameters should be passed:

- Short DFSP Name (needed for the process for changing the default DFSP)
- SPSP Server URL (improvement - instead of passing this URL every time when a new user is added in the central directory, it can be passed upon DFSP registration)


As a response the central directory should generate username/password for the DFSP and send back.

### 2. Get user number information

This API will return the name of the user, the default DFSP and the DFSPs associated with a user number.
Input parameters:

- User Number

Response:

- Name of the User
- Default DFSP SPSP server URL
- Default DFSP name
- Array of names and urls for all DFSPs attached the user number

### 3. Register a new user in the central directory.

Registration process for a new user should be improved. There's no need to pass every time the SPSP server URL address, because the DFSP authenticate and the central directory should already has that information provided by the previous API.

There should be the following input parameters to the request:

- Name of the the user

As a response the central directory should generate a user number and send it back.

### 4. Add DFSP to user number

Central directory should keep information about all the DFSPs that a user number is registered with, not only the default DFSP for a user number. We need this information for changing default DFSP use case.


There should be an option to add DFSP to a user number. Only the authenticated DFSP can send a request do be added to a user number.
There shouldn't be any input parameters to this API. As a response there should be confirmation or error.


### 5. Remove DFSP from user number

There should be an option for removing the DFSP from a user number.

As input parameter the API should expect:

- User Number
- DFSP name that should be removed

Note - central directory will not allow a default DFSP to be removed from a user number.

As a response there should be confirmation or an error message.

### 6. Make a DFSP the default one for a user number

There should be an API that can make a DFSP default. Only the DFSP that is currently a default one for a user number should be able to do a change.
As input parameter the API should expect:

- User Number
- DFSP name that should become the default


As a response there should be confirmation or an error message.