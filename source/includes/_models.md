# Models

## Category

Field Name | Code | Type | Description | Required
---------- | ---- | ---- | ----------- | --------
name | StringField(required=True) | String | name of category | **Required**
description | StringField(required=True) | String | description of category | **Required**
tags | ListField(StringField()) | Array of String | tags associated with category. |
items | ListField(ReferenceField(Item)) | Array of ObjectID | array of references to `Item` model. |

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

### Attributes model embedded inside Restaurant model
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

## User