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
location | `ReferenceField('Restaurant', required=True)` | ObjectID | reference to `Restaurant` model. | **Required**
name | `StringField(required=True)` | String | name of menu item. |**Required**
desc | `StringField()` | String | description of menu item. |
price | `FloatField(required=True)` | Float | price of menu item. |**Required**
image_url | `StringField()` | String | url that holds menu item image. |
tags | `ListField(StringField())` | Array of String | tags associated with menu item. |

## Restaurant

### Restaurant model

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
_id | `ObjectIdField(primary_key=True)` | ObjectID | MongoDB Id of restaurant | **Required**
name | `StringField(required=True)` | String | name of restaurant | **Required**
link | `StringField()` | String | link to restaurant info |
rating | `StringField()` | String | rating of restaurant |
attrs | `EmbeddedDocumentField(Attributes)` | Attribute model | attributes of restaurant |
dollarsigncount | `IntField()` | Integer | dollar sign indicating price of restaurant |
cuisine | `StringField()` | String | type of cuisine |
revcount | `IntField()` | Integer |  |
loc | `StringField()` | String | restaurant address |
menudict | `ListField(ReferenceField(Item))` | Array of ObjectID | object ids of menu item that the restaurant serves |

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
location | `ReferenceField('Restaurant', required=True)` | ObjectID | MongoDb object id of restaurant model | **Required**
name | `StringField(required=True)` | String | reviewer name |
city | `StringField(required=True)` | String | city location name |
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
access | `EmbeddedDocumentField(Access, required=True)` | Access model | refer to `Access` model | **Required**
phone | `PhoneField()` | Phone field | account phone number |

### - Restrictions

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
vegetarian | `BooleanField(default=False)` | Boolean | is vegetarian? |
keto | `BooleanField(default=False)` | Boolean | is keto? |

### - Pref levels

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
spice_pref | `IntField(default = 5)` | Integer | preference for spice | 
chinese_pref | `IntField(default = 5)` | Integer | preference for chinese |
finedine_pref | `IntField(default = 5)` | Integer | preference for fine dining | 

### - Other

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
created_on | `DateTimeField(default = datetime.now)` | DateTime field | indicating when this account is generated
fav_restaurant | `ReferenceField(Restaurant)` | Object ID | reference to restaurant model |


### Access model

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
user | `BooleanField(default=True)` | Boolean | is this account "user" level? |
admin | `BooleanField(default=False)` | Boolean | is this account "admin" level? |
