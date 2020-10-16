Test tutorial for filtering Tweets by location
----------------------------------------------

Test Go to the [filter tutorial](/filtering-twitter-by-location)

Go to the [filter tutorial](/filtering-twitter-by-location.html)

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

You can use the [editor on GitHub](https://github.com/EmilySheehan/happycamper.github.io/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/EmilySheehan/happycamper.github.io/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
