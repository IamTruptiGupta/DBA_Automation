PHASE 2 — BACKUP & RESTORE

Create backup directory
-sudo mkdir -p /pgbackups/full
-sudo chown -R postgres:postgres /pgbackups

Take full database backup
-sudo -u postgres /usr/lib/postgresql/18/bin/pg_dump \
-F c -d appdb \
-f /pgbackups/full/appdb_full_$(date +%F).dump

Verify backup file
-ls /pgbackups/full/

Create test database for restore
-sudo -u postgres psql -p 5433
-CREATE DATABASE appdb_restore_test;
-\q

Restore backup into test DB
-sudo -u postgres /usr/lib/postgresql/18/bin/pg_restore \
-p 5433 -d appdb_restore_test \
/pgbackups/full/appdb_full_$(date +%F).dump

Verify tables after restore
-sudo -u postgres psql -p 5433 -d appdb_restore_test
-\dt

Check specific schema tables
-\dt app_schema.*

Verify data inside table
-SELECT * FROM app_schema.verify_table;

List all relations
-\d

Exit
-\q
