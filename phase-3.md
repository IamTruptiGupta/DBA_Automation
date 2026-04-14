PHASE 3 — MONITORING & OBSERVABILITY

Navigate to project folder
-cd ~/flask_dashboard

Activate virtual environment
-source venv/bin/activate

Install dependencies (if not already)
-pip install flask psycopg2-binary

Run Flask application
-python3 app.py

Open browser
-http://localhost:5000

Check active & idle connections
(Displayed on dashboard using pg_stat_activity)

Check long-running queries
(Displayed using pg_stat_activity with query duration)

Check blocking locks
(Displayed using pg_locks + pg_stat_activity)

Check database size
(Displayed using pg_database)

Check table size ranking
(Displayed using pg_stat_user_tables)

Kill long-running query (if needed)
-Click "Kill" button in dashboard
OR
-run SQL manually:
SELECT pg_terminate_backend(pid);

Auto-refresh dashboard
(Page refreshes every 5 seconds)

Stop Flask server
-Press CTRL + C

Deactivate environment
-deactivate
