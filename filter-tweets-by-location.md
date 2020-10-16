Tweet Locations (geo-tagged Tweets)<a id='ActivityLocation' class="tall">&nbsp;</a>
-----------------------------------------------------------------------------------

Twitter enables users to specify a location for individual Tweets. PowerTrack offers multiple ways to filter for Tweets by Tweet-specific location data through its various operators (see our documentation for [Twitter PowerTrack Operators](/sources/twitter/powertrack_operators.html) for details). Tweet-specific location information falls into two general categories:

-	Tweets with a specific latitude/longitude "Point" coordinate
-	Tweets with a Twitter "Place" (see [HERE](https://blog.twitter.com/2010/twitter-places-more-context-your-tweets) and [HERE](https://dev.twitter.com/docs/platform-objects/places) for more information).

Tweets with a Point coordinate come from GPS enabled devices, and represent the exact GPS location of the Tweet in question. This type of location does not contain any contextual information about the GPS location being referenced (e.g. associated city, country, etc.), unless the exact location can be associated with a Twitter Place.

Tweets with a Twitter "Place" contain a GPS polygon, consisting of 4 lat-lon coordinates that define the general area (the "Place") from which the user is posting the Tweet. Additionally, the Place will have a display name, type (e.g. city, neighborhood), and country code corresponding to the country where the Place is located, among other fields.

### Activity Location Metadata<a id='ActivityLocationPayloads' class="tall">&nbsp;</a>

#### Twitter Place JSON Metadata

Note that with Original format the root level "geo" and "coordinates" attributes are set to "null".

##### Twitter Original Format

<pre> { "place": { "id": "fd70c22040963ac7", "url": "https:\/\/api.twitter.com\/1.1\/geo\/id\/fd70c22040963ac7.json", "place_type": "city", "name": "Boulder", "full_name": "Boulder, CO", "country_code": "US", "country": "United States", "contained_within": \[

```
 ],
 "bounding_box": {
   "type": "Polygon",
   "coordinates": [
     [
       [-105.301758, 39.964069],
       [-105.301758, 40.094551],
       [-105.178142, 40.094551],
       [-105.178142, 39.964069]
     ]
   ]
 },
 "attributes": {}
```

\} }</pre><br />
