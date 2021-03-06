# fragments-postgres

*the documentation in this readme is work in progress and currently unfinished !*

[![BETA](http://img.shields.io/badge/Stability-BETA-orange.svg?style=flat)]()
[![NPM Package](https://img.shields.io/npm/v/fragments-postgres.svg?style=flat)](https://www.npmjs.org/package/fragments-postgres)
[![Build Status](https://travis-ci.org/snd/fragments-postgres.svg?branch=master)](https://travis-ci.org/snd/fragments-postgres/branches)
[![Dependencies](https://david-dm.org/snd/fragments-postgres.svg)](https://david-dm.org/snd/fragments-postgres)

**postgres for [fragments](https://github.com/snd/fragments): migration commands, auto generated [mesa](https://github.com/snd/mesa) tables, data accessor functions and more**

[you should probably first read what fragments is all about !](https://github.com/snd/fragments)

also see [fragments-user](https://github.com/snd/fragments-user)
as an example app that uses fragments-postgres extensively.

just add fragments-postgres as a source to your fragments application. for example like this:
``` javascript
#!/usr/bin/env node

var hinoki = require('hinoki');
var fragments = require('fragments');
var fragmentsPostgres = require('fragments-postgres');

var source = hinoki.source([
  __dirname,
  fragments.source,
  fragments.umgebung,
  fragmentsPostgres
]);

source = hinoki.decorateSourceToAlsoLookupWithPrefix(source, 'fragments_');

module.exports = fragments(source);

if (require.main === module) {
  module.exports.runCommand();
}
```

### migration commands

```
migrations:create {migration-name} - create a new migration in directory that is in envvar `MIGRATION_PATH`
pg:create - create database whose name is in envvar `POSTGRES_DATABASE`
pg:drop - drop database whose name is in envvar `POSTGRES_DATABASE`
pg:drop-create - drop and then create database whose name is in in envvar `POSTGRES_DATABASE`
pg:drop-create-migrate - drop and then create database whose name is in in envvar `POSTGRES_DATABASE` and then apply all migrations in directory that is in envvar `MIGRATION_PATH` to database whose url is in envvar `DATABASE_URL`
pg:migrate [--verbose] [--dry] - migrate: apply all migrations in directory that is in envvar `MIGRATION_PATH` to database whose url is in envvar `DATABASE_URL`
```

### mesa

key `mesa` maps to a [mesa object](https://github.com/snd/mesa) that connects
to the database reachable under envvar `DATABASE_URL` using a connection pool
of size envvar `POSTGRES_POOL_SIZE`.

### auto generated mesa tables

key `userTable` maps to a [mesa object](https://github.com/snd/mesa)
that is made from [`mesa`](#mesa) above to use table `user`: `mesa.table('user')`.
`urlSnapshotTable` uses table `url_snapshot`: `mesa.table('url_snapshot')`.
you get the idea.

### auto generated data accessor functions

key `firstUserWhereId` maps to a function which
returns a promise that will resolve to the first
record from table `user` where column `id` matches the argument `id`
in the database reachable under envvar `DATABASE_URL`.
it runs the following mesa query: `userTable.where({id: id}).first()`.
use any table name or column name. chain multiple where clauses.

`selectUrlSnapshotWhereIsDead(false)` returns a promise that will resolve to all
records from table `url_snapshot` where column `is_dead` is `false`.
it runs the following mesa query: `urlSnapshotTable.where({is_dead: false}).select()`.
use any table name or column name. chain multiple where clauses.

`deleteUserWhereId(id)` works as expected.
it runs the following mesa query: `userTable.where({id: id}).delete()`.
use any table name or column name. chain multiple where clauses.
at least one where clause is required for delete.

if you need more complex queries use the [mesa tables](#auto-generated-mesa-tables) directly.

## [license: MIT](LICENSE)
