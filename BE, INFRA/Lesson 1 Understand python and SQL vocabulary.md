## What is venv in python?
A venv is just a folder containing a Python executable (a symlink to the real one), an empty `site-packages` directory, and a `pyvenv.cfg` marker file. When Python starts and finds that `pyvenv.cfg`, it uses the venv’s `site-packages` instead of the system one, which is the whole trick: isolated packages per project, not a VM, container, or sandbox. `activate` only prepends the venv’s `bin/` to `PATH` (plus sets `VIRTUAL_ENV` and the prompt), so you can skip it and run `~/venv/bin/python` directly, which is handy for cron and systemd. Venvs aren’t portable (absolute paths baked in) and shouldn’t go in git; treat them as disposable and rebuild from `requirements.txt`.

```bash
python3 -m venv .venv            # create
source .venv/bin/activate        # activate (deactivate to leave)
pip install "psycopg[binary]"    # install into the venv
pip freeze > requirements.txt    # record deps
pip install -r requirements.txt  # rebuild later
```

Basically, different programs may have different dependencies, and to not messup the system level dependencies this is used. 




## What is psycopg?

- psycopg is the library python uses to interface with postgresql server. The flow of command looks like this from top to bottom:
```
your code
   │  Python API (DB-API 2.0 / PEP 249: connect, cursor, execute, fetch*, commit)
psycopg          ← Python package: types, cursors, transactions, parameter handling
   │  C function calls
libpq            ← Postgres's official C client library: implements the wire protocol
   │  bytes over a TCP socket (or Unix domain socket)
Postgres server  ← postmaster + one backend process per connection
```


- We need to have the "binary" version of psycopg installed because it comes with precompiled libpq. If we don't have it, then python will have to call this library by slowly compiling and interfacing using `ctypes`, which is slow. 


## How does it work with the db process?

Postgresql has its own application layer protocol called wire protocol. Bunch of messages in this protocol will be discussed. 

When we do `psycopg.connect(host,port)`, the libpq layer sends a tcp request to the database server program (postmaster). It then sends a **StartupMessage** with the protocol version (3.0), user, and database name. The server responds with an authentication request and then if it succeeds responds with **AuthenticationOk** message. 

Postmaster listens on port 5432 and forks a child process for each incoming database access request. Which is why it is expensive to have many open connections to db server at one point.  

And then the server sends some other data info after auth sending multiple messages with **ReadyForQuery** at the end.


## How does executing a SQL query work?
`conn.execute(sql, params)`
The Postgres wire protocol is a stream of typed messages: one byte for the type, a 4-byte length, then the payload. There are two query modes.

- **Simple query:** one `Query ('Q')` message containing the SQL text. Parameters aren’t supported, so the client would have to interpolate values into the string itself.
- **Extended query:** separate messages: `Parse` (SQL text with `$1`, `$2` placeholders), `Bind` (the parameter values), `Execute`, `Sync`.

**Psycopg 3 uses the extended protocol** when you pass parameters. It rewrites your `%s` placeholders into `$1`, `$2`, and sends the values in the `Bind` message, **separate from the SQL text**. So the query is parsed before the values exist, and a value can never be interpreted as SQL. That’s the real mechanism behind injection safety. (Psycopg 2 did client-side string escaping instead, which is more fragile. Many older tutorials describe that behavior, and it no longer applies.)

Here is the exchange for `conn.execute("SELECT * FROM runs WHERE id = %s", (5,))`:

```
client → Parse:   "SELECT * FROM runs WHERE id = $1"
client → Bind:    $1 = 5   (psycopg's int dumper converts the Python int)
client → Execute
client → Sync
server → ParseComplete, BindComplete
server → RowDescription: column names + type OIDs (e.g. int8, text, timestamptz)
server → DataRow ...     (one per result row)
server → CommandComplete: "SELECT 1"
server → ReadyForQuery
```

Inside the backend, the statement goes through **parse → analyze/rewrite → plan → execute**. The planner is what decides between a sequential scan and an index scan, and `EXPLAIN` (L9) shows you its choice.


## How do python types get to psql types?

Every Postgres type has an **OID**, a numeric type ID. `RowDescription` tells the client each column’s OID. psycopg keeps a registry of **loaders** (Postgres bytes → Python object, chosen by OID) and **dumpers** (Python object → Postgres bytes, chosen by Python type). So `timestamptz` arrives as a timezone-aware `datetime`, and `jsonb` as a `dict`. Values travel in either **text** or **binary** format; the default is text, and binary is optional per cursor.

## Multi Stage Commit of a Database modification
- `conn.execute()` is a shortcut: it creates a `Cursor`, runs the query on it, and returns the cursor. The cursor holds the result set the server sent back. `fetchone()` and `fetchall()` read from it. `fetchone()` returns a tuple, using the loaders above.
- By default psycopg is **not in autocommit mode**. Before your first statement it sends `BEGIN`, so all your statements run inside one server-side transaction. `conn.commit()` sends `COMMIT`; `conn.rollback()` sends `ROLLBACK`. If the connection closes without a commit, the backend rolls the transaction back. That’s why an uncommitted INSERT vanishes. `conn.pgconn.transaction_status` tells you the current state (idle, in transaction, in error).
- Using `with psycopg.connect(...) as conn:` commits on a clean exit, rolls back on an exception, and closes the connection.
- Correction to my earlier wording: a cursor isn’t a “pointer to the answer”, it’s an object holding the result set and a position within it.
  
  


