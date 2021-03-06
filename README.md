#presto-api

Easily build a RESTful API on top of MongoDB collections.

In order to start building a project, it's often wise to set up a RESTful API.  Presto-API allows you to build an API-by-configuration, rather than API-by-middleware.

###Typical Usage

The below snippet shows how one can quickly get an API set up.  With the below, you now have an API running that can handle typical RESTful operations (GET, POST, PUT and DELETE).

```js
let Presto = require('presto-api');
let api = new Presto({
	name: 'api.widgets.com',
	version: 'v0',
	port: 3000,
	jsonp: true,
	crossDomain: true,
	crossDomainAllowOrigin: '*',
	maxAge: 500,
	database: {
		host: 'localhost',
		port: 27017,
		name: 'widgetsdb'
	},
	resources: [
		{
			name: 'widgets',
			sort: { widget_id: 1 },
			limit: 20,
			get: true,
			post: true,
			put: true,
			del: true,
			schema: {
				widget_id: 'number',
				name: 'string',
				price: 'string',
				img_path: 'string',
				for_sale: 'boolean'
			}
		}
	]
});

api.init();
```

###Minimal Configuration

At the absolute minimum Presto-API requires an array of resources so that it can map API routes to your MongoDB collections.  Typically this is an array of configuration options that describe how an API endpoint should handle a particular resource.  However, you can also pass a string array of resource names.  In this case, Presto-API will use sensible defaults to complete the setup.  See defaults section for more.

###Minimal Usage
```js
let Presto = require('presto-api');
let api = new Presto({
	resources: ['widgets']
});
api.init();
```

###Configuration Options

######name (optional)

######version (optional)

######port (optional)

######jsonp (optional)

######crossDomain (optional)

######crossDomainAllowOrigin (optional)

######maxAge (optional)

######database.host (optional)

######database.port (optional)

######database.name (optional)

######resources (**required**)

######resource.name (optional)

######resource.sort (optional)

######resource.limit (optional)

######resource.get (optional)
When true, a route will be setup that allows GET operations on your collection.

curl http://localhost:3000/songs/
curl http://localhost:3000/songs/566073c92f1cc55a23b64f3b

######resoure.post (optional)
When true, a route will be setup that allows POST operations on your collection.

######resource.put (optional)
When true, a route will be setup that allows PUT operations on your collection.

######resource.del (optional)
When true, a route will be setup that allows DELETE operations on your collection.

######resource.schema (optional)
When provided, Presto-API will ensure that data POSTed or PUT to your API endpoint will conform to the specified schema.

Presto-API accepts arbitrarily deep JSON objects here and allows for the following field types: string, number, id (MongoDB ObjectID), and date.

###Working with your Presto-API

######fields
`/songs/?fields=artist,album,name,year` -- include only the specified fields in the result set returned by the Presto-API

######sort
`/songs/?sort=<field>:<direction>` -- field corresponds to a a field in the MongoDB collection.  Direction is either asc (ascending) or desc (descending) 
`/songs/?sort=artist:asc` - sort songs by artist name
`/songs/?sort=artist:asc,album:asc,name:asc` -- specifying primary, secondary and tertiary sorts

######offset
`/songs/?offset=100&sort=created:desc&limit=10` -- return the 101st - 111th most recently created song objects.

######limit
Limit the number of results returned by Presto-API
`/songs/?limit=10` -- return no more than 10 songs

######q
Full-text search query -- this allows searching of multiple fields.  
Note that this requires a text index to be set up on the collection.

`/songs/?q=moon` -- Return all songs in which the artist, album or song name include the word moon.  (In this case a text index has been created for the songs collection including artist, album and song fields).

###Presto-API Query Language
In order to facilitate searching of individual fields of your collections, Presto-API accepts key-value pairs that have been separated from the URI with an underscore -- see the following:
`/songs/_/<property_name>/<property_value>`

`/songs/_/artist/beirut` -- return all Beirut songs
`/songs/_/artist/beirut/year/2006` -- return all Beirut songs from 2006








