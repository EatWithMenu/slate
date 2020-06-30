
# Authentication API

## Registration / Sign-up

### POST to /auth/signup/

```python
import requests

# User data includes all required fields of User model:
data = {"username": "example_username", "password": "example_password", "email": "example@example.com", "phone": "111-111-1111", "access": {"user": "false", "admin": "true"}, "vegetarian": "true", "keto": "true", "spice_pref": "4"}

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

After login, the frontend user possesses an **access token** and a **refresh token**. When logging out, the backend needs to revoke both of these tokens so they are no longer viable. To do this, the logout process provides two endpoints: `/auth/logout/` and `/auth/logout2/`, which revoke the active access token and refresh token, respectively. The POST body can be empty, but `/auth/logout/` requires the access token header `Authorization: Bearer insert_access_token_here`, and `/auth/logout2/` requires the refresh token header `Authorization: Bearer insert_refresh_token_here`. Upon success, a success message is returned in both cases.

## Manage refresh token

## Forgot password

## Reset password

# Category API

# Menu Item API

# Query API

# Restaurant API

# Review API

# User API

