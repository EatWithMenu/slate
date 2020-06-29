# Errors

Menu Backend API uses the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is invalid. Could be: `Request not json`, `Update failure`, or `Deletion failure`.
401 | Unauthorized -- Authorization has been refused for the given credentials
403 | Forbidden -- The current user is not authorized to take this action.
404 | Not Found -- This route is currently not supported.
409 | Something is Invalid - Could be: `This email is already taken or the email address is wrong`, `The user did not authorize the use of their email`, `This username is already taken`, or `Invalid token provided`.
500 | Internal Server Error -- We had a problem with our server.