# Couch
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
`DELETE` `localhost:5984/name_of_database/_id?rev=revision_number

# Update a Document
`PUT` `localhost:5984/name_of_database/_id` `Body: { "key" : "value }` `Headers: Content-Type: application/json`

Could specify `_deleted : true` 

# Fetch a Document
`GET` `localhost:5984/name_of_database/_id` Add `revs_info=true` query param to show all revisions `status: available` has to do with Databse compaction
