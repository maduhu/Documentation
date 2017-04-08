# Identity Service API

Identity Service methods for managing identity related data, like sessions, images, PINs, etc.

1. **identity.credential.check** - checks credentials and optionally creates a session, returning session id. Credentials can contain one or multiple factors of identification.
	* parameters
    	* user - unique identification of the user
    	* password - password of the user
	* result
    	* session - unique session identification
	* errors
    	* identify.invalidCredention - user or password are invalid
    	* identify.passwordExpired - the password is expired
    	*
1. **identity.credential.add** - add user and password credentials
	* parameters
    	* user - unique identification of the user
    	* password - password of the user
	* result
    	*
	* errors
    	* identify.invalidUser - user is invalid
    	* identify.duplicatedUser - user already exists in the service
    	* identify.passwordPolicy - the password does not meet the criteria of the password policy
 1. **identity.credential.remove** - remove user from the identity service
	* parameters
    	* user - unique identification of the user
	* result
    	*
	* errors
    	* identify.invalidUser - user is invalid or non-existing

1. **identity.password.edit** - changes user password
1. **identity.session.remove** - ends sessions

1. **identity.image.~** - methods for images associated with a user
1. **identity.fingerprint.~** - methods for bio-metric fingerprints
