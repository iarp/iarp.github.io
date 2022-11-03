# PostgreSQL

## Setting and resetting sequences and ids

```sql
SELECT setval('django_migrations_id_seq', 161, true);
SELECT setval(pg_get_serial_sequence('sk_season', 'id'), max(id)) FROM sk_season;
```

## Backup Database with date

```sql
docker exec Postgres11 pg_dump --clean -h localhost -U postgres -d database > /mnt/backups/database/$(date +%Y-%m-%d-%H.%M).sql
```

## Restore database

Add `-1` (number one) to force entire thing into transaction that will rollback with any errors

```sql
docker exec Postgres11 psql -h localhost -U postgres -d database -f /mnt/backups/database/....sql
```


## List all databases

```sql
docker exec Postgres11 psql -U postgres -c 'SELECT pg_database.datname as "Database" FROM pg_database' | grep -Ev "(Database|----------------|postgres|template1|template0|\([0-9]+ rows\))"
```
