# MongoDB

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Document-oriented database](#document-oriented-database)
- [Schema-less collections](#schema-less-collections)
- [Data modeling](#data-modeling)
- [Installation](#installation)
- [Test MongoDB on Linux/OS X](#test-mongodb-on-linuxos-x)
- [Test MongoDB on Windows](#test-mongodb-on-windows)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Document-oriented database

Store JSON-like documents.

```json
{
   "_id" : ObjectId("54c955492b7c8eb21818bd09"),
   "address" : {
      "street" : "2 Avenue",
      "zipcode" : "10075",
      "building" : "1480",
      "coord" : [ -73.9557413, 40.7720266 ]
   },
   "borough" : "Manhattan",
   "cuisine" : "Italian",
   "grades" : [
      {
         "date" : ISODate("2014-10-01T00:00:00Z"),
         "grade" : "A",
         "score" : 11
      },
      {
         "date" : ISODate("2014-01-16T00:00:00Z"),
         "grade" : "B",
         "score" : 17
      }
   ],
   "name" : "Vella",
   "restaurant_id" : "41704620"
}
```

## Schema-less collections

Collection ~ SQL table, but no schema
Free-form documents ~ SQL table row

TODO: schemaless example

## Data modeling

* Creating a data model with MongoDB does not have to follow the rules that apply for relational databases. Often, they should not.
* Consider theses questions: is this a composition relationship (containment)? Is this "aggregate" of documents often used at the same time (i.e. can we reduce chattiness)? Would embedding lead to "a lot" of data duplication?

Normalized data model (references) vs. Embedded data model (sub-documents)

TODO: examples

https://docs.mongodb.com/manual/core/data-modeling-introduction/

## Installation

https://www.mongodb.com

After downloading MongoDB, you must follow the installation procedure for your platform:

https://docs.mongodb.com/manual/installation/#tutorials

## Test MongoDB on Linux/OS X

As long as you have set up your PATH correctly, you should be able to enter the MongoDB console from your command line, and insert and find some data:

```bash
$> mongo
MongoDB shell version: 3.2.3
connecting to: test
> db.things.insert({ "fruit": "apple" })
WriteResult({ "nInserted" : 1 })
> db.things.insert({ "name": "John Doe", "age": 24 })
WriteResult({ "nInserted" : 1 })
> db.things.find()
{ "_id" : ObjectId("56ca09b5d536b4526d219ba8"), "fruit" : "apple" }
{ "_id" : ObjectId("56ca095ed536b4526d219ba7"), "name" : "John Doe", "age" : 24 }
```

## Test MongoDB on Windows

MongoDB looks for a /data/db directory by default. You can create it in Babun with the following command:

```bash
$> mkdir -p /c/data/db
```

To run the MongoDB server, run the following command in Babun (adapt the path if your MongoDB installation is elsewhere):

```bash
$> "/c/Program Files/MongoDB/Server/3.2/bin/mongod.exe"

2016-02-22T07:25:52.998+0100 I CONTROL  [initandlisten] MongoDB starting : pid=203 port=27017 dbpath=/opt/local/var/db/mongodb 64-bit host=Phaeton.local
2016-02-22T07:25:52.999+0100 I CONTROL  [initandlisten] db version v3.0.7
2016-02-22T07:25:52.999+0100 I CONTROL  [initandlisten] git version: nogitversion
2016-02-22T07:25:52.999+0100 I CONTROL  [initandlisten] build info: Darwin tenten-slave.macports.org 14.5.0 Darwin Kernel Version 14.5.0: Wed Jul 29 02:26:53 PDT 2015; root:xnu-2782.40.9~1/RELEASE_X86_64 x86_64 BOOST_LIB_VERSION=1_49
2016-02-22T07:25:52.999+0100 I CONTROL  [initandlisten] allocator: system
2016-02-22T07:25:52.999+0100 I CONTROL  [initandlisten] options: { storage: { dbPath: "/opt/local/var/db/mongodb" }, systemLog: { destination: "file", logAppend: true, path: "/opt/local/var/log/mongodb/mongodb.log" } }
2016-02-22T07:25:57.514+0100 I NETWORK  [initandlisten] waiting for connections on port 27017
```

If you see Waiting for connections on port 27017, it means your MongoDB server is running. It will keep running until you hit Ctrl-C to stop it.

To run a MongoDB client, you must use the Windows command line and not Babun, as there are interaction bugs with Babun:

```bash
$> "C:\Program Files\MongoDB\Server\3.2\bin\mongo.exe"
MongoDB shell version: 3.2.3
connecting to: test
> db.things.insert({ "fruit": "apple" })
WriteResult({ "nInserted" : 1 })
> db.things.insert({ "name": "John Doe", "age": 24 })
WriteResult({ "nInserted" : 1 })
> db.things.find()
{ "_id" : ObjectId("56ca09b5d536b4526d219ba8"), "fruit" : "apple" }
{ "_id" : ObjectId("56ca095ed536b4526d219ba7"), "name" : "John Doe", "age" : 24 }
```

https://docs.mongodb.org/getting-started/shell/