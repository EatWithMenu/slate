# Models

## Category

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
name | `StringField(required=True)` | String | name of category | **Required**
description | `StringField(required=True)` | String | description of category | **Required**
tags | `ListField(StringField())` | Array of String | tags associated with category. |
items | `ListField(ReferenceField(Item))` | Array of ObjectID | array of references to `Item` model. |

## Menu Item

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
_id | `StringField(primary_key=True, required=True)` | String of length 36 | MongoDB Primary Key hash, must be length 36 alphanumeric| **Required**
location | `StringField(required=True)` | String of length 36 | reference to `Restaurant` model. | **Required**
name | `StringField(required=True)` | String | name of menu item. |**Required**
desc | `StringField()` | String | description of menu item. |
price | `FloatField(required=True)` | Float | price of menu item. |**Required**
image_url | `StringField()` | String | url that holds menu item image. |
tags | `ListField(StringField())` | Array of String | tags associated with menu item. |

## Restaurant

### Restaurant model

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
_id | `_id = StringField(primary_key=True, required=True)` | String | MongoDB Primary Key hash, must be length 36 alphanumeric| **Required**
name | `StringField(required=True)` | String | name of restaurant | **Required**
link | `StringField()` | String | link to restaurant info |
rating | `StringField()` | String | rating of restaurant |
attrs | `EmbeddedDocumentField(Attributes)` | Attribute model | attributes of restaurant |
dollarsigncount | `IntField()` | Integer | dollar sign indicating price of restaurant |
cuisine | `StringField()` | String | type of cuisine |
revcount | `IntField()` | Integer |  |
loc | `StringField()` | String | restaurant address |
menudict | `ListField(StringField())` | Array of String (length 36) | object ids of menu item that the restaurant serves |

### Attributes model

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
piclink | `StringField()` | String | url of restaurant picture |
addr | `StringField()` | String | restaurant address |
cuisines | `StringField()` | String | type of cuisine |
diningstyle | `StringField()` | String | dining style of cuisine |
dresscode | `StringField()` | String | restaurant dress code |
additional | `StringField()` | String | additional info of restaurant |
website | `StringField()` | String | restaurant website link |


## Review

### Review model

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
location | `StringField(required=True)` | String of length 36| MongoDb object id of restaurant model | **Required**
name | `StringField(required=True)` | String | reviewer name | **Required**
city | `StringField(required=True)` | String | city location name | **Required**
overallrating | `IntField()` | Integer | overall rating for this review |
subratings | `EmbeddedDocumentField(Subrating)` | Subrating model | subrating information for this review
text | `StringField()` | String | review text |

### Subrating model

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
overall | `IntField()` | Integer | overall rating |
food | `IntField()` | Integer | food rating | 
service | `IntField()` | Integer | service rating |
ambience | `IntField()` | Integer | ambience rating |


## User

### User model

### - User info

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
username | `StringField(required=True)` | String | first and last name of user | **Required**
password | `BinaryField(required=True)` | Binary | encrypted account password | **Required**
email | `EmailField(required=True, unique=True)` | Email field | account email | **Required**
access | `EmbeddedDocumentField(Access)` | Access model | refer to `Access` model |
phone | `PhoneField()` | Phone field | account phone number |

### - Restrictions

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
restrictions | `ListField()` | Array of String | list of restrictions | 

### - Pref levels

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
preferences | `DictField(default = {})` | Dictionary (key = string, value = integer) | key-value pair of user preferences | 
cluster_group_prefs | `DictField(default = {})` | Dictionary (key = string, value = integer) | key-value pair of cluster_group_prefs (https://docs.google.com/spreadsheets/d/108FCNhgjhSnSmJ-OyNcjUzSwVw2f7l6cF6dU--GoR9M/edit#gid=0)|
processed_prefs | `DynamicField()` | Array of String | cached preferences after being processed by dsci |

### - Other

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
created_on | `DateTimeField(default = datetime.now)` | DateTime field | indicates when this account was generated |
fav_restaurant | `StringField()` | String of length 36| reference to restaurant model |


### Access model

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
user | `BooleanField(default=False)` | Boolean | is this account "user" level? |
admin | `BooleanField(default=False)` | Boolean | is this account "admin" level? |
googlelogin | `BooleanField(default=False)` | Boolean | is this account "googlelogin" level? |
facebooklogin | `BooleanField(default=False)` | Boolean | is this account "facebooklogin" level? |
guest | `BooleanField(default=False)` | Boolean | is this account "guest" level? |