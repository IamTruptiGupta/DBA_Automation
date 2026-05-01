PHASE 1 — CORE DBA AUTOMATION

Login as postgres
-sudo -u postgres psql -p 5433

Create database
-CREATE DATABASE appdb;

Create roles
-CREATE ROLE app_ro LOGIN PASSWORD;
-CREATE ROLE app_rw LOGIN PASSWORD;
-CREATE ROLE app_admin LOGIN PASSWORD;

Password expiry
-ALTER ROLE app_ro VALID UNTIL '2026-12-31';

Grant connection
-GRANT CONNECT ON DATABASE appdb TO app_ro, app_rw, app_admin;

Switch DB
-\c appdb

Create schema
-CREATE SCHEMA app_schema AUTHORIZATION app_admin;

Revoke public access
-REVOKE ALL ON SCHEMA public FROM PUBLIC;

Read-only permissions
-GRANT USAGE ON SCHEMA app_schema TO app_ro;
-GRANT SELECT ON ALL TABLES IN SCHEMA app_schema TO app_ro;

Read-write permissions
-GRANT USAGE ON SCHEMA app_schema TO app_rw;
-GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA app_schema TO app_rw;

Admin permissions
-GRANT ALL PRIVILEGES ON SCHEMA app_schema TO app_admin;

Validation — Admin create table
-SET ROLE app_admin;
-CREATE TABLE app_schema.verify_table(id INT);
-RESET ROLE;

Validate table
-\dt app_schema.*

Check connections
-SELECT usename, datname, state FROM pg_stat_activity;

Exit
-\q
