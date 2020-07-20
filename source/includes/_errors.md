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


## Error Ids

Look for specific error ids here!

Error Id | Name of Error | Error message | Status Code
-------- | ------------- | ------------- | -----------
1 | `request_not_json` | Data type in request is not JSON | 400
2 | `unauthorized` | Authorization has been revoked | 401
3 | `forbidden` | Current user is not allowed to take this action | 403
4 | `invalid_route` | This route is currently not supported | 404
5 | `invalid_email` | This email is already taken or the email address is wrong | 409
6 | `oauth_scopes_unauthorized` | The user did not authorize the use of their email | 409
7 | `invalid_username` | This username is already taken | 409
8 | `invalid_token` | Invalid token provided | 409
9 | `update_failure` | Database document update failed | 400
10 | `deletion_failure` | Database document deletion failed | 400
11 | `validation_error` | Marshmallow validation has failed | 400
12 | `mongodb_get_object_error` | MongoDB document get request has failed | 500
13 | `all_other_error` | This error message corresponds to all other errors | 500
14 | `wrong_login_route` | You cannot attempt login to `/auth/login/` without `user` access field being True | 401