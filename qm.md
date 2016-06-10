qmongo-lite
===========

Greatly simplified no-frills fast mongodb driver for nodejs.  A large part of the
speedup is from reduced memory pressure and faster garbage collection.

This is a ground-up rewrite, it is not a wrapper for (in fact, does not share any
code with) the official MongoDB nodejs [mongodb](https://npmjs.org/package/mongodb)
driver.  All the code for talking to the database is in `qm.js` (575 lines) and in
the related [`qbson`](https://github.com/andrasq/node-qbson) BSON encode/decode
functions.

        var qm = require('qmongo/qm');
        var mongoUri = "mongodb://user:pass@localhost/database";
        qm.connect(mongoUri, function(err, db) {
            db.useCollection('admin', 'users');
            db.find({deleted: {$ne: true}}, function(err, docs) {
                // got docs.length matching non-deleted documents
            });
        });

Api
---

### qm.connect( url, options, callback(err, qm) )

Connect to the db named by the url.  The url format is `"mongodb://<user>:<pwd>@<host></database>"`.
The leading `"mongodb://"` and the hostname `"@<host>"` are required.  Returns a qm database client.

### db.useCollection( [dbName,] collectionName )

Make the subsequent commands operate on the named collection (or, optionally,
the named collection in the named database).  This setting persists for this
`qm` object until next changed.  The default database and collection are
`test` and `test`, respectively.  Returns the `db` for chaining to the next call.

### db.find( query, options, callback(err, documents) )

Find documents in the current collection in use.  Returns the found documents to
its callback.  If no callback is given, returns a closure with a `toArray` method
that will deliver the matching documents to its callback when available.

Results are not batched and must fit into a single message (default 48 mb).

Options:
- skip - omit the first N matches (default 0)
- limit - return no more than M matches (default all)
- raw - return the raw un-decoded BSON entities, not JS objects

### db.runCommand( cmd, callback(err, reply) )

Execute the mongodb [database command](https://docs.mongodb.com/manual/reference/command/)
against the current database and return the results.
