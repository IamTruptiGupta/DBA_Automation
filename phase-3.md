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


**Task 3.2**
**Slow query analyzer** Step 1 — Enable pg_stat_statements first 
Find your config file: sudo find / -name "postgresql.conf" 2>/dev/null 
Open it: sudo nano /etc/postgresql/15/main/postgresql.conf
change these two lines: shared_preload_libraries = 'pg_stat_statements'
log_min_duration_statement = 1000 
After that Save 
then restart: sudo systemctl restart postgresql

Step 2 — Enable the extension in your database: sudo -u postgres psql -p 5432 -d appdb sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements; \q

Step 3 — Add Task 3.2 to your app.py 
Open your file: nano ~/flask_dashboard/app.py
Add these two new routes at the bottom, just before if name == "main":
##CODE##
@app.route("/slowqueries") def slow_queries(): conn = get_conn() try: with conn.cursor() as cur:

        # Top slow queries by average time
        cur.execute("""
            SELECT
                left(query, 100),
                calls,
                round(total_exec_time::numeric, 2) AS total_ms,
                round(mean_exec_time::numeric, 2) AS avg_ms,
                round(stddev_exec_time::numeric, 2) AS stddev_ms,
                rows
            FROM pg_stat_statements
            WHERE query NOT LIKE '%pg_stat%'
            ORDER BY mean_exec_time DESC
            LIMIT 15;
        """)
        slow = cur.fetchall()

        # Most executed queries
        cur.execute("""
            SELECT
                left(query, 100),
                calls,
                round(total_exec_time::numeric, 2) AS total_ms,
                round(mean_exec_time::numeric, 2) AS avg_ms,
                rows
            FROM pg_stat_statements
            WHERE query NOT LIKE '%pg_stat%'
            ORDER BY calls DESC
            LIMIT 15;
        """)
        most_called = cur.fetchall()

finally:
    release_conn(conn)

return render_template("slowqueries.html",
                       slow=slow,
                       most_called=most_called)
@app.route("/explain", methods=["POST"]) def explain(): from flask import request query = request.form.get("query", "") result = "" error = "" conn = get_conn() try: with conn.cursor() as cur: cur.execute("EXPLAIN ANALYZE " + query) rows = cur.fetchall() result = "\n".join(row[0] for row in rows) except Exception as e: error = str(e) conn.rollback() finally: release_conn(conn) return render_template("slowqueries.html", slow=[], most_called=[], explain_result=result, explain_error=error, explain_query=query)
Make html file 
Step 4 Create the new template file 

Step 5 — Add a link to Task 3.2 in your main dashboard 
Open templates/index.html: 
nano ~/flask_dashboard/templates/index.html Find the topbar section and add this link next to the Live badge: html Slow Query Analyzer

Step 6 — Run and test python3 app.py 
