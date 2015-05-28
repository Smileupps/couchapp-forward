# Couchapp requests forwarder
https://github.com/Smileupps/couchapp-forward

This design document receives and forwards requests, using couchdb root as base starting path.

## USE CASES

- to restrict a set of requests only to specific usernames/roles
- example: ddoc "/smileupps/_design/_couchos/" provides both a couchapp editor and a restricted version of futon/fauxton, which prevents all guests or regular users to invoke usually unrestricted couchdb handlers (_all_dbs, _all_docs, ...). This allows to safely use futon or fauxton, being sure none can access your database list or retrieve documents stored within unrestricted databases.

## HOW TO 

1. put this ddoc "_design/_forward" into the restricted database with name *restricted*

2. within your main application ddoc "mydb/_design/myddoc", add these rewriting rules:
"rewrites":[
  ...
  {
    "from": "/public/*",
    "to": "../../../restricted/_design/_forward/_rewrite/mydb/_design/myddoc/_rewrite/*"
  },
  {
    "from": "/*",
    "to": "../../../*"
  },
  ...
]

3. From your smileupps control panel or within your couchdb configuration, create a new domain/vhost with the following path:
[vhosts]
restricted.smileupps.com = /mydb/_design/myddoc/_rewrite/public/

4. This allows all requests to restricted.smileupps.com, to be correctly forwarded to the second rule only if user is authenticated and allowed on *restricted* db

## RESTRICTED DATABASES:

As *restricted* database you can use:
* *_users*, which is by default allowed to administrators only
* a new dummy database, used only as a kind of firewall, with the [_security object](http://docs.couchdb.org/en/latest/api/database/security.html#api-db-security) set up to allow only your own specific usernames/roles


