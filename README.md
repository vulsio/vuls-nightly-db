# vuls-nightly-db

Lock files for the vuls DBs published to `ghcr.io/vulsio/vuls-nightly-db`.

A lock is the recipe of one published DB: which extracted branch it reads
and, for every data source in the DB, the commit of
`ghcr.io/vulsio/vuls-data-db:vuls-data-extracted-<name>` it is built from.

| Lock | DB | Built by |
| --- | --- | --- |
| `db-nightly.lock.json` | `:nightly` | `db-nightly.yml` in [vulsio/vuls-data-db](https://github.com/vulsio/vuls-data-db) |

```json
{
  "branch": "nightly",
  "registry": "ghcr.io/vulsio/vuls-data-db",
  "sources": [
    {"name": "alma-errata", "commit": "<40 hex>"}
  ]
}
```

The same DB can be rebuilt anywhere from a lock at any commit of this
repository:

```sh
vuls db build --lock db-nightly.lock.json --dbpath ./vuls.db
```

## Who changes a lock

- **The per-source guard** in vuls-data-db, after each extract: when the
  source's change passes the diff guard, it moves that source's `commit` to
  the extracted head. It changes nothing else.
- **`approve.yml`** in vuls-data-db, run by hand: sets a source's `commit`
  (e.g. after a guard failed on a change judged correct, or to roll a source
  back).
- **Pull requests here**: adding or removing a source (and reordering). A
  source added here also needs an entry in `db-guard.json` in vuls-data-db.
  Check the file before opening the PR:

  ```sh
  vuls db build --lock db-nightly.lock.json --validate-only
  ```

Every commit records who or which run moved what. This repository has no
workflows of its own.
