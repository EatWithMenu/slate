
# Authentication API

## Registration / Sign-up

### POST to /auth/signup/

```python
import requests

# User data includes all required fields of User model:
data = {"username": "example_username", "password": "example_password", "email": "example@example.com", "phone": "111-111-1111", "access": {"user": "true", "admin": "false"},  "restrictions": ["vegetarian", "vegan"], "preferences": {"Sushi": 10, "Asian": 10, "Latin American": 1}}

response = requests.post("https://www.menubackend.com/auth/signup/", json=data) # json=data automatically handles Content-Type header
print(response.text)
```
>{
    "result": {
        "id": "5efa368ac5fab88355ffa9c0"
    }
}

Registering a user is performed by sending a POST request to the endpoint at `/auth/signup/`. The body of the POST includes user information, including all required fields and any optional fields in the [user](#user) model. The header must include `Content-Type: application/json`. If the username or email is already taken, returns an [error](#errors) response. Upon success, the user's id is returned.

## Login

### POST to /auth/login/ -- Native login

```python

# Native user's password and email:
data = {"password": "example_password", "email": "example@example.com"}

response = requests.post("https://www.menubackend.com/auth/login/", json=data)
print(response.text)
```
>{
    "result": {
        "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciO...",
        "logged_in_as": "example_username",
        "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciO..."
    }
}

The native login API takes a POST request containing the user's email and password. If successful, returns a JSON response containing a newly created access token and refresh token. If the user has not already registered (i.e., is not on the database), tokens will not be issued and an [error](#errors) response is returned. Again requires the `Content-Type: application/json` header.

### POST to /auth/login/google/ -- Google login

```python
# Google user's ID token obtained from Google's login API:
data = {"id_token": "insert_google_id_token_here"}

response = requests.post("https://www.menubackend.com/auth/login/google/", json=data) 
print(response.text)
```
>{
    "result": {
        "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciO...",
        "logged_in_as": "example_username",
        "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciO..."
    }
}

Google login takes a POST request containing the user's id token obtained from Google's login API. If successful, returns a JSON response containing a newly created access token and refresh token. If the user has not already registered (i.e., is not on the database), tokens will not be issued and an [error](#errors) response is returned. Again requires the `Content-Type: application/json` header.

A successful login attempt provides the user with a valid **access token** and **refresh token**. As its name suggests, the access token is used to *access* the backend's protected endpoints. However, the access token has a very limited lifespan of a few mins. Once the access token expires, the refresh token, which has a much longer lifespan of several hours, can be used to obtain a new access token using the [refresh](#manage-refresh-token) endpoint.

## Logout

```python
# First revoke the current access token:
data = {} # Empty POST body
headers = {'Authorization': 'Bearer insert_access_token_here'}
response = requests.post("https://www.menubackend.com/auth/logout/", headers=headers, json=data) 
print(response.text)
```
>{
    "result": "Logout successful. Access token successfully revoked."
}

```python
# Next revoke the current refresh token:
data = {} # Empty POST body
headers = {'Authorization': 'Bearer insert_refresh_token_here'}
response = requests.post("https://www.menubackend.com/auth/logout2/", headers=headers, json=data) 
print(response.text)
```
>{
    "result": "Logout successful. Refresh token successfully revoked."
}

### POST to /auth/logout/ and /auth/logout2/

After login, the frontend user possesses an **access token** and a **refresh token**. When logging out, the backend needs to revoke both of these tokens so they are no longer viable. To do this, the logout process provides two endpoints: `/auth/logout/` and `/auth/logout2/`, which revoke the active access token and refresh token, respectively. The POST body can be empty, but `/auth/logout/` requires the access token header `Authorization: Bearer insert_access_token_here`, and `/auth/logout2/` requires the refresh token header `Authorization: Bearer insert_refresh_token_here`. Upon success, a success message is returned in both cases.

## Manage refresh token

### POST to /auth/refresh/

```python
# Obtain a new valid access token using a valid refresh token:
data = {} # Empty POST body
headers = {'Authorization': 'Bearer insert_refresh_token_here'}
response = requests.post("https://www.menubackend.com/auth/refresh/", headers=headers, json=data) 
print(response.text)
```
>{
    "result": {"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciO..."}
}

Sending a POST to this endpoint with a valid refresh token in the headers (`Authorization: Bearer insert_refresh_token_here`) issues a new valid access token.

## Forgot password

### POST to /auth/forgot

```python
data = {"email": "example@example.com"} # email in the post body
response = requests.post("https://www.menubackend.com/auth/forgot/", json=data) 
print(response.text)
```
>{'result': 'An email has been sent if this is a valid Menu email.'}

**[This endpoint is under construction. We need to set up a menu email first]** Sending a POST to this endpoint with a user's email in the body will send an email to that address with a token in the email body. This token will be included in the body of the post request to the [reset](#reset-password) endpoint.

## Reset password

### POST to /auth/reset/
```python
data = {"reset_token": "insert_reset_token_from_email_here", "password": "new_password"} # reset token obtained from the email sent out by forgot endpoint
response = requests.post("https://www.menubackend.com/auth/forgot/", json=data) 
print(response.text)
```
>{'result': 'Successfully reset password.'}

**[This endpoint works with /auth/forgot/, so is still under construction.** Sending a POST to this endpoint with the reset token in the request body as well as the user's new password. The endpoint will update the user's password and send a confirmation email to them that the password has been changed.

# Query API
```python
data = { "name": "Avalon Diner", "addr": "12810 Southwest Fwy. Stafford, TX 77477"} # query the database for restaurant named Avalon diner at that address
response = requests.post("https://www.menubackend.com/query/restaurant/", json=data) 
print(response.text)
```
> {
        "result": [
            {
                "_id": ...
                "addr": "12810 Southwest Fwy. Stafford, TX 77477"
                ... etc ...
            }
        ]
    }

Resource for querying the mongoDB Restaurant collection from an HTTP request. Post request must contain header Content-Type: application/json and the body must be a JSON query. Response is a JSON with one key -- "result" whose corresponding value is a JSON List containing JSON dicts for every restuarant matching the query. If the JSON List is empty, nothing in the database matched the query.

Analagous post requests can be sent to the `/query/item/` endpoint to query the database for menu items.

# Category API

## Get all categories

### GET to /category/
```python
headers = {'Authorization': 'Bearer insert_access_token_here'}

response = requests.get("https://www.menubackend.com/category/", headers=headers) 
print(response.text)
```
>{'result': ['array of all categories']}

To obtain all the categories in the database, send a get request to this endpoint with an access token in the header.

## Add a categories

### POST to /category/
```python
headers = {'Authorization': 'Bearer insert_access_token_here'}

data = {"name": "category_name", "description": "category_description", "items":["array of references to item model"]}# Must be admin user token
response = requests.post("https://www.menubackend.com/category/", headers=headers, json=data) 
print(response.text)
```
>{'result': {'id': 'category id'}

To add a category to the database, send a post request to this endpoint with the access token **from a user with admin priviledges** (admin = true in the access field of the [user](#user) model). If added successfully, returns the category id.

## Get a specific category

### GET to /category/category_id/
```python
headers = {'Authorization': 'Bearer insert_access_token_here'}

response = requests.post("https://www.menubackend.com/category/", headers=headers) 
print(response.text)
```
>{'result': {"_id": {"$oid": "category_id"}, "description": "category_description", "items": [], "tags":[], "name":"category_name"}}

To obtain a specific category, send a get request to this endpoint with an access token in the header and category id in the URL.

## Update a specific category

### PUT to /category/category_id/
```python
headers = {'Authorization': 'Bearer insert_access_token_here'} # Must be admin user token

data = {"description": "category description update"}
response = requests.post("https://www.menubackend.com/category/category_id", headers=headers, json=data) 
print(response.text)
```
>{'result': {"_id": {"$oid": "category_id"}, "description": "category description update", "items": [], "tags":[], "name":"category_name"}}

To update a specific category, send a put request to this endpoint with an **admin** access token in the header and category id in the URL, and the updated category fields in the body.

## Delete a specific category

### DELETE to /category/category_id/
```python
headers = {'Authorization': 'Bearer insert_access_token_here'} # Must be admin user token

response = requests.delete("https://www.menubackend.com/category/category_id", headers=headers) 
print(response.text)
```
>{'result': {"_id": {"$oid": "category_id"}, "description": "category description update", "items": [], "tags":[], "name":"category_name"}}

To delete a specific category, send a delete request to this endpoint with an **admin** access token in the header and category id in the URL, and the updated category fields in the body.

# Menu Item API

The Item API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\item\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all items in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new menu item on the database, where the POST body contains all the requireds fields of the [menu item](#menu-item) model.

To the `\item\item_id\` endpoint, we support:

* GET request (with a valid access token in the header), which returns the specific item with `item_id` in the database.
* PUT request (with an **admin** access token in the header), which updates the item with `item_id` in the database with the request's body contents.
* DELETE request (with an **admin** access token in the header), which deletes the item with `item_id` in the database.

# Restaurant API

The Restaurant API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\restaurant\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all restaurants in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new restaurant on the database, where the POST body contains all the requireds fields of the [restaurant](#restaurant) model.

To the `\restaurant\restaurant_id\` endpoint, we support:

* GET request (with a valid access token in the header), which returns the specific restaurant with `restaurant_id` in the database.
* PUT request (with an **admin** access token in the header), which updates the restaurant with `restaurant_id` in the database with the request's body contents.
* DELETE request (with an **admin** access token in the header), which deletes the restaurant with `restaurant_id` in the database.

# Review API

The Review API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\review\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all reviews in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new review on the database, where the POST body contains all the requireds fields of the [review](#review) model.

To the `\review\review_id\` endpoint, we support:

* GET request (with a valid access token in the header), which returns the specific review with `review_id` in the database.
* PUT request (with an **admin** access token in the header), which updates the review with `review_id` in the database with the request's body contents.
* DELETE request (with an **admin** access token in the header), which deletes the review with `review_id` in the database.


# User API

The User API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\user\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all users in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new user on the database, where the POST body contains all the requireds fields of the [user](#user) model.

To the `\user\user_id\` endpoint, we support:

* GET request (with a valid access token in the header), which returns the specific user with `user_id` in the database.
* PUT request (with an **admin** access token in the header), which updates the user with `user_id` in the database with the request's body contents.
* DELETE request (with an **admin** access token in the header), which deletes the user with `user_id` in the database.

