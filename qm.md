qmongo-lite
===========

Greatly simplified no-frills fast mongodb driver for nodejs.  A large part of the
speedup is from reduced memory pressure and faster garbage collection.

        var Qm = require('qbson/qm');
        var qm = new Qm(socket, options);
        qm.auth(username, password, database, function(err, db) {
            db.useCollection('admin', 'users');
            db.find(
        })

Api
---

### qmongo.connect( url, options, callback(err, qm) )

Connect to the db named by the url.  The url format is `"mongodb://<user>:<pwd>@<host></database>"`.
The leading "mongodb://" and the hostname `"<host>"` are required.

### qm.useCollection( [dbName,] collectionName )

Make the subsequent commands operate on the named collection (or, optionally,
the named collection in the named database).  This setting persists for this
`qm` object until next changed.  The default database and collection are
`test` and `test`, respectively.

### qm.find( query, options, callback(err, documents) )
### qm.find( query, options ).toArray( callback(err, documents) )

Find documents in the collection currently in use.

Options:
- skip
- limit
- raw

### qm.runCommand( cmd, args, callback(err, reply) )
