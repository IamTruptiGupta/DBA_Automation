Task 4.1 
Index & Table Optimization Tool 
Objective
Reduce query latency by identifying missing, unused, and duplicate indexes in the PostgreSQL database. 
What is an Index?
An index is a data structure that allows PostgreSQL to find rows quickly without scanning the entire table. Without indexes, PostgreSQL performs a Sequential Scan — reading every row one by one. With an index, it performs an Index Scan — jumping directly to the relevant rows. Index = that catalog for your database.

Step 1 nano ~/flask_dashboard/app.py
Add before if name == "main"
CODE

        # Unused indexes
        cur.execute("""
            SELECT
                schemaname,
                tablename,
                indexname,
                pg_size_pretty(pg_relation_size(indexrelid)) AS index_size,
                idx_scan AS times_used
            FROM pg_stat_user_indexes
            WHERE idx_scan = 0
            ORDER BY pg_relation_size(indexrelid) DESC;
        """)
        unused = cur.fetchall()

        # All indexes with usage
        cur.execute("""
            SELECT
                t.tablename,
                i.indexname,
                pg_size_pretty(pg_relation_size(i.indexrelid)) AS size,
                s.idx_scan AS times_used,
                s.idx_tup_read AS tuples_read
            FROM pg_indexes i
            JOIN pg_stat_user_indexes s ON i.indexname = s.indexname
            JOIN pg_stat_user_tables t ON t.tablename = s.relname
            ORDER BY s.idx_scan DESC
            LIMIT 20;
        """)
        all_indexes = cur.fetchall()

        # Missing indexes — tables with high seq scans and no index scans
        cur.execute("""
            SELECT
                relname AS tablename,
                seq_scan,
                idx_scan,
                pg_size_pretty(pg_total_relation_size(relid)) AS table_size,
                n_live_tup AS live_rows
            FROM pg_stat_user_tables
            WHERE seq_scan > 50
              AND (idx_scan IS NULL OR idx_scan < seq_scan)
            ORDER BY seq_scan DESC
            LIMIT 10;
        """)
        missing = cur.fetchall()

finally:
    release_conn(conn)

return render_template("indexes.html",

Step 2 — Create indexes.html
nano ~/flask_dashboard/templates/indexes.html

Task 4.2
What is Vacuum?
When you DELETE or UPDATE a row in PostgreSQL, the old row is not immediately removed. 
It stays as a dead tuple — like a ghost row taking up space. Over time thousands of dead tuples pile up → database gets slow and bloated. 
VACUUM = cleaning up those ghost rows.

Step 3 — Create vacuum.html nano ~/flask_dashboard/templates/vacuum.html

Step 4 — Add navigation links in index.html Find your topbar links section and add these two new buttons: 

Step 5 — Run and test python3 app.py

                       unused=unused,
                       all_indexes=all_indexes,
                       missing=missing)
