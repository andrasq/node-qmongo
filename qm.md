qmongo-lite
===========

Greatly simplified no-frills fast mongodb driver for nodejs.

This is a ground-up rewrite, it is not a wrapper for the nodejs
[mongodb](https://npmjs.org/package/mongodb) driver.  All the code for talking to
the database is in `qm.js` (575 lines) and in the related
[`qbson`](https://github.com/andrasq/node-qbson) BSON encode/decode functions.

The functionality is narrowly focused:  talk to a single mongod server over a
single socket, send it commands, and return the responses.  Commands and responses
are pipelined, but are executed by mongod in order.

Unlike the official driver, this version comes in at 1/20 th its size by using just
a single socket, having no automatic reconnect, no built-in cluster support, no
query cursor, no batching, no data streaming.  Some of that is rarely useful,
and all of it is fairly easy to implement in a separate layer.

(The related [`qmongo`](qmongo.js) driver is slightly larger but does support
reconnects, cursors and data streaming.  They are both different experiments,
this one is in minimalism.)

        var qm = require('qmongo/qm');
        var mongoUri = "mongodb://user:pass@localhost/database";
        qm.connect(mongoUri, function(err, db) {
            db.useCollection('admin', 'users');
            db.find({deleted: {$ne: true}}, function(err, docs) {
                // got docs.length matching non-deleted users
            });
        });


Api
---

### qm.connect( url, options, callback(err, db) )

Connect to the db named by the url.  The url format is `"mongodb://<user>:<pwd>@<host></database>"`.
The leading `"mongodb://"` and the hostname `"@<host>"` are required.  Returns a qm database client.

### db.useCollection( [dbName,] collectionName )

Make the subsequent commands operate on the named collection (or, optionally,
the named collection in the named database).  This setting persists for this
`db` object until next changed.  The default database and collection are
`test` and `test`, respectively.  Returns the `db` for chaining to the next call.

### db.find( query, options, callback(err, documents) )

Find documents in the current collection.  Returns the found documents to
its callback.  If no callback is given, returns a closure with a `toArray` method
that will deliver the matching documents to its callback when available.

Results are not batched and must fit into a single message (default 48 mb).

Find uses version 0 of the MongoDB wire protocol, and offers the options:
- skip - omit the first N matches (default 0)
- limit - return no more than M matches (default all)
- raw - return the raw un-decoded BSON entities, not JS objects

### db.runCommand( cmd, callback(err, reply) )

Execute the mongodb [database command](https://docs.mongodb.com/manual/reference/command/)
against the current database and return the result object.  The result will have a field `ok`
that indicates whether the command succeeded.

### db.close( )

Close the connection to MongoDB.
