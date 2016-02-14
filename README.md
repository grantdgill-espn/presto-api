#presto-api

Easily build a RESTful API on top of MongoDB collections.

In order to start building a project, it's often wise to set up a RESTful API.  Presto-API allows you to build an API-by-configuration, rather than API-by-middleware.

##Usage

###Minimal Configuration

At the minimum Presto-API requires an array of resources so that it can map API routes to your MongoDB collections.  Typically this is an array of configuration options that describe how an API endpoint should handle a particular resource.  However, you can also pass a string array of resource names.  In this case, Presto-API will use sensible defaults to complete the setup.  See defaults section for more.

###Minimal Usage
```js
let Presto = require('presto-api');
let api = new Presto({
	resources: ['widgets']
});
api.init();
```

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

###Configuration Options

######name (optional)

######version (optional)

######port (optional)
Type: Integer

This is the port in which your Presto-API will listen.

Default: 3000

######jsonp (optional)
Type: Boolean

When true, 

Default: true

######crossDomain (optional)
Type: Boolean

When true, CORS (cross-origin resource sharing) support will be added to each of your routes.  This setting can be overridden on a per-resource level.

Default: true

######crossDomainAllowOrigin (optional)
Type: String

Specify allowed origins for your resources.

Default: * (all origins allowed).

######maxAge (optional)
Type: Integer

When specified a Cache-Control header will be added to your GET routes which specifies the number of max age in seconds. Note that Presto does not provide a caching mechanism by itself.

######database.host (optional)

Name of host machine where mongod is running.

Default: localhost

######database.port (optional)
Type: Integer

Port on host machine in which mongod is listening.

Default: 27017

######database.name (optional)
Type: String

Name of mongo database to connect to.

Default: local

######resources (**required**)

######resource.name (**required**)
Type: String

Resource name corresponds to the name of your MongoDB collection

######resource.sort (optional)
Type: Object

Allows a default sort be placed on GET requests to the resource.  Can be overriden by specifying a `sort` parameter in GET requests.

######resource.limit (optional)
Type: Integer

Specify a default limit.  Can be overriden by specifying a `limit` parameter in GET requests.

######resource.get (optional)
When true, a route will be setup that allows GET operations on your collection.

Default: true

curl http://localhost:3000/songs/
curl http://localhost:3000/songs/566073c92f1cc55a23b64f3b

######resoure.post (optional)
When true, a route will be setup that allows POST operations on your collection.

Default: true

######resource.put (optional)
When true, a route will be setup that allows PUT operations on your collection.

Default: true

######resource.del (optional)
When true, a route will be setup that allows DELETE operations on your collection.

Default: true

######resource.schema (optional)
When provided, Presto-API will ensure that data POSTed or PUT to your API endpoint will conform to the specified schema.

Presto-API accepts arbitrarily deep JSON objects here and allows for the following field types: string, number, id (MongoDB ObjectID), and date.

###Querying your Presto-API

######fields
Type: String

Limit the fields included in your result set by specifying a comma-delimited string.

Ex: include only the artist, album, name and year in the result set.
`/songs/?fields=artist,album,name,year`

######sort
Type: String

Specify a field and direction (separated by a colon) that will determine return order.  Ex: `?sort=<field>:<direction>`  Direction is either asc (ascending) or desc (descending).  Multiple sorts can be specified by providing fields in a comma-delimited string.

When not specified, direction defaults to asc.

Ex: Sort songs by artist name
`/songs/?sort=artist:asc`

Ex: Specify primary, secondary and tertiary sorts
`/songs/?sort=artist:asc,album:asc,name:asc`

######offset
Corresponds to MongoDB's `skip` operation and controls where the result set starts.

Ex: return the 101st - 111th most recently created song objects.
`/songs/?offset=100&sort=created:desc&limit=10`

######limit
Limit the number of results

Ex: return no more than 10 songs:

`/songs/?limit=10`

######q
Full-text search query -- this allows searching of multiple fields.  
Note that this requires a text index to be set up on the collection.

`/songs/?q=moon` -- Return all songs in which the artist, album or song name include the word moon.  (In this case a text index has been created for the songs collection including artist, album and song fields).

###Presto-API Query Language
In order to facilitate searching of individual fields of your collections, Presto-API accepts key-value pairs that have been separated from the URI with an underscore -- see the following:

`/songs/_/<property1_name>/<property1_value>/<property2_name>/<property2_value>`

####Examples

###### return all songs by Beirut
`/songs/_/artist/beirut`

##### return all songs by Beirut from 2006
`/songs/_/artist/beirut/year/2006`








