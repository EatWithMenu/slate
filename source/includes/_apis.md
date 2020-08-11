
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
data = {"password": "example_password", "username_or_email": "example@example.com" or "example"}

response = requests.post("https://www.menubackend.com/auth/login/", json=data)
print(response.text)
```
>{
    "result": {
        "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciO...",
        "logged_in_as": "example_username",
        "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciO...",
        "user_id": "5f0fb5503cb6921b32c6b...",
    }
}

The native login API takes a POST request containing the user's (username or email) and (password). If successful, returns a JSON response containing a newly created access token and refresh token. If the user has not already registered (i.e., is not on the database), tokens will not be issued and an [error](#errors) response is returned. Again requires the `Content-Type: application/json` header.

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
        "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGciO...",
        "user_id": "5f0fb5503cb6921b32c6b...",
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

### GET to /categories/
```python
headers = {'Authorization': 'Bearer insert_access_token_here'}

response = requests.get("https://www.menubackend.com/category/", headers=headers) 
print(response.text)
```
>{'result': ['array of all categories']}

To obtain all the categories in the database, send a get request to this endpoint with an access token in the header.

## Add a categories

### POST to /categories/
```python
headers = {'Authorization': 'Bearer insert_access_token_here'}

data = {"name": "category_name", "description": "category_description", "items":["array of references to item model"]}# Must be admin user token
response = requests.post("https://www.menubackend.com/category/", headers=headers, json=data) 
print(response.text)
```
>{'result': {'id': 'category id'}

To add a category to the database, send a post request to this endpoint with the access token **from a user with admin priviledges** (admin = true in the access field of the [user](#user) model). If added successfully, returns the category id.

## Get a specific category

### GET to `/category/<category_id>/`
```python
headers = {'Authorization': 'Bearer insert_access_token_here'}

response = requests.post("https://www.menubackend.com/category/", headers=headers) 
print(response.text)
```
>{'result': {"_id": {"$oid": "category_id"}, "description": "category_description", "items": [], "tags":[], "name":"category_name"}}

To obtain a specific category, send a get request to this endpoint with an access token in the header and category id in the URL.

## Update a specific category

### PATCH to `/category/<category_id>/`
```python
headers = {'Authorization': 'Bearer insert_access_token_here'} # Must be admin user token

data = {"description": "category description update"}
response = requests.post("https://www.menubackend.com/category/category_id", headers=headers, json=data) 
print(response.text)
```
>{'result': {"_id": {"$oid": "category_id"}, "description": "category description update", "items": [], "tags":[], "name":"category_name"}}

To update a specific category, send a patch request to this endpoint with an **admin** access token in the header and category id in the URL, and the updated category fields in the body.

## Delete a specific category

### DELETE to `/category/<category_id>/`
```python
headers = {'Authorization': 'Bearer insert_access_token_here'} # Must be admin user token

response = requests.delete("https://www.menubackend.com/category/category_id", headers=headers) 
print(response.text)
```
>{'result': {"_id": {"$oid": "category_id"}, "description": "category description update", "items": [], "tags":[], "name":"category_name"}}

To delete a specific category, send a delete request to this endpoint with an **admin** access token in the header and category id in the URL, and the updated category fields in the body.

# Menu Item API

The Item API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\items\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all items in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new menu item on the database, where the POST body contains all the requireds fields of the [menu item](#menu-item) model.

To the `\item\<item_id>\` endpoint, we support:

* GET request (with a valid access token in the header), which returns the specific item with `item_id` in the database.
* PATCH request (with an **admin** access token in the header), which updates the item with `item_id` in the database with the request's body contents.
* DELETE request (with an **admin** access token in the header), which deletes the item with `item_id` in the database.

# Restaurant API

The Restaurant API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\restaurants\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all restaurants in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new restaurant on the database, where the POST body contains all the requireds fields of the [restaurant](#restaurant) model.

To the `\restaurant\<restaurant_id>\` endpoint, we support:

* GET request (with a valid access token in the header), which returns the specific restaurant with `restaurant_id` in the database.
* PATCH request (with an **admin** access token in the header), which updates the restaurant with `restaurant_id` in the database with the request's body contents.
* DELETE request (with an **admin** access token in the header), which deletes the restaurant with `restaurant_id` in the database.

# Review API

The Review API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\reviews\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all reviews in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new review on the database, where the POST body contains all the requireds fields of the [review](#review) model.

To the `\review\<review_id>\` endpoint, we support:

* GET request (with a valid access token in the header), which returns the specific review with `review_id` in the database.
* PATCH request (with an **admin** access token in the header), which updates the review with `review_id` in the database with the request's body contents.
* DELETE request (with an **admin** access token in the header), which deletes the review with `review_id` in the database.

To the `\review\<restaurant_id>\` endpoint, we support:

* GET request (with a valid access token in the header), which returns all reviews associated with `restaurant_id` in the database

To the `\review\<restaurant_id>\<max_number_of_reviews>\` endpoint, we support:

* GET request (with a valid access token in the header), which returns maximum of max_number_of_reviews number of reviews associated with the restaurant. You do not have to worry whether the number you request exceeds the actual number of reviews present. For instance, if you request 1000 reviews for a restaurant that currently has 600 reviews, this endpoint will return 600 reviews present.


# User API

The User API provides basic CRUD functionality which is very similar to the other CRUD APIs in our system.
To the `\users\` endpoint, we support:

* GET request (with an **admin** access token in the header), which returns all users in the database.
* POST request (with an **admin** access token in the header), which allows creation of a new user on the database, where the POST body contains all the requireds fields of the [user](#user) model.

To the `\user\<user_id>\` endpoint, we support:

* GET request (with an **admin** access token in the header, or with the jwt id of access token matching with `<user_id>` in the request param), which returns the specific user with `user_id` in the database.
* PATCH request (with an **admin** access token in the header), which updates the user with `user_id` in the database with the request's body contents. Frontend can send us `cluster_group_prefs` dictionary in the body json. For instance, `{"Desserts" : 3, "Bar": 7"}` (case insensitive string keys). PATCH request will internally map "Desserts" and "Bar" to corresponding cluster label number and store `{"2" : 3, "22": 3, "35": 3, "27": 7, "30": 7, "32": 7}` , since "Desserts" has three label numbers: 2, 22, 35, and "Bar" has three label numbers: 27, 30, 32.
After this conversion, backend endpoint calls the DSCI usrprefgen/ POST request to update the user preferences.
* DELETE request (with an **admin** access token in the header), which deletes the user with `user_id` in the database.


# Search API

### POST to /search/

The Search API provides one endpoint supporting a post request to handle search queries.
To the `\search\` endpoint, send a POST request (with a logged in user's access token in the header), where POST body contains the following:

* (Required) `page`: An integer to represent which page the user has clicked on. The first page is page 0. This is required.
    * Example: `0`, `1`, `2`, `3`...
* (Optional) `user_coords`: a two-length list of latitude and longitude coordinatres from the user's location. This is optional, and if not provided the `loc` and `locscore` fields will be empty. 
    * Example: `[29.72154017, -95.39608383]`
* (Optional) `search_string`: Any string that the user inputs from the search bar. This is supposed to be either a cuisine, restaurant name, cluster name, or food name. This is optional, and if not provided the return will just be the highest rated items for the user.
    * Example: `chinese`, `burgers`, `chicken`, `pizza`
* (Optional) `submenus`: a list of submenus that the user selects. Can be any combination of strings from the list of all possible submenus (see right). Should either be 'all' or a list of other strings. 'all' automatically includes all other submenus, so including other submenus with it is redundant. Adding delivery or takeout means only restaurants with delivery or takeout's menu items are considered.
    * Example: `['all']`, `['delivery', 'dinner']`
    * All possible submenu filters: `[main, takeout, delivery, dessert, dinner, lunch, breakfast, pizza, sushi, appetizers, kids, brunch]`

Errors: In the case that `page` parameter is not included in the POST request body, returns [error id 15](#error-ids). If the `processed_prefs` field of the [user](#user) is an empty list on the database, meaning a PATCH request was never sent to the [/user/<user_id>/](#user-api) endpoint following user registration, returns [error id 16](#error-ids).

Response: The response is a list where each recommended restaurant is represented by a dictionary with 6 keys:

* `restaurant_object`: A dictionary containing all of the fields of the [restaurant](#restaurant) model with the exception of `menudict`.
* `items`: A dictionary where the keys are menu item's string names, and the values are dictionaries of the form {"score": 0.23158002514783957, "rev": 24}, where "score" is the item's recommendation score.
* `item_sum_score`: The sum of item scores for that restaurant.
* `rev_sum_score`: The sum of how the items were perceived in the reviews.
* `loc`: Distance in miles of the restaurant from the user (0 if `user_coords` is not provided above).
* `locscore`: Miles of location converted to a score (0 if `user_coords` is not provided above).


```python
headers = {'Authorization': 'Bearer insert_access_token_here'}

data = {"page": 0, "search_string": "breakfast", "submenus":["breakfast"], "user_coords": [29.72154017, -95.39608383]}

response = requests.post("https://www.menubackend.com/search/", headers=headers, json=data) 
print(response.text)
```
>[
    {
        "restaurant_object": {
            "_id": "ce64dcbe-5399-4be7-8f4b-88dc01b022b1",
            "name": "The Original Ninfa's - Uptown Houston",
            "link": "https://www.opentable.com/r/the-original-ninfas-uptown-houston?avt=eyJ2IjoxLCJtIjoxLCJwIjowfQ&corrid=f458c3a2-b079-46d0-a159-e58fae678ba4",
            "rating": "4.2",
            "attrs": {
                "piclink": "https://images.otstatic.com/prod/26414663/4/medium.jpg",
                "addr": "1700 Post Oak Blvd Houston, TX  77056",
                "diningstyle": "Casual Dining",
                "dresscode": "Casual Dress",
                "additional": "Beer, Cocktails, Delivery, Full Bar, Outdoor dining, Private Room, Takeout, Weekend Brunch, Wine",
                "website": "http://www.ninfas.com/"
            },
            "dollarsigncount": 2,
            "cuisine": "Mexican",
            "revcount": 175,
            "loc": "Galleria / Uptown"
        },
        "items": {
            "Bean, Egg, Cheese": {
                "score": 0.33183933558814865,
                "rev": 30
            },
            "Fajita, Egg, Cheese": {
                "score": 0.2749612585690266,
                "rev": 24
            },
            "Chorizo, Egg, Cheese": {
                "score": 0.23158002514783957,
                "rev": 24
            },
            "Potato, Egg, Cheese": {
                "score": 0.22243416722471326,
                "rev": 24
            },
            "Bacon, Egg, Cheese": {
                "score": 0.20954943656571773,
                "rev": 24
            }
        },
        "item_sum_score": 0.26302694106198093,
        "rev_sum_score": 0.6363636363636364,
        "loc": 4.408288309118191,
        "locscore": 0.04665314401622718
    },
   ...
]



# Interested API

### POST to /interested/

This endpoint is specifically for storing beta-user-request email info into mongodb collection, to later send out emails on updates to our product
The Interested API supports only one request: POST. Use the POST request for /interested/ endpoint to store an email address and a default timestamp to 'interested' collection. The signup_time field will default to the request time.

```python
data = {"email": <email address>}

response = requests.post("https://www.menubackend.com/interested/", json=data) 
```

>{'id': <"mongoDB document id">}

