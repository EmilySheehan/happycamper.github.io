Introduction
------------

When working with Tweet data, there are two classes of geographical metadata:

-	Tweet location - Available when user shares location at time of Tweet.
-	Account Location - Based on the 'home' location provided by user in their public profile. This is a free-form character field and may or may not contain metadata that can be geo-referenced.

These are described separately in the next two sections.

*Important Notes:*

-	With one exception, all geographical coordinates are provided in the [LONG, LAT] order.  
-	Except for a root-level 'geo' attribute, all geographic coordinates are provided in the [LONG, LAT] order.
-	All PowerTrack Geo-Operators expect coordinates in the [LONG, LAT] order.

If you are using the 'original' format:

-	JSON parsers should use the [LONG, LAT] order and always ignore the root-level 'geo' attribute.

If you are using the Activity Stream format:

-	Geo-tagged tweets will have a root-level 'geo' attribute in the *[LAT, LONG]* order.
-	All other geographic coordinate attributes are in [LONG,LAT] order.

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

<pre>
{
   "place": {
     "id": "fd70c22040963ac7",
     "url": "https:\/\/api.twitter.com\/1.1\/geo\/id\/fd70c22040963ac7.json",
     "place_type": "city",
     "name": "Boulder",
     "full_name": "Boulder, CO",
     "country_code": "US",
     "country": "United States",
     "contained_within": [

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
   }
}
</pre>
<br />

##### Activity Streams Format

Note that in this format, there is no root level "geo" attribute.

<pre>
{
     "location": {
         "objectType": "place",
         "displayName": "Boulder, CO",
         "name": "Boulder",
         "country_code": "United States",
         "twitter_country_code": "US",
         "link": "https:\/\/api.twitter.com\/1.1\/geo\/id\/fd70c22040963ac7.json",
         "geo": {
           "type": "Polygon",
           "coordinates": [
             [
               [-105.301758, 39.964069],
               [-105.301758, 40.094551],
               [-105.178142, 40.094551],
               [-105.178142, 39.964069]
             ]
           ]
         }
     }
}
</pre>
<br/>

#### Exact Geo Location JSON metadata

In the case of Twitter's original format, the root level "geo" and "coordinates" attributes provide the decimal degree coordinates for the exact location. Tweets containing this metadata can also include "Twitter Place" data described above, although the presence of both is not guaranteed.

**Note that the "coordinates" attributes is formatted as [LONGITUDE, latitude], while the "geo" attribute is formatted as [latitude, LONGITUDE].**

##### Twitter Original Format

<pre>
{
    "geo": {
        "type": "Point",
        "coordinates": [40.0160921, -105.2812196]
    },
    "coordinates": {
        "type": "Point",
        "coordinates": [-105.2812196, 40.0160921]
    }
}
</pre>

##### Activity Streams Format

<pre> { "geo": { "type": "Point", "coordinates": [40.0161311, -105.2811837] } }</pre><br />

Activity Location Operators<a id='ActivityLocationOperators' class="tall">&nbsp;</a>
------------------------------------------------------------------------------------

#### place:

Filter for specific Places by their name or ID. To discover "Places" associated with a specific area, use Twitter's reverse_geocode endpoint in the REST API. Then use the Place IDs you find with the place: operator to track Tweets that include the specific Place being referenced. If you use the Place name rather than the numeric ID, ensure that you quote any names that include spaces or punctuation.

#### place_contains:

Rather than matching for specific Places, the place_contains: operator performs a substring match against Place names sent through from Twitter. This can accommodate concepts like place_contains:", CO" to match Places that contain names like "Boulder, CO" or "Denver, CO".

#### country_code:

Each Twitter "Place" comes with a country code, indicating the country in which the Place is located. The country_code: operator allows you to filter on this ISO alpha-2 character code (see here for country code references).

#### has:geo

The has:geo operator matches for the presence of either Point or Place geo information within the Twitter payload. Note that this does not allow you to specify specific locations or types of geo data, it simply requires that results have Tweet-specific location information of some kind.

#### point_radius:

The point_radius: operator allows you to specify a circular geographic area and match Tweets containing Tweet-specific location data that fall within that area. To use, define a central lon-lat coordinate, and then set the radius (up to 25 miles). Any Tweet containing a geo Point that falls within this region will be matched. Addtionally, Tweets containing Twitter Places will match where the geo polygon defined for the Place falls fully within the defined point-radius area. Places whose polygons fall outside the defined point-radius area to any extent will not match.

Usage resembles the following:

```
 point_radius:[lon lat radius]
```

#### bounding_box:

The bounding_box: operator allows you to specify a 4-sided geographic area and match Tweets containing Tweet-specific location data that fall within that area. To use, define lon-lat coordinates that represent the opposite corners of the box, such that each side of the box is up to 25 miles in length. Any Tweet containing a geo Point that falls within this region will be matched. Addtionally, Tweets containing Twitter Places will match where the geo polygon defined for the Place falls fully within the defined point-radius area. Places whose polygons fall outside the defined point-radius area to any extent will not match.

Usage resembles the following:

```
 bounding_box:[west_long south_lat east_long north_lat]
```

---

<br/>

Profile Locations (Account-based)<a id='ProfileLocation' class="tall">&nbsp;</a>
--------------------------------------------------------------------------------

Another option in filtering for Tweets by location information is to match for location information within a Twitter user's profile. Several data fields fall into this category, but all represent types of information which are set by the user at the account level. These values are generally not frequently changed by the user, and do not necessarily represent the location that the user is currently Tweeting from, although they may.

In addition to the profile location provided by Twitter, Gnip provides an optional Profile Geo enrichment that formalizes the data in the profile location, and makes it more convenient to filter.

### Profile Location Metadata<a id='ProfileLocationPayloads' class="tall">&nbsp;</a>

##### Original Twitter Format

<pre> { "user": { "location": "Boulder, CO", "description": "By now feeling like a Colorado native", "utc_offset": -25200, "time_zone": "Mountain Time (US &amp; Canada)", } }</pre><br />

##### Activity Streams Format

<pre> { "actor": { "summary": "By now feeling like a Colorado native", "twitterTimeZone": "Mountain Time (US &amp; Canada)", "utcOffset": "-25200", "location": { "objectType": "place", "displayName": "Boulder, CO" } } }</pre><br />

With the Profile Geo enrichment enabled, the above Twitter Profile Location results in the following "profileLocations" attribute in the "gnip" root-level section. Note that these metadata are only available in the Activity Stream format. Also note that the "profileLocations" metadata is defined as an array of locations. While only one location is currently provided, the Profile Geo enrichment may in the future be able to resolve multiple locations mentioned is a user's Profile Location. See [HERE](http://support.gnip.com/enrichments/profile_geo.html) for more information on the Profile Geo enrichment.

##### Profile Geo Metadata

**Note:** *that all Profile Geo coordinates are provided in the [Longitude, Latitude] order.*

<pre> { "gnip": { "profileLocations": \[ { "objectType": "place", "geo": { "type": "point", "coordinates": \[ -105.27055, 40.01499 ] }, "address": { "country": "United States", "countryCode": "US", "locality": "Boulder", "region": "Colorado", "subRegion": "Boulder County" }, "displayName": "Boulder, Colorado, United States" } ] } }</pre><br />

Profile Location Operators<a id='ProfileLocationOperators' class="tall">&nbsp;</a>
----------------------------------------------------------------------------------

#### Profile Geo Enrichment Operators

The following Operators are available for building rules if you have the Profile Geo enrichment enabled:

#### has:profile_geo

This filter matches for the presence of Gnip's Profile Geo enrichment data in a specific Tweet, regardless of the value. This will only match Tweets where Gnip has been able to provide formal Geography information for the profile location provided by the Twitter user, consistent with the description of the enrichment here.

#### profile_point_radius: / profile_bounding_box:

Matches tweets where Gnip's Profile Geo enrichment data is available, and falls within the defined point-radius or bounding-box area. Note: this will only match Tweets where Gnip has been able to provide formal Geography information for the profile location provided by the Twitter user, consistent with the description of the enrichment here.

Usage resembles the following:

```
 profile_point_radius:[lon lat radius]

 profile_bounding_box:[west_long south_lat east_long north_lat]
```

#### profile_country_code:

Matches tweets where Gnip's Profile Geo enrichment data is available, and contains the defined country code. Note: this will only match Tweets where Gnip has been able to provide formal Geography information for the profile location provided by the Twitter user, consistent with the description of the enrichment here.

#### profile_region: / profile_region_contains:

Matches tweets where Gnip's Profile Geo enrichment data is available, and includes the specified "region." Note that profile_region: will perform an exact string match, while profile_region_contains: performs a substring match.

This will only match Tweets where Gnip has been able to provide formal Geography information for the profile location provided by the Twitter user, consistent with the description of the enrichment here.

#### profile_locality: / profile_locality_contains:

Matches tweets where Gnip's Profile Geo enrichment data is available, and includes the specified "locality." Note that profile_locality: will perform an exact string match, while profile_locality_contains: performs a substring match.

This will only match Tweets where Gnip has been able to provide formal Geography information for the profile location provided by the Twitter user, consistent with the description of the enrichment here.

#### Standard Profile Location Operators

The following Operators are available for filtering on location mentions in a user's Twitter Profile Location and are not dependent on having the Profile Geo enrichment enabled:

#### bio_location:

The bio_location: operator performs a tokenized match against the user's account-level location field. Note that this field is user-generated and does not necessarily reflect an actual location, and generally does not change from Tweet to Tweet.

#### bio_location_contains:

Substring match against the user's account-level location field. Note that this can return large amounts of data, and should be used with care. We recommend strategies which focus on specifically known types of data that limit the results. For example, substrings like bio_location_contains:", CO" instead of bio_location_contains:"CO".

#### time_zone:

Matches the user's account-level time zone, as selected in their account settings on twitter.com ([https://twitter.com/account/settings]).

---

Other Operators<a id='OtherOperators' class="tall">&nbsp;</a>
-------------------------------------------------------------

The following additional operators filter on fields which, while not explicitly location fields, do sometimes contain account-based location information

#### bio_contains:

Performs a substring match on the user's account-level bio, which is comprised of user-generated text and may not include location information.

#### bio_lang:

Matches Tweets based on the account-level language associated with the user. This language is set at the account level and will be represented by an ISO 639-1 language code. Note that Twitter does not support all languages on the ISO 639-1 list, and the language may not actually reflect the user's true language, as there are default values, and the user can change the value.

---

Usage Examples<a id='UsageExamples' class="tall">&nbsp;</a>
-----------------------------------------------------------

In many cases, you may want to pull in as much data as possible related to a location, regardless of whether this information exists as Tweet-specific information, or information hidden in the user's account metadata. Below are examples of how the various operators can be combined in a rule to catch more types of data. In these examples, the rule is looking for mentions of the hashtag '#FlagstaffFire' that contain various types of location data.

-	Geo-tagged Tweets within a bounding box or associated with a Twitter Place that mentions "Boulder":

	`#FlagstaffFire (bounding_box:[-105.301758 39.964069 -105.178505 40.09455] OR place_contains:"Boulder")`

-	Tweets from users that have Profile Locations that mention Boulder, CO and not Boulder, NV, using **standard** Profile Location Operators:

	`#FlagstaffFire bio_location_contains:"boulder" -(bio_location_contains:nevada OR bio_location_contains:", NV")`

-	Tweets from users that have Profile Locations that mention Boulder, CO and not Boulder, NV, using **Profile Geo** Operators:

	`#FlagstaffFire profile_locality:boulder profile_region:colorado`

-	Tweets that have any Activity Location (geo-tagged Tweet) with a Profile timezone set to US Mountain:

	`#FlagstaffFire has:geo time_zone:"Mountain Time (US & Canada)"`

-	Tweets with any hint of coming from the Boulder, CO area using **Profile Geo** Operators:

	`#FlagstaffFire (point_radius:[-105.292778 40.019444 25mi] OR place:"Boulder, CO" OR (profile_locality:boulder profile_region:colorado ))`
