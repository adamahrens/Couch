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
