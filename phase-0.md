PHASE 0 — ENVIRONMENT SETUP

Install PostgreSQL
-sudo apt update
-sudo apt install postgresql -y

Check version
-psql --version

Change port = 5433
-sudo nano /etc/postgresql/18/main/postgresql.conf

-sudo systemctl restart postgresql

Verify port
-ss -lntp | grep 5433

sudo nano /etc/postgresql/18/main/postgresql.conf
change: data_directory = '/pgdata'

sudo systemctl restart postgresql

Verify data directory
-sudo -u postgres psql -c "show data_directory;"

Enable logging
-sudo nano /etc/postgresql/18/main/postgresql.conf
-logging_collector = on
-log_connections = on
-log_min_duration_statement = 1000

-sudo systemctl restart postgresql

Set password
-sudo -u postgres psql
-\password postgres
-\q

Login
-psql -h 127.0.0.1 -p 5433 -U postgres -d postgres
