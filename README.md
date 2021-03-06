# CouchDB
CouchDB stuff for v 2.2.0

# What is Couch

Cluster of unreliable hardware. 
* Has horizontal scalability. 
* Add extra nodes to the cluster. 
* Increase abilities as the need arises. 
* Should be much less expensive (cluster of RaspberryPis')

Vertical scability need to add more RAM, more bandwidth, more CPU. Basically change the hardware

* NoSQL (Document Oriented). 
* Stored as JSON documents. 
* Schemaless
* REST API
* Eventual consistency ( sacrificed for reliability ). Changes eventually propogate to other nodes

# CouchDB vs Relational

Would need to setup the table and columns before saving to the database. NoSQL allows to add more key values without adding columns (Adding Bands link to an Artist )

Utilize MapReduce queries to get the data we need.

# How's it different

* Document storage vs defined schema
* Multi version concurrency control instead of locking
* Upload documents vs inserting values (JSON document to CouchDB to replace exisiting document vs insert rows into tables)
* Need to know how to use the Data vs how the data will look

# Adding a User

* Need to create a _user document

```
{
  "_id" : "org.couchdb.user:username,
  "name" : "username",
  "password" : "xxxxxx",
  "roles" = [],
  "type" = "user
}
```

# REST

`GET` `localhost` with header `Authorization: Basic asd32523`

# Create DB via REST
`PUT` `localhost:5984/name_of_database`

# Delete DB via REST
`DELETE` `localhost:5984/name_of_database`

# Get DB via Rest
`GET` `localhost:5984/name_of_database`

# Create a Document
`POST` `localhost:5984/name_of_database` `Body: { "key" : "value }` `Headers: Content-Type: application/json`
If no `_id` key is provided then CouchDB will create one for you.
`rev` in the response is required for all future `POST` calls

# Delete a Document
`DELETE` `localhost:5984/name_of_database/_id?rev=revision_number`

# Update a Document
`PUT` `localhost:5984/name_of_database/_id` `Body: { "key" : "value }` `Headers: Content-Type: application/json`

Could specify `_deleted : true` 

# Fetch a Document
`GET` `localhost:5984/name_of_database/_id` Add `revs_info=true` query param to show all revisions `status: available` has to do with Databse compaction

# Add a Attachment to a Dcoument
`PUT` `localhost:5984/name_of_database/_id?rev=rev_id` `Headers : Content-Type: image/jpeg`

# Database Commands
`_compact` begins the compaction process.

`_view_cleanup` removes indexes for view that do not exisit

`_revs_limit` GET or PUT the max number of revisions (default = 1000)

`POST` `localhost:5984/name_of_database/_compact` `Headers: Content-Type: application/json`

`POST` `localhost:5984/name_of_database/_view_cleanup` `Headers: Content-Type: application/json`

`GET` `localhost:5984/name_of_database/_revs_limit` `Headers: Content-Type: application/json`

# Fetch All Documents
`GET` to fetch all documents

`GET` `localhost:5984/name_of_database/_all_docs` `Headers: Content-Type: application/json`

```
{
    "total_rows": 1,
    "offset": 0,
    "rows": [
        {
            "id": "eea3ef21a814dcab8f3f489df800aa5f",
            "key": "eea3ef21a814dcab8f3f489df800aa5f",
            "value": {
                "rev": "2-0fff73b53338b93f39592627a02a8017"
            }
        }
    ]
}
```

Additional Query Params

| Query         | Type          | Example  |
| ------------- |:-------------:| -----:|
| descending     | boolean | http://localhost:5984/name_of_database/_all_docs?descending=true |
| include_docs   | boolean | http://localhost:5984/name_of_database/_all_docs?include_docs=true  |
| limit          | number  | http://localhost:5984/name_of_database/_all_docs?limit=25  |
| skip           | number  | http://localhost:5984/name_of_database/_all_docs?skip=10  |

`POST` to retrieve a specific group of documents all at once

# Bulk Documents

`POST` to upload multiple new documents at once

`http://localhost:5984/name_of_database/_bulk_docs

```
{ "docs" : [
	{ "_id": "sdlkjf;aslkj32523;lkj;lkjadsf",
	  "name": "First Last",
	   "address": {
		"street": "2875 Goodrich",
		"city": "Saint Paul",
		"state": "MN"
		}
	},
	{ "_id": "superlkjwlkjva;lskj;lwek",
	  "name": "Jimmy Last",
	   "address": {
		"street": "3112 Goodrich",
		"city": "Saint Paul",
		"state": "MN"
		}
	},
	{ "_id": "asdfhgfdjrtyeryeryerydfghfsf",
	  "name": "First Jimmy",
	   "address": {
		"street": "5656 Goodrich",
		"city": "Saint Paul",
		"state": "MN"
		}
	}
	]
}
``` 
# Changes

`GET` `http://localhost:5984/name_of_database/_changes`

# Security

`GET` To view the current security. `http://localhost:port/name_of_database/_security`

`PUT` To replace the current security

# Design Documents

Utilize MapReduceQuery to return Design Docs

Types
* Views (Emits Key Value pairs)
* Show (Displays a single Document)
* List (Produce list of mutliple Documents )
* Update (Broad changes across a database, add/change fields)
* Validation

To see the results of the Design Doc `http://localhost:5984/name_of_database/_design/name_of_design_doc/_view/name_of_view`

# Build Design Documents in Futon

Add with example like

``` 
function (doc) {
  if (doc.name.length > 0 && doc.hobbies.length > 0) {
    emit(doc.name, doc.hobbies[0]);
  }
}
```

# Mango

Query language inspired by MongoDB.
Consists of two parts
* Indexes
* Find statements to query

# Anatomy of an Index

* Fields: Array of keys that are to be indexed
* ddoc: Name of design document to put the index in (will create if doesnt exist)

```
{"index" : { "fields" : ["key"] }, "ddoc" : "name_of_design_document", "name" : "name_of_index", "type" : "json" }
```

Using sample above as the Body in the request

`POST` `http://localhost:5984/name_of_database/_index`

Return the list of all indexes
`GET` `http://localhost:5984/name_of_database/_index`

To Remove an Index
`DELETE` `http://localhost:5984/name_of_database/_index/ddoc_name/json/name_of_index`

# Anatomy of a Find Query

```
{
 "selector" : {
   "_id": { "$gt" : null }
 },
 "fields" : ["_id", "column_name_1", "column_name_2"],
 "limit" : 5,
 "sort" : [{"_id" : "desc"}],
 "use_index" : ["ddoc", "name"]
}
```
* selector the only required part
* list of fields to return. Full document returned if fields not specified 
* _id and _rev need to be included
* limit defaults to 25
* skip defaults to 0
* sort values must be included in the fields array, selector, and index being used
* use_index specifieds the design document and index name to use. CouchDB will guess otherwise

# Examples of selector

`$lt`, `$lte`, `$gt`, `$gte`, `$in`, `$nin`, `$eq`, `$neq`

More selectors can be found at https://docs.couchdb.org/en/2.2.0/api/database/find.html#find-selectors
`"year" : { "$lt" : 1986 }`

Can use object.propertyName for finds embedded objects

Concrete example

```
{
   "selector": {
      "_id": {
         "$gt": null
      },
      "name": {
         "$gt": null
      }
   },
   "limit": 5
}
```

```
{
   "selector": {
      "_id": {
         "$gt": null
      },
      "name": {
         "$gt": null
      },
      "hours": {
         "$eq": null
      }
   },
   "fields": [
      "name",
      "hours",
      "city"
   ],
   "limit": 5
}
```

```
{
   "selector": {
      "attributes.WiFi": {
         "$ne": "no"
      }
   },
   "limit": 5
}
```

```
{
   "selector": {
      "stars": {
         "$gt": 4
      }
   },
   "fields": [
      "name",
      "city",
      "state",
      "stars"
   ]
}
```

```
{
   "selector": {
      "stars": {
         "$gt": 4
      },
      "review_count": {
         "$gt": 100
      }
   },
   "fields": [
      "name",
      "city",
      "state",
      "stars",
      "review_count"
   ]
}
```

Can execute a Find via REST

`POST` `http://localhost:5984/name_of_database/_find`

# Complex Queries

$and / $or : [ array of other selectors]. $not, $all (must be run on an array)

```
{
   "selector": {
      "$and": [
         {
            "name": {
               "$gt": null
            }
         },
         {
            "city": {
               "$eq": "Las Vegas"
            }
         },
         {
            "state": {
               "$ne": "NV"
            }
         }
      ]
   }
}
```

Documents matching they are have a city equal to Las Vegas outside of Nevada or they're in Nevada but not Las Vegas
```
{
   "selector": {
      "$or": [
         {
            "$and": [
               {
                  "city": {
                     "$eq": "Las Vegas"
                  }
               },
               {
                  "state": {
                     "$ne": "NV"
                  }
               }
            ]
         },
         {
            "$and": [
               {
                  "city": {
                     "$ne": "Las Vegas"
                  }
               },
               {
                  "state": {
                     "$eq": "NV"
                  }
               }
            ]
         }
      ]
   },
   "fields": [
      "name",
      "state",
      "city",
      "postal_code"
   ]
}
```

# Replication

* Used to update one database with changes from another
* Triggered by documents or tasks in _replicator
* Will copy and delete documents
* All involved databases will then become synced
* Tries resolving conflicts or choose one randomly
* Mango queries can be used in the replication task

# Replication via API

Basic format. Target and Source are the only required fields. Can be Remote -> Local, Local -> Remote, etc

```
{
 "_id" : "optional will be auto generated if not provided",
 "source" : "http://user:password@server.com:3001/name_of_database",
 "target" : "http://user:password@localhost.com:3001/name_of_database".
 "create_target" : true // creates the database if it doesnt exist
 "continuous" : true, //default false
 "filter" : "mydocuments/myfilter",
 "selector" : { "filter_by" : { "$eq" : "a" }}
}
```

`PUT` `http://localhost:5984/_replicator/_id_to_use`

# Clusters

* Cluster - Group of connected computers that work together as if they're a single entity
* Node - Single computer in the cluster
* Shard - A partition of a database held in a separate node
* Replicates - Copies of a document in separate shards
* Quorum - Minimum number of agreement among nodes before Read/Write execution is considered successful

Defaults 
q = 8 (Number of shards to create)
r = 2 (Read quorum)
w = 2 (Write quorum)
n = 3 (Number of replicates)
