qmongo
======

This repo contains some experimental code for talking to a mongodb database.
It is a branch off the [qbson](https://github.com/andrasq/node-qbson) BSON coders,
which itself was a spin-off from the 2016 Kinvey Spring Hackathon.

This is a fork of [node-qbson](https://github.com/andrasq/node-qbson) that
included all the early development.

The two primary modules in this package are `qmongo/qmongo` and `qmongo/qm`.
The former is a Cursor-ed mongodb driver that supports batched data streaming.
The latter is a simple query-response driver that returns the matching documents
(up to the specified limit, or up to 2^31-1 by default).

qm
--

See [qm](./qm.md)

Related Work
------------

- mongodb - the official nodejs mongodb driver
- mongolian - alternate mongodb driver (no longer maintained)
- [qbson](https://github.com/andrasq/node-qbson) - the BSON library used by qmongo
