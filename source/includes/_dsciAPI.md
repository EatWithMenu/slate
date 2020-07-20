
# DSCI API

**Note:** Frontend should NOT call this directly; you should call backend's endpoint which then calls these.

## Reviews

### POST to /usefulreviewsgen/

```python
# Get useful reviews
rhash = '0b7f3d99-13ce-4d45-955e-71902c4cb9a4'
response = requests.post(awsurl + 'usefulreviewsgen/', json = {'rhash':rhash})
newdict = ast.literal_eval(response.text.replace('Infinity', '-1').replace('false', 'False').replace('null','None'))
```
>[
    {
        'name': 'RevrendDr',
        'city': 'Kansas City',
        'text': 'The mushroom risotto was delicious, as was the lobster bisque.  Also had the lamb shank, which was nice and tender.  The white sangria on special every Saturday night was also a big hit.  Our waitress was sweet and attentive, if a little new.  Will definitely go back for another visit.',
        'overallrating': '5',
        'subratings':
            {
                'Overall': '5', 
                'food': '5',
                'service': '4',
                'ambience': '4'
            }
   }, ... # Other reviews left out for clarity
]

Getting useful reviews is performed by sending a POST request to the endpoint at `/usefulreviewsgen/`. The body of the POST includes `rhash`. The header must include `Content-Type: application/json`. Upon success, a list of dictionaries representing reviews is returned.

**Inputs**

* `rhash`: the restaurant hash

**Outputs**

* List of dictionaries, each one containing:
    * `name` : `<name of the reviewer>`
    * `city` : `<city the reviewer is from (i think)>`
    * `text` : `<review text>`
    * `overallrating` : `<rating of the review>`
    * `subratings` : `<dict of subratings the user gave the restaurant>`


## User preference

### POST to /usrprefgen/

```python
# Generate user preferences
prefdict = {'Sushi': 10, 'Asian': 6, 'Latin Ameridfasdfcan': 10}
test_cluster_group_prefs = {'0': 1, '2': 2, '3': 3, '6':2}
response = requests.post(awsurl + 'usrprefgen/', json = {'prefscu':prefdict, 'prefscl': test_cluster_group_prefs})
utdl = ast.literal_eval(response.text.replace('Infinity', '-1').replace('false', 'False').replace('null','None'))
```
>[
    {'key': ['piece', 'succulent'], 'value': {'score': 0.0039, 'n': 1.0}},
    {'key': ['mole', 'annie'], 'value': {'score': 0.0039, 'n': 1.0}},
    {'key': ['crawfish', 'étouffée'], 'value': {'score': 0.0039, 'n': 1.0}},
    {'key': ['pickle', 'lodge'], 'value': {'score': 0.0039, 'n': 1.0}}
]

Takes a POST request optionally containing a cuisine preferences dictionary and/or a cluster preferences dictionary. Returns a list of dictionaries representing the user's preferences.

**Inputs**

* (Should be Optional, but required for now) `prefcu`: a dictionary of cuisine names mapped to the score for that cuisine, which is a boundless integer, although this would work best with the score contained between -10 and 10. If any cuisine name is incorrect, that will be output in `log.txt`. All possible cuisines are listed on the right.
    * Example: `{'Sushi': 10, 'Asian': 6, 'Latin American': 10}`
* (Should be Optional, but required for now) `prefcl`: shows the user preference for each of the 54 clusters. The input dictionary is in the form of the string index of the cluster mapped to the integer score associated to that cluster. The keys can be any integer \[0, 54\] but 54 is kind of a useless cluster so it shouldn't be used as a key. The values can be any integer but should be constrained to \[-10, 10\] for best results. If an invalid key is provided, then it will be ignored and spit out in `log.txt`
    * Example: `{'0': 1, '2': 2, '3': 3, '6':2}`

```python
# All possible cuisines (as of July 12, 2020)
from reccomender import make_from_cluster as mfc
mfc.cuisine_list()[1]

['American', 'Seafood', 'Steak', 'Italian', 'Steakhouse', 'Contemporary American', 'Southern', 'Mexican', 'Japanese', 'French', 'Wine Bar', 'Comfort Food', 'Contemporary Italian', 'Latin American', 'Tex-Mex', 'Cajun', 'Pizzeria', 'Mediterranean', 'Sushi', 'International', 'Lounge', 'Spanish', 'Asian', 'Global', 'Brazilian', 'Shellfish', 'Creole / Cajun / Southern', 'Bar / Lounge / Bottle Service', 'Tapas / Small Plates', 'Contemporary Southern', 'Bistro', 'Brazilian Steakhouse', 'European', 'Pub', 'Oyster Bar', 'Cocktail Bar', 'Chinese', 'Contemporary French', 'South American', 'Fusion / Eclectic', 'Farm-to-table', 'Gastro Pub', 'Sports Bar', 'Indian', 'Contemporary French / American', 'French American', 'Barbecue', 'Southwest', 'Continental', 'Vegetarian / Vegan', 'Southern African', 'Afternoon Tea', 'Contemporary Mexican', 'Latin / Spanish', 'Dessert', 'Grill', 'Lebanese', 'Korean', 'Sicilian', 'Peruvian', 'Chinese (Canton)', 'Fish', 'Burgers', 'Wild Game', 'Mexican / Southwestern', 'Contemporary Asian', 'Vietnamese', 'Traditional Mexican', 'Breakfast', 'Organic', 'Regional Japanese', 'Prime Rib', 'Dim Sum', 'Meat', 'Contemporary Indian', 'Winery', 'Halal', 'Pakistani', 'Argentinean', 'Greek', 'Creative Japanese', 'Teppanyaki', 'Ecuadorian', 'Cuban', 'Soul food', 'British', 'Beer Garden', 'Austrian', 'German', 'Rotisserie Chicken', 'Ramen', 'Regional Mexican', 'Café', 'Modern European', 'Northern Mexican', 'Thai', 'English', 'Fondue']
```

**Outputs**

* List of dictionaries with two key - value pairs; the first key is `"key"` and is mapped to a two element tuple: \[main ingredient name, descriptor\]. The second key is `"value"` and is mapped to a two element dictionary: `"score"` mapped to the item score of that ingredient and `"n"` mapped to the `confidence index` \(how many times that ingredient has shown up in the input preferences\).
    * See the code example on the right for an example.


## Search results

### POST to /search/

```python
# Search for chinese restaurants with delivery

# utdl = result of previous call to /userprefgen/
response = requests.post(awsurl + 'search/', json = {
    'search_string':'chinese', # breaks properly
    'submenus':['delivery'], # non required works
    'prefs':utdl, # breaks properly
    'coords':[29.72154017, -95.39608383], # non required works
    'restrictions':[], # non required works
    'prefdict': {'locscore': 2, 'rev_sum_score': 0.03, 'item_sum_score': 0.6} 
    })
newdict = ast.literal_eval(response.text.replace('Infinity', '-1').replace('false', 'False').replace('null','None'))
```
```javascript
{
  "3841689": { // hash of restaurant 
    "items": { // dictionary of items in the top n belonging to that restaurant 
      "82051594": {"score": 0.3346763757962888, "rev": 0}, // item hash is mapped to the score
      "21833507": {"score": 0.3191075257345033, "rev": 3},
      ...
    },
    "item_sum_score": 1.307567803061584, // sum of item scores for that restaurant
    "rev_sum_score": 0.0, // the sum of how the items were perceived in the reviews
    "loc": 21.016145659571272, // how far away the restaurant is in miles
    "locscore": 0.005 // miles of location converted to a score
  },
  ...
}
```
```javascript
/* All possible submenu filters */
main, takeout, delivery, dessert, dinner, lunch, breakfast, pizza, sushi, appetizers, kids, brunch
```

Takes a POST request containing a search query and other parameters. Returns a list of dictionaries representing the recommended restaurants and their basic information.

**Inputs**

* `prefs`: the output of the user preference endpoint, which is explained in detail in the [user-preferences](#user-preference) endpoint in the "results" section. This is required.
* (Optional) `coords`: a two-length list of latitude and longitude coordinatres from the user's location. This is optional, and if not provided the `loc` and `locscore` fields will be empty. 
    * Example: `[29.72154017, -95.39608383]`
* (Optional) `restrictions`: a list of restrictions the user selected. For now, only 'vegan' and 'vegetarian' are supported.
    * Example: `['vegan']`, `[]`
* (Optional) `prefdict`: This is a three-length dictionary with keys `locscore`, `rev_sum_score`, and `item_sum_score`. Each of these keys are mapped to a float which represents the importance of location, review score, and item score respectively for the user.
* (Optional) `search_string`: Any string that the user inputs from the search bar. This is supposed to be either a cuisine, restaurant name, cluster name, or food name. This is optional, and if not provided the return will just be the highest rated items for the user.
    * Example: `chinese`, `burgers`, `chicken`, `pizza`
* (Optional) `submenus`: a list of submenus that the user selects. Can be any combination of strings from the list of all possible submenus (see right). Should either be 'all' or a list of other strings. 'all' automatically includes all other submenus, so including other submenus with it is redundant. Adding delivery or takeout means only restaurants with delivery or takeout's menu items are considered.
    * Example: `['all']`, `['delivery', 'dinner']`

**Outputs**

* List of dictionaries where the restaurant hash is mapped to a dictionary of top items belonging to that restaurant
    * See the code example on the right for an example.


## Restaurant menu 

### POST to /rstget/

```python
# Get recommended menu items for a restaurant 
rhash = '0b7f3d99-13ce-4d45-955e-71902c4cb9a4'
response = requests.post(awsurl + 'rstget/', json = {'rhash':rhash}), 'prefs':utdl}) 
newdict = ast.literal_eval(response.text.replace('Infinity', '-1').replace('false', 'False').replace('null','None'))
```
```javascript
{
    "42d9f32b-f4d8-4723-b905-240dcd3324f2":  // hash of the item
        {"itemscores": 
            [ // list of ingredients, each with a scores dict and the name and description. This structure is consistent for each ingredient, with the 'scores' key mapped to a dictionary of 'score' mapped to the item score, 'n' mapped to the confidence index, 'desc' mapped to the descriptor of that ingredient, and 'name' mapped to the name of that ingredient.
                {"scores": {"score": 0.829, "n": 3}, "desc": "", "name": "tomato"}, 
                {"scores": {"score": 0.738, "n": 3}, "desc": "", "name": "onion"},
                ...
            ],
            "score": 0.171969300975924, // score of the item's ingredients (float)
            "hash": 4e17ee65-c3ad-480d-8457-1878fc2c146f, // hash of the restaurant it belongs to (string)
            "submenu": "Lunch Menu", // submenu the item was found in (string)
            "submenufilter": ["lunch"], // submenu filter the item was found in (list of strings - constrained to items in all possible submenufilters
            "price": "9.00", // price of the item (string of float)
            "rev": 4, // accumulated score from the reviews (int))
        }
    ...
}
```
```javascript
/* All possible submenu filters */
main, takeout, delivery, dessert, dinner, lunch, breakfast, pizza, sushi, appetizers, kids, brunch
```

Takes a POST request containing a restaurant hash and (optionally) user preferences. Returns a list of dictionaries representing the recommended menu items and their basic information.

**Inputs**

* `rhash`: A UUID of a restraunt in the database. Must be a string with no leading spaces.
* (Optional) `prefs`: the output of the user preference endpoint, which is explained in detail in the [user-preferences](#user-preference) in the "results" section. This is optional, and if it is not provided the `itemscores` key in the results dictionary will map to an empty list. 

**Outputs**

* See the code example on the right for an example and description.