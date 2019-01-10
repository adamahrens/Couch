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
