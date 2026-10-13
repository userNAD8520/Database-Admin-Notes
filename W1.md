I'll read both uploaded files to see what topics they cover.


I've read both files. File 1 is your 4-part exam-prep template; File 2 is the MySQL Weeks 1–7 topic list — the exact scope of your Database Admin midterm (Wed Oct 28). I'll apply the template to those topics.

**Three assumptions (stated once, then I proceed):**
1. Each **Week = one numbered Topic**, so there are 7 Topics in Part 1.
2. A **MySQL server is not installed in this sandbox**, so SQL "output" blocks are MySQL 8.0/8.4 *documented* results — I flag anything version-sensitive rather than invent output.
3. Where your source lists an MCQ but the **options weren't pasted**, I give the governing principle + the likely intended answer instead of guessing blindly.

---

# 🔵 PART 1 — EXTENSIVE NOTES

# 🟢 TOPIC 1. Week 1 — Introduction to Databases & MySQL   [Exam Likelihood: 🟠 Medium]

**1. Big Picture (2–4 sentences)**
This week frames everything the rest of the course builds on: *what* a database is, *which* engines dominate the industry, and *how MySQL is licensed*, because licensing determines what you may legally deploy and sell. It also seeds the four numbers/paths every exam loves: **port 3306**, the **Linux `my.cnf`**, the **Windows `my.ini`**, and the **default schema set**. If you can recite the licensing model and the RDBMS-vs-NoSQL split cold, you bank the "easy marks" that appear as multiple-choice on nearly every test.

**2. Key Terms and Definitions**

| Term | Precise definition | Common confusion with |
|---|---|---|
| RDBMS | Relational Database Management System — stores data in tables (relations) of rows/columns and is queried with SQL | "database" itself (the RDBMS is the *engine*/*server*; the database is a namespace of tables) |
| SQL | Structured Query Language — the declarative language used to define and query relational data | MySQL (SQL is the *language*; MySQL is one *implementation* of it) |
| NoSQL | Non-relational databases (document, key-value, wide-column, graph) that trade strict schemas/joins for scale and flexibility | "no SQL at all" (many support SQL-like query languages) |
| Open source | Source code is publicly available and licensed for reuse/modification (MySQL = GPLv2) | "free of charge" (open source ≠ zero cost in all cases; "free as in speech") |
| Proprietary | Closed-source, license-controlled commercial software (Oracle, MS SQL Server) | "not free" (some proprietary products are free for limited use) |
| GPLv2 | GNU General Public License v2 — the license MySQL Community Edition ships under; copyleft | "public domain" (GPL has conditions, esp. on redistribution) |
| Schema | In MySQL, a synonym for a *database* (a container for tables/views/routines) | "schema" in other systems = a table structure / DDL definition |
| Instance | A running `mysqld` server process managing one data directory | "database" (one instance can host many databases) |

**3. Core Concepts Explained**

**Why so many DB engines exist.** Different workloads optimise different things. Transactional OLTP systems (banking, order systems) want consistency and durability → relational engines. High-volume, loosely-structured data (logs, catalogs, real-time feeds) wants horizontal scale and flexible schemas → NoSQL. MySQL sits firmly in the relational, OLTP-first camp, but MySQL 8 also stores **JSON documents**, blurring the line.

**The dominant databases** (industry "top 10" by usage, per DB-Engines-style rankings): **Oracle, MySQL, Microsoft SQL Server, PostgreSQL, MongoDB, Redis, SQLite, MariaDB, Elasticsearch, IBM Db2.** What they share: Oracle/SQL Server are proprietary and enterprise-priced; MySQL/PostgreSQL/MariaDB/SQLite are open source; MongoDB/Redis are NoSQL.

```
                 Databases
        ┌────────────┴─────────────┐
     Relational (SQL)          Non-relational (NoSQL)
   ┌──────┬──────┬──────┐    ┌───────┬────────┬────────┐
 Oracle MySQL Postgres   MSSQL  MongoDB  Redis  Cassandra
 (prop.) (OSS)  (OSS)  (prop.)  (doc)   (kv)  (wide-col)
```

**MySQL licensing — the exam-critical part.** MySQL is dual-licensed:
- **Community Edition** → **GPLv2**. Free to use, including as the back end of a website, *even a commercial website*, as long as you don't redistribute MySQL itself as part of your product in a way that triggers copyleft.
- **Enterprise Edition** → **proprietary commercial** license, adds support, monitoring, backup tooling.
- Because the *server* is GPLv2 (not LGPL), if you **embed and redistribute** MySQL in your own product, GPL obligations can attach — which is exactly why Oracle offers commercial licenses.

> 📌 **MUST KNOW:** "Is MySQL free?" — **Yes** (Community Edition, GPLv2). "Is it open source or proprietary?" — **It is dual-licensed: open source (GPLv2) Community + proprietary Enterprise.**

**Connection basics.** The MySQL server listens on TCP port **3306** by default. The **client** program `mysql` connects with `-u` (user), `-p` (prompt for password), optionally `-h` (host) and `-P` (port, capital P).

**Config files.** Defaults live in an INI-style file: **`my.cnf` on Linux** (searched in `/etc/my.cnf`, `/etc/mysql/my.cnf`, and `~/.my.cnf`) and **`my.ini` on Windows** (e.g. `C:\ProgramData\MySQL\MySQL Server 8.0\my.ini`).

**4. Syntax / Commands / Formulas Reference**

| Command | What it does | Minimal example | Expected output |
|---|---|---|---|
| `mysql --version` | Print client version | `mysql --version` | `mysql  Ver 8.0.xx for Linux on x86_64 (MySQL Community Server - GPL)` |
| `mysql -u root -p` | Log in as root; `-p` prompts for password | `mysql -u root -p` | `Enter password:` then the `mysql>` prompt |
| `mysql -u u -p -h host -P 3306` | Connect to a remote host/port | `mysql -u mike -p -h 10.0.0.5 -P 3306` | interactive session on remote server |
| `SHOW VARIABLES LIKE 'port';` | Confirm listening port | `SHOW VARIABLES LIKE 'port';` | `| port | 3306 |` |
| `SELECT VERSION();` | Server version | `SELECT VERSION();` | `8.0.36` |
| config path (Linux) | main server config | `ls /etc/mysql/my.cnf` | path if present |
| config path (Windows) | main server config | `dir "C:\ProgramData\MySQL\MySQL Server 8.0\my.ini"` | path if present |

**5. Worked Examples (increasing difficulty)**

**Example 1 (basic) — verify the client and default port.**
```bash
mysql --version
```
```text
mysql  Ver 8.0.36 for Linux on x86_64 (MySQL Community Server - GPL)
```
*Why:* confirms client installed and which edition (Community → GPL).

```sql
mysql -u root -p -e "SELECT VERSION(); SHOW VARIABLES LIKE 'port';"
```
```text
+-----------+
| VERSION() |
+-----------+
| 8.0.36    |
+-----------+
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| port          | 3306  |
+---------------+-------+
```
*Why:* `-e "..."` runs statements non-interactively — perfect for scripts and exams.

**Example 2 (intermediate) — connect to a non-default port.**
```bash
mysql -u root -p -P 3305
```
```text
Enter password: ********
Welcome to the MySQL monitor.  Commands end with ; or \g.
...
mysql>
```
*Why:* capital `-P` is **port**; lowercase `-p` is **password**. Mixing these up is the #1 typo (see traps).

**Example 3 (exam-style) — licensing reasoning.**
> *Scenario:* You build a commercial web app hosted on your own servers, using MySQL Community Edition as the database. Then a client asks for a copy of your *standalone product* bundled with MySQL to run on their own hardware. Can you ship it? 
> **Answer:** Community Edition is GPLv2 (copyleft). Shipping a *bundled* MySQL inside a distributed product can oblige you to license your combined work under GPL or buy a **commercial/Enterprise license**. Using MySQL as a *back-end service* you operate yourself does **not** force you to open-source your app.

**6. Comparison Tables**

**SQL (Relational) vs NoSQL**

| Aspect | Relational (SQL) | NoSQL |
|---|---|---|
| Data model | Tables, rows, fixed columns | Documents, key-value, wide-column, graph |
| Schema | Rigid, defined up front (DDL) | Flexible / schema-on-read |
| Query language | SQL (standardised) | Varies (Mongo query, CQL, etc.) |
| Scaling | Vertical (bigger server); sharding is manual | Horizontal (built to shard/partition) |
| Transactions | Strong ACID guarantees | Often eventual consistency (many offer tunable ACID now) |
| When to use | Structured data, integrity, reporting | Massive scale, fast-changing/unstructured data |
| Gotcha | Schema changes are migrations = downtime risk | No joins → data duplication; weaker integrity by default |

**Open source vs Proprietary**

| Aspect | Open source (MySQL Community, PostgreSQL) | Proprietary (Oracle, MS SQL Server) |
|---|---|---|
| Source | Public (GPLv2 for MySQL) | Closed |
| Cost | Free to run; paid *support* optional | License + support fees |
| Redistribution | Copyleft conditions apply | Contract controls it |
| When to use | Budget, flexibility, community | Enterprise support, vendor-certified stack |

**MySQL Community vs Enterprise**

| Feature | Community | Enterprise |
|---|---|---|
| License | GPLv2 | Commercial |
| Price | Free | Paid |
| MySQL Enterprise Backup/Monitor | ✗ | ✓ |
| Oracle support SLA | ✗ | ✓ |
| Core server features | ✓ (same engine) | ✓ |

**7. ⚠️ Traps, Quirks and Common Mistakes**

> ⚠️ **TRAP — `-p` vs `-P`.** `-p` = password prompt; `-P` = port. `mysql -u root -P` hangs awaiting a password. Correct: `mysql -u root -p` .
> ⚠️ **TRAP — "open source = public domain."** Wrong. GPLv2 is *copyleft*: it has redistribution conditions. This is exactly what exam licensing questions probe.
> ⚠️ **TRAP — "MySQL is either free OR proprietary."** It's **both**, via dual licensing. Don't pick one over the other.
> ⚠️ **TRAP — config filename confusion.** Windows uses **`my.ini`**; Linux uses **`my.cnf`**. Never swap them.
> ⚠️ **SOURCE-MATERIAL NOTE:** Your Week-1 sheet lists "What License does MySQL have?" **twice** (a duplication/typo in the course notes). The answer is unchanged: **GPLv2 (Community) / commercial (Enterprise)**.

**8. 💡 Memory Aids**

- **"3306"** → think *"3-3-0-6 = MySQL"*; SQL Server is 1433, PostgreSQL 5432.
- **"ini = WIndows, cnf = liNux"** → *Windows has the 'i', Linux has the 'c'* (i for "Installer/Windows", c for "conf/Linux").
- **GPL = "Give Public License"** → the source must stay open to recipients.
- **Ports = P** (capital) and **password = p** (small) → *"big P = Port, little p = pass."*

**9. 🎯 Exam Worthiness Analysis**

- **Likelihood: 🟠 Medium** — licensing + port + config paths are easy MCQs the instructor clearly loves (they're in the In-Class Quiz verbatim), but they're only a small slice of the 180-minute midterm.
- **Likely formats:** multiple choice, true/false, short answer ("name the default port / config files"), tiny CLI scenario.
- **Sub-points an examiner would test:** default port **3306**; **`my.cnf`=Linux / `my.ini`=Windows**; MySQL = **relational, SQL-based**; MySQL **free as a website back end**; MySQL **GPLv2 Community + proprietary Enterprise**; the meaning of `-u` and `-p`; a couple of top database names.
- **"I can ___" checklist:**
  - I can state MySQL's license model in one sentence (dual: GPLv2 + commercial).
  - I can name the default port and both config filenames from memory.
  - I can explain SQL vs NoSQL in 2 lines each.
  - I can decode `mysql -u root -p` flag-by-flag.
  - I can list ≥6 top databases and classify each as OSS or proprietary.

**10. Quick Self-Check**

1. **Default TCP port for MySQL?** → **3306.**
2. **Linux config file name?** → **`my.cnf`** (searched in `/etc/my.cnf`, `/etc/mysql/my.cnf`, `~/.my.cnf`).
3. **Is MySQL open source or proprietary?** → **Both — dual-licensed (GPLv2 Community; commercial Enterprise).**
4. **SQL vs NoSQL in one line?** → SQL = fixed-schema tables + strong ACID; NoSQL = flexible/scale-out, weaker default consistency.
5. **What does `-p` do in `mysql -u root -p`?** → **Prompts for the password** (never type the password inline — it leaks into shell history).

**📝 Provided Quiz Bank (Week 1 In-Class — answers)**

| # | Question | Answer | Why |
|---|---|---|---|
| 1 | MySQL default TCP port? | **3306** | compiled default |
| 2 | Linux config file? | **`my.cnf`** | Windows = `my.ini` |
| 3 | Windows config file? | **`my.ini`** | Linux = `my.cnf` |
| 4 | "MySQL is primarily a relational database based on SQL." | **True** | it's an RDBMS |
| 5 | Which is *not* a top database today? | pick the **legacy/desktop** option (e.g. MS Access/`dBase`); not Oracle/MySQL/PostgreSQL/MongoDB | those last ones are all top-tier |
| 6 | "MySQL is free to use as a back-end database for a website." | **True** | GPLv2/Community permits it |
| 7 | What is correct about `mysql -u root -p`? | `-u`=user **root**; `-p` **prompts** for password | not port, not plaintext password |

---

# 🟢 TOPIC 2. Week 2 — Architecture & System Management   [Exam Likelihood: 🔴 High]

**1. Big Picture (2–4 sentences)**
This week is the DBA's cockpit: how the server is *structured*, where its settings live, how it *starts and stops*, how clients reach it, and — the showpiece — how to **recover a lost root password**. It is the single most lab-heavy, highest-yield week, because the midterm's Lab section is built directly on these procedures. Master the config-file layering, case-sensitivity rules, `mysqld --verbose --help`, and the two password-reset workflows and you cover a large block of likely exam marks.

**2. Key Terms and Definitions**

| Term | Precise definition | Common confusion with |
|---|---|---|
| DBMS architecture | The layered client/server design: clients → connection/thread layer → SQL parser/optimiser → storage engine(s) → data files | "the data directory" (that's just on-disk storage) |
| Instance | One running `mysqld` server + its data directory | "database" |
| Daemon | Unix term for a background server process (`mysqld` on Linux) | "service" (Windows terminology, `MySQL80`) |
| Configuration file | INI file supplying startup defaults (`my.cnf`/`my.ini`) | "the data directory" |
| Variable | A server parameter (`SHOW VARIABLES`) that can be **dynamic** (SET at runtime) or **static** (needs restart) | "status" (reads-only counters via `SHOW STATUS`) |
| `lower_case_table_names` | System variable controlling identifier casing (0 = case-sensitive, 1 = lowercased/case-insensitive) | "keywords are case-sensitive" (they are **not**) |
| System schema | A pre-installed database MySQL needs to function (`mysql`, `information_schema`, `performance_schema`, `sys`) | "user database" (yours) |
| `caching_sha2_password` | Default auth plugin in MySQL 8 (SHA-256 based) | `mysql_native_password` (legacy SHA-1 based) |
| `--skip-grant-tables` | Start with privilege checks disabled (used in password recovery) | "grants are gone" (they're intact, just not enforced) |

**3. Core Concepts Explained**

**Architecture layers.** Clients connect over TCP/socket to `mysqld`. One **connection thread** per client. The **SQL layer** parses, optimises, and executes; the **storage-engine layer** (InnoDB default) handles rows on disk and in the **buffer pool** (RAM cache). Memory is allocated mostly to the **InnoDB buffer pool** and per-connection work areas. This is why "memory allocation + processes + storage structures" appear together as "DBMS architecture."

**Configuration layering (order of precedence, Linux):** global `/etc/my.cnf` → `/etc/mysql/my.cnf` (which `!includedir`s `/etc/mysql/mysql.conf.d/` and `conf.d/`) → `~/.my.cnf`. **Later/ more-specific files and later occurrences win.** On Windows, the installer writes `C:\ProgramData\MySQL\MySQL Server 8.0\my.ini`.

**Case sensitivity (very testable):**
- **Database/table names:** governed by `lower_case_table_names`. **Linux default = 0 → case-SENSITIVE.** **Windows default = 1 → stored lowercase, comparisons case-INSENSITIVE.** (A raw `.tar` Linux install can be configured otherwise, but defaults are what exams ask.)
- **Column names, aliases, and SQL keywords** (`SELECT`, `FROM`) are **case-insensitive everywhere.**

**System schemas installed by default (all platforms):** `mysql`, `information_schema`, `performance_schema`, `sys`. (Sample DBs `sakila`, `world`, `employees` are **not** default.)

**Starting/stopping, by platform:**

| Platform | Start | Stop | Status |
|---|---|---|---|
| Linux (systemd) | `sudo systemctl start mysql` | `sudo systemctl stop mysql` | `systemctl status mysql` |
| Linux (service) | `sudo service mysql start` | `sudo service mysql stop` | `sudo service mysql status` |
| Windows | `net start MySQL80` or Services (`services.msc`) | `net stop MySQL80` | Services console |

**Config changes need a restart.** Editing `my.ini`/`my.cnf` does nothing until the daemon reloads, which normally requires a **restart** (`restart` for systemd; stop+start on Windows).

**Auth plugins.** MySQL 8's default is **`caching_sha2_password`** (strong SHA-256, faster than `sha256_password`). The legacy **`mysql_native_password`** (SHA-1-based) is weaker and deprecated. So: *caching_sha2 IS more secure → True; "8's default is mysql_native_password" → False.*

**4. Syntax / Commands / Formulas Reference**

| Command | What it does | Example | Expected output |
|---|---|---|---|
| `mysqld --verbose --help` | Dump all server options + compiled defaults | `mysqld --verbose --help \| less` | long help/list of variables & defaults |
| `SHOW VARIABLES LIKE 'x%';` | List matching runtime variables | `SHOW VARIABLES LIKE 'secure_file_priv';` | one row: the folder path |
| `mysqladmin variables` | Dump variables via admin client | `mysqladmin -u root -p variables` | aligned `name  value` list |
| `SHOW DATABASES;` | List databases/schemas | `SHOW DATABASES;` | mysql, information_schema, performance_schema, sys (+yours) |
| `USE sys; SHOW TABLES;` | Switch schema, list tables | `USE sys; SHOW TABLES;` | ~100 `sys` views |
| `SELECT * FROM mysql.user\G` | Inspect user accounts (root only) | `SELECT user,host FROM mysql.user;` | rows for root, mysql.sys, etc. |
| `netstat -ltnp \| grep 3306` | Confirm listener (Linux) | `sudo netstat -ltnp \| grep 3306` | `tcp ... :3306 ... LISTEN ... mysqld` |
| `sudo service mysql restart` | Restart daemon (Linux) | `sudo service mysql restart` | `* Restarting MySQL database server mysqld  [ OK ]` |

**5. Worked Examples (increasing difficulty)**

**Example 1 (basic) — explore default schemas.**
```sql
SHOW DATABASES;
USE information_schema;
SHOW TABLES LIKE 'TABLES%';
```
```text
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
...
| TABLES             |
| TABLESPACES        |
```

**Example 2 (intermediate) — inspect startup variables without starting the server.**
```bash
mysqld --verbose --help | grep -E "^(port|datadir|socket|lower_case_table_names) "
```
```text
datadir                                  /var/lib/mysql/
lower_case_table_names                   0
port                                     3306
socket                                   /var/run/mysqld/mysqld.sock
```
*Why:* `--verbose --help` prints compiled-in defaults **before** the server reads live config changes — the canonical way to see "default settings and initialisation parameters."

**Example 3 (exam-style) — programmatic connectivity from Python.**
```bash
python3 --version
sudo apt install python3-pip -y
python3 -m pip install mysql-connector-python
```
```text
Python 3.12.3
...
Successfully installed mysql-connector-python-9.x
```
```python
# mysql-connect.py
import mysql.connector
conn = mysql.connector.connect(host="localhost", user="root", passwd="YourPass")
cur = conn.cursor()
cur.execute("SHOW VARIABLES like 'version'")
for row in cur.fetchall():
    print(row)
cur.close(); conn.close()
```
```text
('version', '8.0.36')
```
*Why:* cursor + `fetchall()` is the pattern to memorise; `passwd=` (not `password=`) is the connector's keyword.

**Example 4 (exam-style) — Windows root password reset via `--init-file`.**
```sql
-- reset.sql  (plaintext; delete afterwards!)
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPassword';
```
```bat
:: 1) Stop the service (Services.msc or:)
net stop MySQL80
:: 2) Start daemon in an ADMIN CMD with the init file
"C:\Program Files\MySQL\MySQL Server 8.0\bin\mysqld.exe" --defaults-file="C:\ProgramData\MySQL\MySQL Server 8.0\my.ini" --init-file="C:\reset.sql"
:: 3) In another admin CMD, verify login, then stop the daemon (Ctrl+C) and start the service normally
net start MySQL80
:: 4) DELETE reset.sql  <-- critical
```
*Why:* `--init-file` runs the SQL at startup, letting you change the password without ever authenticating. **`--defaults-file` must be the FIRST option.**

**Example 5 (exam-style) — Linux root password reset via safe mode.**
```bash
sudo service mysql stop
sudo mkdir -p /var/run/mysqld
sudo chown mysql:mysql /var/run/mysqld
sudo mysqld_safe --skip-grant-tables --skip-networking &
mysql -u root            # now connects with no password
```
```sql
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPassword';
```
```bash
ps -ef | grep 'mysqld'
sudo killall mysqld
sudo kill -9 <PID>       # only if killall didn't work
sudo service mysql start
mysql -u root -p         # verify new password
```
*Why:* `--skip-grant-tables` bypasses auth; `--skip-networking` keeps the insecure window off the network. `FLUSH PRIVILEGES` is required so `ALTER USER` is honoured in this mode. Cleanup the safe-mode process or you'll have two daemons fighting over the data directory.

**6. Comparison Tables**

**Case sensitivity**

| Identifier | Linux (default) | Windows (default) |
|---|---|---|
| Database/table name | **Case-SENSITIVE** (`lower_case_table_names=0`) | **Case-INSENSITIVE** (stored lowercase, `=1`) |
| Column name | case-insensitive | case-insensitive |
| SQL keyword | case-insensitive | case-insensitive |
| **Gotcha** | `Emp` ≠ `emp` on Linux → script breaks when moved from Windows | `Emp`/`emp` collide on Windows → two tables impossible |

**Auth plugins**

| Aspect | `caching_sha2_password` | `mysql_native_password` |
|---|---|---|
| Default in MySQL 8 | **Yes** | No (legacy/deprecated) |
| Hash | SHA-256 + server salt | SHA-1 |
| Security | Stronger (True in quiz) | Weaker |
| Compat | Needs modern client libs | Old clients may require it |

**Config change handling**

| Action | Effect |
|---|---|
| Edit `my.ini`/`my.cnf` | Nothing until reload |
| `SET GLOBAL var=x` | Immediate, lost on restart (dynamic vars) |
| Restart service | Applies file changes (needed for static vars) |

**Password reset: Windows vs Linux**

| Aspect | Windows (`--init-file`) | Linux (`--skip-grant-tables`) |
|---|---|---|
| Mechanism | Run SQL script at startup | Start with auth disabled |
| Extra flags | `--defaults-file`, `--init-file` | `--skip-grant-tables`, `--skip-networking` |
| Needs `FLUSH PRIVILEGES` | Usually no (init-file) | **Yes** |
| Cleanup | Delete plaintext SQL file | Kill safe-mode process |
| Security risk | Plaintext password on disk | Open/no-network window |

**7. ⚠️ Traps, Quirks and Common Mistakes**

> ⚠️ **TRAP — "just edit the config and it takes effect."** No: **restart required**. Quiz answer: *"We need to restart the service for config changes to take effect" = True.*
> ⚠️ **TRAP — case sensitivity.** MySQL 8 on Linux is case-**sensitive** for DB/table names (default 0); column names/keywords are always case-insensitive. Don't answer "all identifiers are case-insensitive."
> ⚠️ **TRAP — "system databases."** Only `mysql`, `information_schema`, `performance_schema`, `sys` are default. `sakila`/`world`/`employees` are **samples**, so a "which is NOT pre-installed" question usually targets one of those.
> ⚠️ **TRAP — default auth plugin.** MySQL 8 default = **`caching_sha2_password`, NOT `mysql_native_password`** (a favourite false statement).
> ⚠️ **TRAP — "only the Command Prompt can start/stop Windows MySQL."** **False** — you can use **Services.msc**, `net start/stop`, or the MySQL Installer.
> ⚠️ **TRAP — `--defaults-file` position.** It must be the **first** argument to `mysqld`, or it's ignored.
> ⚠️ **TRAP — leaving `reset.sql`.** A plaintext root password on disk is a security leak; the lab explicitly requires deleting it.
> ⚠️ **SOURCE AMBIGUITY:** *"Which of the following is false about when the following command is executed: `mysql`?"* — options weren't provided. The subtle truth: `mysql` with no flags connects as the **OS login user** (or errors if no such MySQL user), using the **socket**, and prints the version banner. Answer the intended option based on that behaviour.

**8. 💡 Memory Aids**

- **Case sensitivity:** *"Linux Loves Case; Windows Wusses Out"* (Linux case-sensitive, Windows case-insensitive).
- **System schemas = "MIPS"** — **M**ysql, **I**nformation_schema, **P**erformance_schema, **S**ys.
- **Auth plugin:** *"8 = SHA-2 (`caching_sha2`); old = SHA-1 (`native`)."*
- **Password reset:** *"Windows writes a file (`--init-file`); Linux opens the doors (`--skip-grant-tables`)."*
- **Config precedence:** *"later and closer (to the user) wins."*

**9. 🎯 Exam Worthiness Analysis**

- **Likelihood: 🔴 High** — it's the biggest lab block and the In-Class + Reading + Lab quizzes draw heavily from it.
- **Likely formats:** multiple choice, true/false, short answer, **command-line scenario** (reset root password), diagram order (reset steps), Python-connector code.
- **Sub-points an examiner would test:** restart-for-config; you *can* reset root if you can start/stop the service (**True**); case-sensitivity rules; which schemas are default; which auth plugin is default/more secure; `mysqld --verbose --help`; `netstat -ltnp`; Python connect + cursor + `fetchall()`; both password-reset workflows step-by-step.
- **"I can ___" checklist:**
  - I can name the four default schemas and the two config files.
  - I can state the case-sensitivity rule for Linux vs Windows.
  - I can identify `caching_sha2_password` as the MySQL 8 default and explain why it's stronger.
  - I can reset the root password on **both** Windows and Linux from memory.
  - I can write a 6-line Python script that connects, runs a query, and prints rows.
  - I can confirm the listener with `netstat -ltnp` and connect on a custom `-P` port.

**10. Quick Self-Check**

1. **Do config-file edits take effect live?** → **No — restart the service.**
2. **Is `emp` the same table as `Emp` on Linux?** → **No** (case-sensitive; `lower_case_table_names=0`).
3. **MySQL 8 default auth plugin?** → **`caching_sha2_password`.**
4. **Command to see compiled defaults/startup params?** → **`mysqld --verbose --help`.**
5. **First flag for a password reset with `--init-file` on Windows?** → **`--defaults-file`** (must come first).

**📝 Provided Quiz Bank (Week 2)**

| # | Question | Answer | Why |
|---|---|---|---|
| 1 | Restart needed for `my.ini`/`my.cnf` changes? | **True** | daemon reads config at startup |
| 2 | If you can start/stop the service, can you reset root's password? | **True** | `--init-file` / `--skip-grant-tables` bypass auth |
| 3 | Correct case-sensitivity statement? | Linux DB/table names **case-sensitive**; columns/keywords case-insensitive | `lower_case_table_names` |
| 4 | Which is **NOT** pre-installed? | the **sample** schema (e.g. `sakila`/`world`) | only MIPS are default |
| 5 | `caching_sha2_password` more secure than `mysql_native_password`? | **True** | SHA-256 vs SHA-1 |
| 6 | "As of MySQL 8 the default plugin is `mysql_native_password`." | **False** | default is `caching_sha2_password` |
| 7 (Reading) | Command-line backup command? | **`mysqldump`** | logical dump to `.sql` |
| 8 (Reading) | Which is **NOT** a storage engine? | the non-engine option (e.g. a non-InnoDB/MyISAM/CSV/MEMORY/BLACKHOLE name) | MySQL ships InnoDB/MyISAM/MEMORY/CSV/ARCHIVE/BLACKHOLE/etc. |
| 9 (Reading) | "MySQL 8 is the first version supporting JSON objects." | **False** | JSON type arrived in **5.7** |
| 10 (Reading) | Default engine as of MySQL 5.5? | **InnoDB** | InnoDB replaced MyISAM as default in 5.5 |
| 11 (Reading) | Which is **NOT** a supported Linux package installer? | the Windows/irrelevant option (e.g. **MSI**) | Linux uses RPM/DEB/apt/yum/tar |
| 12 (Reading) | "Only the Command Prompt can start/stop Windows MySQL." | **False** | Services.msc / `net start` / Installer |
| 13 (Reading) | "Which is not a command to check if MySQL runs on Linux?" | e.g. `mysqladmin version --local`-style non-status option; valid ones: `systemctl status mysql`, `service mysql status`, `ps -ef \| grep mysqld`, `netstat -ltnp` | pick the odd one out |
| 14 (Lab) | `mysql -u root` after `--skip-grant-tables`? | connects **without password** | auth disabled |

---



# 🟢 TOPIC 3. Week 3 — Data Import, Redirection & Recovery   [Exam Likelihood: 🔴 High]

**1. Big Picture (2–4 sentences)**
This week is about getting data **in** and getting it **back** when it's gone. You learn three import routes (shell redirection, the `source` command, MySQL Workbench), the industrial-strength bulk loader `LOAD DATA INFILE` with its delimiter/quote/line-ending controls, the security wall called `secure_file_priv`, and how to defend against SQL injection. Then it flips to disaster recovery: restoring a table from a dump, finding accidentally deleted rows with an outer join, and bulk-reinserting them with `INSERT INTO ... SELECT`. Import + recovery + injection are all heavily lab- and quiz-tested.

**2. Key Terms and Definitions**

| Term | Precise definition | Common confusion with |
|---|---|---|
| Redirection (`<`) | OS shell operator that feeds a file to a program's STDIN | the MySQL `source` command (runs inside the client) |
| Dump file | A `.sql` text file of statements that recreates a DB (schema + data) | a **binary** backup (physical file copy) |
| `mysqldump` | Client tool that *produces* a logical `.sql` dump | `mysql` (which *consumes* SQL) |
| `LOAD DATA INFILE` | Server-side bulk loader that reads a **server-local** text file into a table | `LOAD DATA LOCAL INFILE` (reads from the **client** machine) |
| `secure_file_priv` | Security variable restricting which directory `LOAD DATA INFILE`/`SELECT ... INTO OUTFILE` may use | a permission on the account (it's a server setting) |
| `@dummy` | A user variable used to *absorb* a file column you don't want to import | a real column (it's throwaway) |
| SQL injection | Attacker-supplied string that alters the structure of a query | "bad input validation" (broader; injection is the exploit) |
| Parameterized query | Query with `%s` placeholders + a params tuple, so data is never parsed as SQL | string concatenation (the vulnerable pattern) |
| Logical vs physical restore | Replaying SQL (`INSERT`) vs copying raw data files | "both are the same" |

**3. Core Concepts Explained**

**Three import methods**
```
METHOD 1 (shell redirection)         METHOD 2 (source, inside client)   METHOD 3 (Workbench)
mysql -u root -p -t < dump.sql       mysql> source dump.sql             File > Open SQL Script...
  └ OS feeds file to mysql STDIN        └ client reads file itself         └ GUI runs it, shows log
```
- **Method 1** is scriptable (best for automation/cron).
- **Method 2** shows per-statement feedback; runs inside an existing session; needs a path the *client* can read.
- **Method 3** is best for **large** files and browsing results, but is manual.

**What a dump looks like.** A `mysqldump` file typically contains: `DROP TABLE IF EXISTS`, `CREATE TABLE`, and `INSERT INTO ... VALUES (...), (...);` (multi-row). `DROP DATABASE IF EXISTS` suppresses the error when the DB doesn't exist. **There is no undo, no confirmation prompt, and no recycle bin** for `DROP DATABASE` — once it runs, the data is gone unless you have a backup.

**`LOAD DATA INFILE` — the bulk loader.** It maps a flat file's columns to a table's columns:
- `FIELDS TERMINATED BY` → the column separator (`'\t'` tab, `','` comma…)
- `OPTIONALLY ENCLOSED BY` → quote char around text fields (`'"'`)
- `LINES TERMINATED BY` → row separator. **Cross-platform trap:** Linux/Unix = `'\n'` (LF), Windows = `'\r\n'` (CRLF), classic Mac = `'\r'` (CR).
- `IGNORE n LINES` → skip a header/comment block.
- `(col1, @dummy, col3)` → map file columns; `@dummy` discards one.

**`secure_file_priv`.** For security, the server will only read files from one directory. Check it with `SHOW VARIABLES LIKE 'secure_file_priv';` and put your file there — **never disable the safeguard.** Typical values: Linux `/var/lib/mysql-files/`; Windows installer `C:\ProgramData\MySQL\MySQL Server 8.0\Uploads\`. An empty value = no restriction (insecure); `NULL` = disabled entirely.

**SQL injection & defence.** Building SQL by string concatenation lets user input change the query:
```python
# VULNERABLE
cur.execute("SELECT * FROM users WHERE name = '" + name + "'")   # name = "' OR 1=1 -- "
```
The fix is a **parameterized query** — the driver sends the SQL and the data separately:
```python
# SAFE
cur.execute("SELECT * FROM users WHERE name = %s", (name,))
```

**Documentation conventions (reading the MySQL manual).** `[]` = optional; `{ a | b }` = choose one; italics = a value you supply; `|` = "or"; repeated elements use `...`. Being able to *read* these is itself a tested skill.

**4. Syntax / Commands / Formulas Reference**

| Command | What it does | Minimal example | Expected output |
|---|---|---|---|
| `mysql -u root -p db < f.sql` | Import a dump (redirection) | `mysql -u root -p employees < emp.sql` | (silent on success) |
| `mysql -u root -p -t < f.sql` | Same, force **table** output | `mysql -u root -p -t < emp.sql` | non-interactive runs w/ table formatting |
| `mysql -u root -p > out.txt` | Capture results to file | `mysql -u root -p -e "SHOW DATABASES;" > out.txt` | file written |
| `source f.sql` | Import from inside client | `mysql> source EmployeeDB.sql` | `Query OK, N rows affected` lines |
| `mysqldump -u root -p db > f.sql` | Create a dump | `mysqldump -u root -p employees > emp.sql` | `.sql` file |
| `LOAD DATA INFILE '...' INTO TABLE t ...` | Bulk import | see below | `Query OK, N rows affected ...` |
| `SHOW VARIABLES LIKE 'secure_file_priv';` | Find allowed dir | `SHOW VARIABLES LIKE 'secure_file_priv';` | the path |
| `DROP DATABASE IF EXISTS d;` | Drop w/o error if absent | `DROP DATABASE IF EXISTS staging;` | `Query OK` |
| `INSERT INTO t SELECT ... FROM backup` | Bulk re-insert rows | `INSERT INTO employees SELECT * FROM backup.employees WHERE id BETWEEN 5 AND 9;` | `Query OK, N rows affected` |

**`LOAD DATA INFILE` full template:**
```sql
LOAD DATA INFILE '/var/lib/mysql-files/words.txt'
INTO TABLE dictionary.words
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(word, @dummy, length_of_word);
```

**5. Worked Examples (increasing difficulty)**

**Example 1 (basic) — import a dump via redirection.**
```bash
mysql -u root -p -t < /home/student/EmployeeDB.sql
```
```text
Query OK, 1 row affected
Query OK, 0 rows affected
...
```
*Why:* `-t` forces tabular output for the statements in the file; silent success = import worked.

**Example 2 (intermediate) — `LOAD DATA INFILE` with header skip and column mapping.**
File `words.txt` (tab-separated, one header row):
```text
word	rank	junk
apple	1	xx
banana	2	yy
```
```sql
CREATE DATABASE IF NOT EXISTS dictionary;
USE dictionary;
CREATE TABLE words (word VARCHAR(50), rank INT);
LOAD DATA INFILE '/var/lib/mysql-files/words.txt'
INTO TABLE words
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
IGNORE 1 LINES
(word, rank, @dummy);
SELECT * FROM words;
```
```text
+--------+------+
| word   | rank |
+--------+------+
| apple  |    1 |
| banana |    2 |
+--------+------+
```
*Why:* `IGNORE 1 LINES` drops the header; `@dummy` swallows the third file column.

**Example 3 (exam-style) — secure_file_priv blocks your import.** 
```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```
```text
+------------------+-----------------------+
| Variable_name    | Value                 |
+------------------+-----------------------+
| secure_file_priv | /var/lib/mysql-files/ |
+------------------+-----------------------+
```
```sql
LOAD DATA INFILE '/home/student/words.txt' INTO TABLE words ...;
```
```text
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option
so it cannot execute this statement
```
> **Fix:** copy the file into `/var/lib/mysql-files/` (Linux) or the `Uploads\` folder (Windows) and retry — **do not** turn the safeguard off.

**Example 4 (exam-style) — disaster recovery via `INSERT INTO ... SELECT`.** 
Realised rows 5–9 were accidentally deleted last night. A dated backup `employees_backup_2024_01_01.sql` was loaded into database `backup`.
```sql
-- Find rows present in backup but missing from production (accidental deletes),
-- while keeping newly created production rows.
SELECT b.*
FROM backup.employees b
LEFT JOIN production.employees p ON b.emp_no = p.emp_no
WHERE p.emp_no IS NULL;

-- Bulk-restore the missing rows
INSERT INTO production.employees
SELECT * FROM backup.employees b
WHERE b.emp_no BETWEEN 5 AND 9
  AND b.emp_no NOT IN (SELECT emp_no FROM production.employees);

-- Verify
SELECT COUNT(*) FROM production.employees;
SELECT * FROM production.employees WHERE emp_no BETWEEN 5 AND 9 LIMIT 10;

-- Cleanup
DROP DATABASE backup;
```
*Why:* the **LEFT JOIN ... WHERE p IS NULL** pattern isolates deleted rows; `INSERT ... SELECT` restores in bulk instead of row-by-row.

**Example 5 (exam-style) — parameterized query vs injection (Python).**
```python
import mysql.connector
conn = mysql.connector.connect(host="localhost", user="app", passwd="AppPass", database="shop")
cur = conn.cursor()

# VULNERABLE: user sends  ' OR '1'='1
bad = "SELECT * FROM customers WHERE name = '" + name + "'"
# SAFE:
cur.execute("SELECT * FROM customers WHERE name = %s AND active = %s", (name, 1))
print(cur.fetchall())
cur.close(); conn.close()
```
*Why:* placeholders keep data out of the SQL grammar, so `' OR '1'='1` is treated as a literal name, not logic.

**6. Comparison Tables**

**Three import methods**

| Aspect | Redirection `<` | `source` | Workbench |
|---|---|---|---|
| Runs where | OS shell | MySQL client | GUI |
| Scriptable | ✅ best | ⚠️ interactive | ❌ manual |
| Feedback | minimal | per-statement | full log pane |
| Best for | automation | quick in-session | **large** files |
| Gotcha | shell owns the file path | client must read the path | must open file first |

**`LOAD DATA INFILE` vs `LOAD DATA LOCAL INFILE`**

| Aspect | INFILE | LOCAL INFILE |
|---|---|---|
| File location | **Server** filesystem | **Client** filesystem |
| Governed by | `secure_file_priv` | `local_infile` (client+server) |
| Speed for remote | fast (server-side) | data shipped to server |
| Gotcha | file must sit in the allowed dir | disabled by default in 8.0 |

**Line terminators (cross-platform)**

| OS | Line ending | SQL literal |
|---|---|---|
| Linux/Unix | LF | `'\n'` |
| Windows | CRLF | `'\r\n'` |
| Classic Mac | CR | `'\r'` |
| **Gotcha** | Windows file on Linux with `'\n'` leaves stray `\r` on the last field | use the matching literal |

**String concat vs parameterized**

| Aspect | Concatenation | Parameterized |
|---|---|---|
| Injection-safe | ❌ | ✅ |
| Syntax | `"..." + x + "..."` | `"... %s", (x,)` |
| Placeholder | none | `%s` (connector) / `?` (other drivers) |
| When to use | **never** with user input | always |

**7. ⚠️ Traps, Quirks and Common Mistakes**

> ⚠️ **TRAP — `DROP DATABASE` gives no warning.** It executes immediately, no prompt, no undo, no recycle bin. `IF EXISTS` only suppresses the "doesn't exist" error.
> ⚠️ **TRAP — `secure_file_priv`.** The file must be in the allowed dir; a wrong path throws **ERROR 1290**. Do **not** disable the setting to "fix" it.
> ⚠️ **TRAP — Windows backslash escaping in SQL.** In `LOAD DATA INFILE 'C:\path\f.txt'`, `\` is an escape char → write `'C:\\path\\f.txt'` or use forward slashes `'C:/path/f.txt'`.
> ⚠️ **TRAP — LF vs CRLF.** A Windows CRLF file loaded with `LINES TERMINATED BY '\n'` leaves a trailing `\r` in the last column. Match the literal to the file.
> ⚠️ **TRAP — `mysql` redirection is a shell feature.** `mysql> mysql -u root -p < f.sql` **fails** — `<` only works at the OS shell, not inside the client (use `source` there).
> ⚠️ **TRAP — injection via concatenation.** Even "SELECT-only" code is exploitable with `OR 1=1` / `UNION` / `DROP TABLE`. Use parameters.
> ⚠️ **TRAP — replication won't save you from `DELETE`.** (Foreshadows W5): a human `DELETE` is *replicated*, so backups are your only defence.

**8. 💡 Memory Aids**

- **Import order:** *"**R**edirection, **S**ource, **W**orkbench = **RSW**"* (script → session → GUI).
- **`secure_file_priv`:** *"MySQL reads only from its own backyard."*
- **Line endings:** *"**L**inux = **L**F, **W**indows = CR**+**LF"* (Windows always adds the CR).
- **Recovery join:** *"LEFT JOIN + IS NULL = find the ghosts (deleted rows)."*
- **Injection fix:** *"Data goes in the tuple, never in the string."*

**9. 🎯 Exam Worthiness Analysis**

- **Likelihood: 🔴 High** — three separate quiz sets (In-Class + Lab) plus a multi-step lab.
- **Likely formats:** multiple choice, true/false, short answer, **command-line scenario** (which import flag?), fill-in-the-blank (`LOAD DATA` clause), code-writing (parameterized query), sequence (recovery steps).
- **Sub-points an examiner would test:** the command that reveals the import folder (`SHOW VARIABLES LIKE 'secure_file_priv'`); `DROP DATABASE` has no undo; `IF EXISTS` purpose; the redirection command for a named `.sql`; the three import methods; `FIELDS/LINES/ENCLOSED/IGNORE` clauses; `@dummy`; LEFT JOIN recovery; `INSERT ... SELECT`; parameterized queries.
- **"I can ___" checklist:**
  - I can import a dump three different ways from memory.
  - I can write a full `LOAD DATA INFILE` with delimiter, enclosure, line ending, header skip, and column mapping.
  - I can find and obey `secure_file_priv`.
  - I can explain why `DROP DATABASE` is irreversible.
  - I can recover deleted rows with a LEFT JOIN + `INSERT ... SELECT`.
  - I can rewrite a concatenated query as a parameterized one.

**10. Quick Self-Check**

1. **Statement that shows where files must be for `LOAD DATA INFILE`?** → **`SHOW VARIABLES LIKE 'secure_file_priv';`**
2. **Does `DROP DATABASE` prompt for confirmation?** → **No** — it's immediate and irreversible.
3. **Redirection command for `daily_backup_2017_10_01.sql`?** → **`mysql -u root -p < daily_backup_2017_10_01.sql`**
4. **How do you skip a header row?** → **`IGNORE 1 LINES`.**
5. **How do you prevent SQL injection?** → **Use parameterized queries** (`execute(sql, params)`), never string concatenation.

**📝 Provided Quiz Bank (Week 3)**

| # | Question | Answer | Why |
|---|---|---|---|
| 1 | Statement showing the import folder? | `SHOW VARIABLES LIKE 'secure_file_priv';` | reveals the allowed directory |
| 2 | `IF EXISTS` in `DROP DATABASE` purpose? | **True** — suppress error if DB absent | avoids error 1008 |
| 3 | Which is **NOT** an import method? | the non-method option (e.g. `cp`/`scp`/a GUI-unrelated action) | methods = redirection, `source`, Workbench, `LOAD DATA` |
| 4 | Redirection command for `daily_backup_2017_10_01.sql`? | `mysql -u root -p < daily_backup_2017_10_01.sql` | `<` = STDIN feed |
| 5 | "DROP DATABASE gives a warning + confirmation prompt." | **False** | no prompt |
| 6 | "There is no warning/undo/recycle bin for accidental DROP." | **True** | irreversible |
| 7 (Lab) | Do `my.ini` and `my.cnf` contain identical default parameters? | **False** | installer/defaults differ by platform |
| 8 (Lab) | "As of MySQL 8, default auth plugin is `mysql_native_password`." | **False** | `caching_sha2_password` |
| 9 (Lab) | Forgot root password — what to do? | **Reset** via `--init-file`(Win)/`--skip-grant-tables`(Linux) | recover, don't reinstall |
| 10 (Lab) | Which is **NOT** good after changing root password? | leaving the **plaintext password file** (and/or sharing it) | security leak |
| 11 (Lab) | Both OSes ship `information_schema, performance_schema, sys, mysql`? | **True** | MIPS are universal |
| 12 (Lab) | `caching_sha2_password` more secure? | **True** | SHA-256 vs SHA-1 |
| 13 (Lab) | "The Python MySQL Connector library..." | **True** — it lets Python talk to MySQL (via `mysql.connector`) | official driver |

---

# 🟢 TOPIC 4. Week 4 — Access Control & Role Management   [Exam Likelihood: 🔴 High]

**1. Big Picture (2–4 sentences)**
This is the security core of DBA work: **who** may log in (users), from **where** (host scoping), and do **what** (privileges), bundled into reusable **roles**. You learn why `root` is dangerous, how to classify privileges by risk, how to build least-privilege role profiles, and how `SET ROLE` (session) differs from `SET DEFAULT ROLE` (persistent). The exams lean hard on two confusions: **`*.*` vs `%.%`**, and **granting a role vs activating it**.

**2. Key Terms and Definitions**

| Term | Precise definition | Common confusion with |
|---|---|---|
| User account | Identity = **name + host**, e.g. `'sam'@'10.0.0.5'` | the username alone (host is part of identity!) |
| Host scope | The `@'host'` part limiting where login is allowed (`localhost`, `%`, an IP) | a network firewall rule |
| Privilege | A right to perform an operation (`SELECT`, `DROP`, …) | a role (a role is a *bundle* of privileges) |
| `USAGE` | Means **no privileges** — account can connect only | "unlimited usage" |
| `*.*` | All databases, all tables (grant scope) | `%.%` (invalid for grants) |
| `%` (host) | Any host (wildcard) | `*` (the DB/table wildcard) |
| Role | Named collection of privileges granted to users | a user (roles can't log in) |
| `SET ROLE` | Activate roles for the **current session** | `SET DEFAULT ROLE` (persists across logins) |
| `WITH GRANT OPTION` | Lets the grantee pass their privileges on | "ALL PRIVILEGES" (different) |
| Least privilege | Give only what's needed for the job | "convenience grants" |

**3. Core Concepts Explained**

**User identity = name + host.** `'sammy1'@'10.0.0.233'` and `'sammy1'@'10.44.33.102'` are **two different accounts** with independent passwords and privileges. Host `%` = any address; `localhost` = only the local socket/loopback; a specific IP = only that IP.

**Why root is dangerous.** `root` has all privileges plus `SUPER`/`FILE`/`SHUTDOWN`. Default `'root'@'localhost'` is **not** remote-accessible — this restriction is a deliberate security feature. Running daily work as root means any injected query or typo can drop your whole server.

**Privilege risk ladder.**

| Risk | Privileges | Note |
|---|---|---|
| 🟢 Harmless read | `SELECT` | can't change data, but may expose sensitive rows |
| 🟠 Modify | `INSERT`, `UPDATE` | alter data, not structure |
| 🔴 Destructive | `DELETE`, `DROP`, `ALTER`, `TRUNCATE` | irreversible data/schema loss |
| ⚫ Admin | `SUPER`, `FILE`, `PROCESS`, `SHUTDOWN`, `CREATE USER`, `WITH GRANT OPTION` | control the server |

**Role-based profiles (typical enterprise patterns).**
```
read-only      → GRANT SELECT ON db.*
app service    → SELECT,INSERT,UPDATE,DELETE on its tables (e.g. WordPress)
designer       → CREATE,ALTER,INDEX (+ SELECT)
backup user    → SELECT,LOCK TABLES,RELOAD,PROCESS
DBA (full)     → GRANT ALL PRIVILEGES ON *.*
```

**`SET ROLE` vs `SET DEFAULT ROLE` (the classic trap).**
- `GRANT 'hr' TO 'user'@'host';` → *assigns* the role to the account (does **not** activate it).
- `SET ROLE 'hr';` / `SET ROLE ALL;` → activates roles **for this session only**.
- `SET DEFAULT ROLE ALL TO 'user'@'host';` → makes the account's roles **auto-activate at login** (persistent). Without it (and without a session `SET ROLE`), the user has the role assigned but **inactive** → effectively no role privileges.
- Exemption form: `SET ROLE ALL EXCEPT 'hr';`.

**Column- and table-level scope.** MySQL 8 supports table-level and even **column-level** grants: `GRANT SELECT(first_name,last_name) ON hr.employees TO 'u'@'host';`.

**5. Syntax Reference** (given its centrality, syntax is expanded here)

```sql
-- Create / drop users
CREATE USER 'u'@'host' IDENTIFIED BY 'pw';
DROP USER 'u'@'host';

-- Grant / revoke
GRANT SELECT, INSERT ON db.table TO 'u'@'host';
GRANT SELECT ON *.* TO 'u'@'%';                    -- all DBs, all tables
GRANT ALL PRIVILEGES ON *.* TO 'u'@'%' WITH GRANT OPTION;
REVOKE SELECT, INSERT ON db.table FROM 'u'@'host';
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'u'@'host';

-- Inspect
SHOW GRANTS FOR 'u'@'host';
SELECT user, host FROM mysql.user;
SELECT CURRENT_USER();            -- effective account for this session

-- Roles
CREATE ROLE 'hr', 'inventory';
GRANT SELECT, INSERT, UPDATE, DELETE ON company.employees TO 'hr';
GRANT 'hr' TO 'u'@'host';                              -- assign
SET DEFAULT ROLE ALL TO 'u'@'host';                    -- persist/auto-activate
SET ROLE ALL;  SET ROLE NONE;  SET ROLE ALL EXCEPT 'hr';

-- Locks & password self-service
ALTER USER 'u'@'host' ACCOUNT LOCK;
ALTER USER 'u'@'host' ACCOUNT UNLOCK;
ALTER USER 'u'@'host' IDENTIFIED BY 'newpw';
FLUSH PRIVILEGES;                  -- reload grant tables
```

**4b. Expected outputs for key commands**

```text
mysql> SELECT CURRENT_USER();
+-----------------+
| CURRENT_USER()  |
+-----------------+
| u@10.0.0.5      |
+-----------------+
```
```text
mysql> SHOW GRANTS FOR 'u'@'localhost';
+----------------------------------------------------------------+
| Grants for u@localhost                                         |
+----------------------------------------------------------------+
| GRANT USAGE ON *.* TO `u`@`localhost`                          |
| GRANT SELECT ON `shop`.`products` TO `u`@`localhost`           |
+----------------------------------------------------------------+
```
*Note:* a freshly created user always shows `GRANT USAGE ON *.*` — that is **"no privileges."**

**5. Worked Examples (increasing difficulty)**

**Example 1 (basic) — create, verify, drop a temporary user.**
```sql
CREATE USER 'delete_me'@'%' IDENTIFIED BY 'Password';
SELECT CURRENT_USER();
DROP USER 'delete_me'@'%';
```
```text
Query OK, 0 rows affected
+----------------+
| CURRENT_USER() |
+----------------+
| root@localhost |
+----------------+
Query OK, 0 rows affected
```

**Example 2 (intermediate) — least-privilege app user + grant/revoke verification.**
```sql
CREATE USER 'app'@'10.0.0.5' IDENTIFIED BY 'AppPw!';
GRANT SELECT, INSERT, UPDATE, DELETE ON shop.* TO 'app'@'10.0.0.5';
SHOW GRANTS FOR 'app'@'10.0.0.5';
REVOKE DELETE ON shop.* FROM 'app'@'10.0.0.5';
```
Then as the app user, a denied action:
```sql
-- logged in as app
DROP TABLE shop.orders;
```
```text
ERROR 1142 (42000): DROP command denied to user 'app'@'10.0.0.5' for table 'orders'
```
*Why:* `ERROR 1142` = "command denied to user" — the exact error the lab expects.

**Example 3 (exam-style) — build departmental roles.**
```sql
CREATE ROLE 'HR', 'Sales', 'Inventory', 'Management';

-- HR over employees, customers, offices
GRANT SELECT, INSERT, UPDATE, DELETE ON company.employees TO 'HR';
GRANT SELECT, INSERT, UPDATE, DELETE ON company.customers TO 'HR';
GRANT SELECT, INSERT, UPDATE, DELETE ON company.offices   TO 'HR';

-- Sales = read-only over all
GRANT SELECT ON company.* TO 'Sales';

-- Assign + activate
CREATE USER 'ana'@'localhost' IDENTIFIED BY 'AnaPw!';
GRANT 'HR' TO 'ana'@'localhost';
SET DEFAULT ROLE ALL TO 'ana'@'localhost';
SHOW GRANTS FOR 'ana'@'localhost';
```
```text
| GRANT USAGE ON *.* TO `ana`@`localhost`            |
| GRANT `HR`@`%` TO `ana`@`localhost`                |
```
*Why:* `GRANT <role> TO <user>` assigns; `SET DEFAULT ROLE ALL` makes it auto-active at every login — the exact emphasis from the lab question.

**Example 4 (exam-style) — undo a mistaken CREATE and GRANT.**
```sql
-- undo the user
DROP USER 'Person1'@'localhost';

-- undo the grant (keep the account)
REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'Person1'@'localhost';
```

**6. Comparison Tables**

**Grant scope syntax**

| Form | Meaning | Valid? |
|---|---|---|
| `*.*` | all databases, all tables | ✅ |
| `db.*` | all tables in `db` | ✅ |
| `db.table` | one table | ✅ |
| `db.tbl(col1,col2)` | specific columns | ✅ (MySQL 8) |
| `%.%` | "all" — **not** valid grant syntax | ❌ |
| `%` (inside `@'...'`) | any host (correct use) | ✅ |

**`SET ROLE` vs `SET DEFAULT ROLE`**

| Aspect | `SET ROLE ALL;` | `SET DEFAULT ROLE ALL TO 'u'@'h';` |
|---|---|---|
| Scope | current session | the account, persistent |
| Survives logout | ❌ | ✅ |
| Applies at next login | ❌ | ✅ |
| Grants the role | ❌ | ❌ (must `GRANT role TO user` first) |

**Privilege risk**

| Privilege | Risk | Can it destroy data? |
|---|---|---|
| `SELECT` | 🟢 low | No (info leak only) |
| `INSERT`/`UPDATE` | 🟠 med | Modifies, not destroys |
| `DELETE`/`DROP` | 🔴 high | **Yes, irreversibly** |
| `USAGE` | — | No (it's "nothing") |

**root vs managed `mainuser` (ties to W5)**

| Aspect | Local `root` | AWS RDS `mainuser` |
|---|---|---|
| `SUPER` | ✅ | ❌ (managed) |
| `FILE` | ✅ | ❌ |
| Replication control | manual binlog | AWS stored procedures |

**7. ⚠️ Traps, Quirks and Common Mistakes**

> ⚠️ **TRAP — `GRANT SELECT ON %.% ...`** is **invalid**. "All" is `*.*`. `%` is only a host wildcard.
> ⚠️ **TRAP — "DROP is safe because it's recoverable."** False — dropped data is gone unless backed up.
> ⚠️ **TRAP — "`USAGE` = lots of permissions."** It means **no privileges**; it's the empty grant every account shows.
> ⚠️ **TRAP — assigning a role ≠ activating it.** `GRANT 'hr' TO 'u'` alone leaves the role **inactive**; you need `SET DEFAULT ROLE` (or `SET ROLE` per session).
> ⚠️ **TRAP — "users should read `mysql.user` to reset their own password."** False — direct access to grant tables is dangerous; use `ALTER USER`.
> ⚠️ **TRAP — different passwords per object.** MySQL allows **one** password per account (name+host), so "different password per table" is false.
> ⚠️ **TRAP — refreshing grant tables.** `GRANT`/`REVOKE`/`CREATE USER` refresh **automatically**; `FLUSH PRIVILEGES;` is only needed after **directly editing** grant tables. "You must always FLUSH after every change" is false.
> ⚠️ **TRAP — `WITH GRANT OPTION`.** It lets the grantee grant to *others* — a privilege-escalation vector; treat as admin-level.

**8. 💡 Memory Aids**

- **Identity:** *"A user is a **pair**, not a name"* (name + host).
- **`*.*` for grants, `%` for hosts** — *"star = scope, percent = place."*
- **Roles:** *"`GRANT ... TO` = give the badge; `SET DEFAULT ROLE` = make it always on."*
- **Risk:** *"SELECT sees, INSERT writes, DELETE/DROP destroy."*
- **USAGE:** *"USAGE = **U**nder **S**tandably **A**llowed **G**round-zero — nothing."*

**9. 🎯 Exam Worthiness Analysis**

- **Likelihood: 🔴 High** — three quiz banks (In-Class, Reading, Lab) plus a full lab flow.
- **Likely formats:** multiple choice, true/false, short answer, **command-writing** (grant a role), **scenario** (which role profile?), debugging (`ERROR 1142`).
- **Sub-points an examiner would test:** `'%'` host vs `*.*` scope; `USAGE` = none; `DROP` risk; role assignment vs `SET DEFAULT ROLE`; `SHOW GRANTS`; `ERROR 1142`; column-level grants (supported in 8); `ACCOUNT LOCK`; how to undo a bad `CREATE USER`/`GRANT`.
- **"I can ___" checklist:**
  - I can create a user with a host scope and explain why host matters.
  - I can grant table- and column-level privileges using correct `db.table` / `*.*` syntax.
  - I can build roles for read-only, app, and DBA profiles.
  - I can distinguish `SET ROLE` from `SET DEFAULT ROLE`.
  - I can undo a mistaken CREATE/GRANT.
  - I can read `SHOW GRANTS` and `mysql.user` output.

**10. Quick Self-Check**

1. **Is `'sammy1'@'10.0.0.233'` the same account as `'sammy1'@'10.44.33.102'`?** → **No** — different host, different account/password/privileges.
2. **Is `GRANT SELECT ON %.%` correct?** → **No** — use `*.*`.
3. **What does `USAGE` mean?** → **No privileges.**
4. **`GRANT 'hr' TO 'ana'` — is `hr` now active?** → **No** — assign ≠ activate; use `SET DEFAULT ROLE`.
5. **How do you undo `GRANT ALL ON *.* TO Person1@localhost;`?** → **`REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'Person1'@'localhost';`**

**📝 Provided Quiz Bank (Week 4)**

| # | Question | Answer | Why |
|---|---|---|---|
| 1 | Same name, two hosts → different passwords/privileges? | **True** | identity = name+host |
| 2 | "DROP isn't dangerous because data is recoverable." | **False** | irreversible |
| 3 | `USAGE` = "no privileges"? | **True** | empty grant |
| 4 | `'%'` host = all addresses? | **True** | host wildcard |
| 5 | `GRANT SELECT ON %.%` = all DBs/tables? | **False** | must be `*.*` |
| 6 | Granting `SELECT` is relatively safe? | **True** | can't change data (but may expose rows) |
| 7 (Reading) | Users should access `mysql.user` to reset their own password? | **False** | use `ALTER USER` |
| 8 (Reading) | MySQL 8: different password per object? | **False** | one password per account |
| 9 (Reading) | SSL on by default but no cert → data unencrypted? | **False-ish** — MySQL **auto-generates** certs, so traffic *is* encrypted (just not CA-trusted) | flag ambiguity |
| 10 (Reading) | SSL disabled → queries readable on the wire? | **True** | plaintext |
| 11 (Reading) | `ALTER USER ... ACCOUNT LOCK` locks a user? | **True** | yes |
| 12 (Reading) | Which is **NOT** a way to refresh grant tables? | the invalid option (grant/revoke/**restart** auto-reload; `FLUSH PRIVILEGES` too) | pick the odd one |
| 13 (Lab) | Which is **NOT** a grantable privilege? | the non-privilege option | valid set includes SELECT/INSERT/…/SUPER |
| 14 (Lab) | Why use roles for many same-privilege users? | **manage/assign in bulk, consistency, least privilege** | avoids per-user grants |
| 15 (Lab) | MySQL 8 can grant per-column privileges? | **True** | `GRANT SELECT(col) ON db.t ...` |
| 16 (Lab) | "`SET DEFAULT ROLE ALL` activates roles..." | **True (partial)** — it sets persistent default roles so they auto-activate at login; it does **not** grant the role | nuance |
| 17 (Lab) | Undo `CREATE USER Person1@localhost ...`? | **`DROP USER 'Person1'@'localhost';`** | remove account |
| 18 (Lab) | Undo `GRANT ALL ON *.* TO Person1@localhost;`? | **`REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'Person1'@'localhost';`** | strip privileges |
| 19 (Lab) | Different privileges by host IP? | **True** | host is part of identity |
| 20 (Lab) | Operations allowed for a brand-new user? | **connect, `SELECT CURRENT_USER()`/`SELECT 1`, change own password; no DB access** | only `USAGE` |

---

✅ **Delivered so far:** Part 1 → Topics 1–4 complete.
▶ **Next chunk:** Topic 5 (Week 5 — Replication & High Availability), Topic 6 (Week 6 — Storage Engines & Constraints).


# 🟢 TOPIC 5. Week 5 — Replication & High Availability   [Exam Likelihood: 🔴 High]

**1. Big Picture (2–4 sentences)**
High availability is about keeping a database online when hardware dies and buying time for maintenance without downtime. MySQL does this with **primary→secondary replication**: the primary records every change in its **binary log**, and replicas replay it via **relay logs**. You also meet the cloud-managed version (AWS **RDS**), where AWS runs the servers and you configure snapshots, read replicas, promotion, and GTID. The exam's favourite twist: **replication is a hardware-failure tool — it does NOT protect against human error like a bad `DELETE`.**

**2. Key Terms and Definitions**

| Term | Precise definition | Common confusion with |
|---|---|---|
| Replication | Asynchronous copying of changes from a primary to one or more secondaries | a **backup** (it propagates mistakes!) |
| Binary log (binlog) | Primary's record of all data-changing statements/row events | the **relay log** (lives on the replica) |
| Relay log | Secondary's local copy of binlog events, applied by the SQL thread | the binlog |
| Server ID | Unique integer identifying each instance in a topology | the hostname (must be an int, must be unique) |
| `log_replica_updates` | Records replicated changes into the replica's own binlog (needed for chained) | `log_bin` (the master switch) |
| GTID | Global Transaction Identifier — unique ID per transaction, replacing file+position | binlog file/position coordinates |
| Topology | The arrangement of primaries/secondaries (chain, ring, dual-primary…) | "the schema" |
| Failover | Promoting a secondary when the primary dies | "backup restore" |
| Blue/Green deployment | Two environments; switch traffic after upgrading the idle one | in-place upgrade |
| Multi-AZ (RDS) | Standby replica for **automatic failover** | Read Replica (for **scale**) |

**3. Core Concepts Explained**

**Why HA exists.** One server = a **single point of failure**; if it dies, the site goes down. Replicas give redundancy, scale read traffic, reduce latency (place replicas near users), and enable **zero-downtime maintenance**.

**Replication vs backups — the exam crux.**

| Failure | Replication handles it? | Backup handles it? |
|---|---|---|
| Disk/hardware dies | ✅ promote a replica in seconds | ⚠️ slower restore |
| Accidental `DELETE`/`DROP` | ❌ **it replicates the mistake** | ✅ restore the pre-mistake state |
| **Best practice** | **Use both** — replication + scheduled backups | |

**Replication internals (three threads).**
```
 PRIMARY                                   SECONDARY
 ┌────────┐   binlog events          ┌────────────────────┐
 │ binlog │ ───────────────────────► │ (I/O thread) writes│
 └────────┘                          │   ▼ relay log      │
                                     │ (SQL thread) applies│
                                     │   ▼ data changes   │
                                     └────────────────────┘
```

**Topologies.**
```
1:1            P ──► S
1:N            P ──► S1, S2, S3
Cascaded       P ──► S1 ──► S2          (needs log_replica_updates on S1)
Dual-Primary   P ◄──► P                 (both writable; conflict risk)
Circular/Ring  P1 ─► P2 ─► P3 ─► P1     (risk: loops)
Multi-Source   S ◄── P1 and P2          (via replication channels)
```

**Configuration prerequisites.**
1. Compatible server versions.
2. **`server_id` unique** on every instance.
3. **`log_bin` ON** on the primary; **relay log** on the secondary.
4. `log_replica_updates` ON for **chained/intermediate** servers.
5. A dedicated **replication user** with `REPLICATION SLAVE` privilege.

**Failover & zero-downtime upgrades.** When the primary fails, promote a secondary (point clients at it). For **Blue/Green** upgrades, build/upgrade a replicated Green instance, let it catch up, then switch traffic — no outage.

**AWS RDS (managed).** In the **shared responsibility model**, AWS manages hardware, OS, patching, power, and DB software; **you** manage schema design, scaling, HA, and optimisation. RDS gives you snapshots, automated backups, Read Replicas, Multi-AZ failover, and parameter groups — but `mainuser` **lacks `SUPER`**, so you use AWS **stored procedures** (e.g. `mysql.rds_set_source_auto_position`) instead of manual binlog admin.

**4. Syntax / Commands / Formulas Reference**

| Command | What it does | Minimal example | Expected output |
|---|---|---|---|
| `SHOW REPLICA STATUS\G` | Replica health (I/O & SQL threads) | `SHOW REPLICA STATUS\G` | rows incl. `Replica_IO_Running: Yes` |
| `SELECT @@binlog_format;` | Current binlog format | `SELECT @@binlog_format;` | `ROW` (8.0 default) |
| `SHOW VARIABLES LIKE '%gtid%';` | GTID settings | `SHOW VARIABLES LIKE '%gtid%';` | `gtid_mode ON`, `enforce_gtid_consistency ON` |
| `SELECT @@server_id;` | This server's ID | `SELECT @@server_id;` | e.g. `1` |
| `CHANGE REPLICATION SOURCE TO ...` | Point replica at primary | `... SOURCE_HOST='10.0.0.9', SOURCE_USER='repl', SOURCE_PASSWORD='pw', SOURCE_AUTO_POSITION=1;` | `Query OK` |
| `START REPLICA;` | Begin replication | `START REPLICA;` | `Query OK` |
| `mysql.rds_set_source_auto_position(1);` | AWS: enable GTID auto-position | `CALL mysql.rds_set_source_auto_position(1);` | `Query OK` |

**5. Worked Examples (increasing difficulty)**

**Example 1 (basic) — inspect replication config.**
```sql
SELECT @@server_id;             -- must be UNIQUE per instance
SELECT @@log_bin;               -- 1 = binlog enabled
SELECT @@binlog_format;         -- ROW | STATEMENT | MIXED
SELECT @@gtid_mode;             -- ON/OFF
```
```text
+-------------+
| @@server_id |
+-------------+
|           1 |
+-------------+
```

**Example 2 (intermediate) — set up replication (self-managed).**
```sql
-- On PRIMARY: create the replication user
CREATE USER 'repl'@'%' IDENTIFIED BY 'ReplPw!';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

-- On SECONDARY: point at the primary and start
CHANGE REPLICATION SOURCE TO
  SOURCE_HOST='10.0.0.9', SOURCE_USER='repl', SOURCE_PASSWORD='ReplPw!',
  SOURCE_AUTO_POSITION=1;
START REPLICA;
SHOW REPLICA STATUS\G
```
```text
Replica_IO_Running: Yes
Replica_SQL_Running: Yes
Seconds_Behind_Source: 0
```
*Why:* both threads `Yes` = healthy; `Seconds_Behind_Source` = lag.

**Example 3 (exam-style) — AWS RDS read replica + promotion.**
```
Console: RDS → select primary → Actions → Create read replica → name: acit3910-server-2
Verify:  SHOW REPLICA STATUS\G     (on the replica endpoint)
Failover: RDS → replica → Actions → Promote   (becomes standalone primary)
```
*Why:* a Read Replica scales **reads**; promotion turns it into a writable standalone for **failover**.

**Example 4 (exam-style) — GTID via parameter group.**
```
1. Create custom parameter group: gtid-replication-parameters (family mysql8.4)
2. Set:  gtid-mode = ON
         enforce-gtid-consistency = ON
3. Associate with instance(s) → Apply immediately → Reboot
4. Verify:  SHOW VARIABLES LIKE '%gtid%';
5. On replica:  CALL mysql.rds_set_source_auto_position(1);
```
```text
| gtid_mode                | ON  |
| enforce_gtid_consistency | ON  |
```
*Why:* GTID replaces file+position; `rds_set_source_auto_position(1)` tells the replica to use GTID auto-positioning instead of manual coordinates.

**6. Comparison Tables**

**Replication vs Backup**

| Aspect | Replication | Backup (dump/snapshot) |
|---|---|---|
| Purpose | HA, read scale, low latency | point-in-time recovery |
| Protects from HW failure | ✅ fast promote | ⚠️ slower restore |
| Protects from human error | ❌ propagates it | ✅ restores old state |
| Speed of recovery | seconds | minutes–hours |
| **Use together?** | **Yes** | **Yes** |

**Topologies**

| Topology | Use case | Gotcha |
|---|---|---|
| 1:1 | simple HA | one replica = still a bottleneck |
| 1:N | read scale-out | more load on primary binlog |
| Cascaded | offload the primary | needs `log_replica_updates` |
| Dual-primary | fast failover | write conflicts |
| Ring | multi-site | loops/duplicates |
| Multi-source | consolidation | channel management |

**Multi-AZ vs Read Replica (RDS)**

| Aspect | Multi-AZ | Read Replica |
|---|---|---|
| Goal | **failover / HA** | **read scaling** |
| Serves reads | ❌ (standby) | ✅ |
| Auto failover | ✅ | ❌ (manual promote) |
| Cross-region | limited | ✅ supported |

**Binlog formats**

| Format | Records | Pro | Con |
|---|---|---|---|
| STATEMENT | the SQL text | compact | non-deterministic risk (e.g. `NOW()`) |
| ROW | the row changes | deterministic | larger logs |
| MIXED | chooses per statement | balance | complexity |

**7. ⚠️ Traps, Quirks and Common Mistakes**

> ⚠️ **TRAP — "replication protects against accidental DELETE."** False — it **replicates** the delete. Only backups recover from it.
> ⚠️ **TRAP — "RDS replicas must be in the same region."** False — cross-region Read Replicas are supported.
> ⚠️ **TRAP — "RDS can't automate failover."** False — **Multi-AZ** gives automatic failover.
> ⚠️ **TRAP — duplicate `server_id`s.** Replication silently breaks. Every instance needs a unique integer.
> ⚠️ **TRAP — chained replication without `log_replica_updates`.** The intermediate server won't log changes, so the downstream replica gets nothing.
> ⚠️ **TRAP — "mainuser is like root on RDS."** RDS `mainuser` **lacks `SUPER`**; use AWS stored procedures.
> ⚠️ **TRAP — forgetting to delete RDS resources.** Idle instances/snapshots keep billing — the lab stresses cleanup.
> ⚠️ **VERSION NOTE:** In MySQL 8.0.22+ the replication terms changed: `START SLAVE`→`START REPLICA`, `SHOW SLAVE STATUS`→`SHOW REPLICA STATUS`, `CHANGE MASTER TO`→`CHANGE REPLICATION SOURCE TO`, `log_slave_updates`→`log_replica_updates`. Old names still work but are deprecated — expect the new ones.

**8. 💡 Memory Aids**

- **"binlog records, relay log replays."**
- **"Replication = hardware insurance; backups = human-error insurance."**
- **Multi-AZ = **A**uto failover; Read Replica = **R**ead scale** (A vs R).
- **GTID = "Global Transaction ID" → no more file/position juggling.**
- **Topology picture:** *"chain goes down, ring goes around, dual goes both ways."*

**9. 🎯 Exam Worthiness Analysis**

- **Likelihood: 🔴 High** — an entire AWS lab plus a Reading quiz; also hybrid RDS questions.
- **Likely formats:** multiple choice, true/false, short answer, diagram (label binlog/relay log threads), scenario (which topology?).
- **Sub-points an examiner would test:** replication vs backups; binlog vs relay log; unique `server_id`; prerequisites; Multi-AZ vs Read Replica; promotion; GTID parameters; RDS shared responsibility; `mainuser` limits.
- **"I can ___" checklist:**
  - I can explain why replication doesn't save you from a bad `DELETE`.
  - I can trace binlog → relay log → apply.
  - I can name four replication topologies.
  - I can list the five replication prerequisites.
  - I can distinguish Multi-AZ from Read Replica and explain promotion.
  - I can describe enabling GTID via a parameter group.

**10. Quick Self-Check**

1. **Which log does the primary write?** → **Binary log (binlog).**
2. **Which log stores events on the replica?** → **Relay log.**
3. **Does replication recover accidentally deleted rows?** → **No** — it replicates the delete.
4. **RDS replicas same-region only?** → **No** — cross-region allowed.
5. **What's needed on every instance for replication?** → A **unique `server_id`.**

**📝 Provided Quiz Bank (Week 5 — Reading)**

| # | Question | Answer | Why |
|---|---|---|---|
| 1 | RDS can help scale DBs for higher demand? | **True** | vertical + replicas |
| 2 | RDS can create Read Replicas for scaling? | **True** | read scale-out |
| 3 | RDS replicas must be in the same region? | **False** | cross-region supported |
| 4 | RDS cannot automate failover? | **False** | Multi-AZ automates it |
| 5 | RDS lets you choose PostgreSQL/MySQL/MSSQL/MariaDB/Oracle? | **True** | multi-engine |
| 6 | Aurora is compatible with…? | **MySQL and PostgreSQL** | both wire-compatible |
| 7 | RDS handles hardware, backups, storage? | **True** | shared responsibility |

---

# 🟢 TOPIC 6. Week 6 — Storage Engines & Constraints   [Exam Likelihood: 🔴 High]

**1. Big Picture (2–4 sentences)**
This week you look under the hood: **how rows are stored** (storage engines), **why InnoDB is the default** (ACID transactions + row locking + FKs), and **how MySQL stops bad data at the door** (constraints). You also learn that some business rules are too complex for plain constraints and need **triggers**. Expect heavy comparison questions (InnoDB vs MyISAM, CHAR vs VARCHAR, DATETIME vs TIMESTAMP) and "which engine for this job" scenarios.

**2. Key Terms and Definitions**

| Term | Precise definition | Common confusion with |
|---|---|---|
| Storage engine | The component that reads/writes table data (InnoDB, MyISAM, MEMORY…) | the DBMS itself |
| InnoDB | Default engine: ACID, row-level locking, FKs, crash recovery, MVCC | MyISAM |
| MyISAM | Older engine: no transactions, **table-level** locking, no FK enforcement | InnoDB |
| ACID | Atomicity, Consistency, Isolation, Durability | "just transactions" |
| Constraint | A rule the DB enforces to keep data valid | application-level validation |
| `CHECK` | Value predicate constraint, enforced from **8.0.16** | UNIQUE (uniqueness, not range) |
| `UNSIGNED` | Numeric attribute allowing only ≥ 0 | a constraint type per se (it's an attribute) |
| Trigger | Server-side code fired by INSERT/UPDATE/DELETE | a stored procedure (called manually) |
| ERD | Entity-Relationship Diagram of tables + relationships | the physical schema |

**3. Core Concepts Explained**

**SSD vs HDD.** SSDs have no moving parts → far higher random-I/O (IOPS), lower latency; ideal for databases (which are random-access-heavy). HDDs are cheaper per GB, better for sequential bulk/archival. For a live MySQL server, **SSD wins**.

**Engines at a glance (`SHOW ENGINES;`).** The engine is chosen per table (`ENGINE=InnoDB`); **InnoDB is the default** (since 5.5).

| Engine | Transactions | Locking | FK | Persistence | Best for |
|---|---|---|---|---|---|
| **InnoDB** | ✅ ACID | row-level | ✅ | disk | default; OLTP |
| MyISAM | ❌ | table-level | ❌ | disk | legacy read-heavy |
| MEMORY | ❌ | table-level | ❌ | **RAM only** | temp/fast data |
| CSV | ❌ | table-level | ❌ | disk (text) | data interchange |
| BLACKHOLE | ❌ | — | ❌ | discards | benchmarking/filtering |
| ARCHIVE | ❌ | row (insert/select) | ❌ | compressed | archival |

**InnoDB vs MyISAM — the key comparison.**
```
                 Need transactions?  ── yes ──► InnoDB
                        │ no
                 Heavy concurrent writes? ── yes ──► InnoDB (row locks)
                        │ no
                 Legacy read-mostly?   ── yes ──► MyISAM (but consider InnoDB anyway)
```

**ACID.**
- **A**tomicity — a transaction is all-or-nothing.
- **C**onsistency — constraints/ invariants hold before and after.
- **I**solation — concurrent transactions don't interfere.
- **D**urability — committed data survives crashes.

**Constraints (data integrity).**

| Constraint | Enforces | Example |
|---|---|---|
| `NOT NULL` | column must have a value | `name VARCHAR(50) NOT NULL` |
| `UNIQUE` | no duplicate values | `email VARCHAR(100) UNIQUE` |
| `PRIMARY KEY` | NOT NULL + UNIQUE row identity | `id INT PRIMARY KEY AUTO_INCREMENT` |
| `FOREIGN KEY` | value must exist in parent table | `FOREIGN KEY (dept) REFERENCES dept(id)` |
| `UNSIGNED` | non-negative numbers | `qty INT UNSIGNED` |
| `CHECK` | predicate (8.0.16+) | `CHECK (age >= 0)` |
| `DEFAULT` / `AUTO_INCREMENT` | default value / auto id | `created TIMESTAMP DEFAULT CURRENT_TIMESTAMP` |

**Advanced constraints via triggers.** Some rules can't be expressed as a constraint — e.g. "a class has at most 30 students" or "free shipping only if total > $50." Use a **BEFORE INSERT/BEFORE UPDATE** trigger that raises an error when the rule is violated.

**ERD best practices.** Clean, non-overlapping layouts; one concern per diagram; show PKs/FKs clearly; use crow's-foot notation; name relationship verbs; avoid crossing lines; normalise enough to remove redundancy.

**4. Syntax Reference**

```sql
-- Engine inspection
SHOW ENGINES;
SHOW TABLE STATUS LIKE 't';           -- shows Engine column per table
CREATE TABLE t (...) ENGINE=InnoDB;   -- per-table engine

-- Constraints
CREATE TABLE enrollment (
  id        INT AUTO_INCREMENT PRIMARY KEY,
  student   VARCHAR(50) NOT NULL,
  course    VARCHAR(50) NOT NULL,
  credits   INT UNSIGNED CHECK (credits BETWEEN 1 AND 6),
  email     VARCHAR(120) UNIQUE,
  dept_id   INT,
  CONSTRAINT fk_dept FOREIGN KEY (dept_id) REFERENCES dept(id)
);
```

```sql
-- Trigger enforcing a business rule (max 30 students)
DELIMITER //
CREATE TRIGGER before_enroll
BEFORE INSERT ON enrollment
FOR EACH ROW
BEGIN
  IF (SELECT COUNT(*) FROM enrollment WHERE course = NEW.course) >= 30 THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Course is full (max 30)';
  END IF;
END//
DELIMITER ;
```
```text
mysql> INSERT INTO enrollment (student, course, credits) VALUES ('Zed','MATH101',3);
ERROR 1644 (45000): Course is full (max 30)
```

**5. Worked Examples (increasing difficulty)**

**Example 1 (basic) — list engines.**
```sql
SHOW ENGINES;
```
```text
+--------------------+---------+--------------------+
| Engine             | Support | Comment            |
+--------------------+---------+--------------------+
| InnoDB             | DEFAULT | Supports txns,row locks,FK |
| MRG_MyISAM         | YES     | ...                |
| MEMORY             | YES     | Hash based, stored in memory |
| CSV                | YES     | CSV storage engine |
| MyISAM             | YES     | MyISAM storage engine |
| ARCHIVE            | YES     | Archive storage engine |
| BLACKHOLE          | YES     | /dev/null storage engine |
+--------------------+---------+--------------------+
```

**Example 2 (intermediate) — constraint rejection.**
```sql
INSERT INTO enrollment (student, course, credits) VALUES ('A','BIO1', 99);
```
```text
ERROR 3819 (HY000): Check constraint 'enrollment_chk_1' is violated.
```
*Why:* `CHECK (credits BETWEEN 1 AND 6)` blocks out-of-range data — proof the DB, not the app, enforced it.

**Example 3 (exam-style) — CHAR vs VARCHAR storage.**
```sql
CREATE TABLE c_demo (a CHAR(15), b VARCHAR(15));
INSERT INTO c_demo VALUES ('hello','hello');
```
```text
CHAR(15) stores 'hello' padded to 15 chars  → 15 bytes (latin1), fixed
VARCHAR(15) stores 'hello' as 5 chars + 1 length byte → ~6 bytes
```
> **Exam answer:** *"'hello' will consume less space as `char(15)` than `varchar(15)`" = **False**.* VARCHAR only pays for actual length + 1–2 bytes; CHAR always reserves the full width. (VARCHAR wins for short strings; CHAR can win when values are consistently near full width.)

**Example 4 (exam-style) — TIMESTAMP vs DATETIME range.**
> *"To store dates outside 1970–2038, use TIMESTAMP instead of DATETIME."* → **False.** **TIMESTAMP** is the one limited to ~1970–2038 (32-bit UTC range); **DATETIME** supports ~1000–9999, so use **DATETIME** for wide ranges.

**6. Comparison Tables**

**InnoDB vs MyISAM**

| Feature | InnoDB | MyISAM |
|---|---|---|
| Transactions | ✅ ACID | ❌ |
| Locking | row-level | table-level |
| Foreign keys | ✅ enforced | ❌ ignored |
| Crash recovery | ✅ (redo log) | ❌ (repair needed) |
| Full-text | ✅ (5.6+) | ✅ |
| Default engine | ✅ (since 5.5) | ❌ |
| When to use | anything with integrity/concurrency | rarely; legacy read-only |

**MEMORY vs InnoDB**

| Aspect | MEMORY | InnoDB |
|---|---|---|
| Storage | RAM only | disk |
| Survives restart | ❌ | ✅ |
| Speed | very fast | fast |
| Use | temp/fast lookups | critical long-term |

**CSV / BLACKHOLE**

| Engine | Behaviour | Use |
|---|---|---|
| CSV | plain CSV file, **no indexes** | import/export interchange |
| BLACKHOLE | accepts writes, **discards** them | benchmarking, replication filtering |

**CHAR vs VARCHAR**

| Aspect | CHAR(n) | VARCHAR(n) |
|---|---|---|
| Storage | fixed n | actual + 1/2 bytes |
| Padding | space-padded | none |
| Best for | fixed-length codes | variable text |
| Gotcha | trailing-space comparisons | length prefix overhead |

**DATETIME vs TIMESTAMP**

| Aspect | DATETIME | TIMESTAMP |
|---|---|---|
| Range | 1000–9999 | 1970–2038 |
| Timezone | stored as-is | stored UTC, converted |
| Auto-update | ❌ | ✅ `DEFAULT/ON UPDATE CURRENT_TIMESTAMP` |
| Use for wide ranges | **✅** | ❌ |

**7. ⚠️ Traps, Quirks and Common Mistakes**

> ⚠️ **TRAP — "InnoDB and MyISAM both support transactions."** **False** — only InnoDB (and other transactional engines).
> ⚠️ **TRAP — "CSV is great for large data."** **False** — no indexes, poor for retrieval.
> ⚠️ **TRAP — MEMORY engine persistence.** It lives in RAM; **restart = data loss** → never for critical data.
> ⚠️ **TRAP — TIMESTAMP vs DATETIME ranges** (see above — the exam flips it on purpose).
> ⚠️ **TRAP — CHAR vs VARCHAR space.** VARCHAR is usually *smaller* for short strings; the "char is smaller" statement is false as worded.
> ⚠️ **TRAP — `CHECK` availability.** Only **enforced from MySQL 8.0.16**; earlier versions parsed but ignored it. Know the version boundary.
> ⚠️ **TRAP — `UNSIGNED` isn't a constraint keyword** in the strict sense — it's a numeric column attribute.
> ⚠️ **TRAP — trigger recursion/permissions.** Triggers run with the definer's privileges; a runaway trigger can cascade. Use `SIGNAL` to raise custom errors.

**8. 💡 Memory Aids**

- **ACID = "All Consistent, Isolated, Durable."**
- **InnoDB = "I**n** — Integrity, InnoDB has it all"** (I: transactions, isolation, integrity).
- **MyISAM = "My data is simple"** — no transactions, table locks.
- **CHAR = "CHunky & fixed"; VARCHAR = "VARiable & lean."**
- **TIMESTAMP = "Time-bomb"** (1970–2038 limit) → use DATETIME for wide ranges.

**9. 🎯 Exam Worthiness Analysis**

- **Likelihood: 🔴 High** — 10 Reading-quiz items plus a schema-building lab.
- **Likely formats:** multiple choice, true/false, short answer, code-writing (CREATE TABLE with constraints), scenario (which engine?).
- **Sub-points an examiner would test:** InnoDB vs MyISAM differences; default engine; ACID definitions; constraint types incl. `CHECK` from 8.0.16; MEMORY/CSV/BLACKHOLE behaviour; CHAR vs VARCHAR; DATETIME vs TIMESTAMP; triggers for complex rules.
- **"I can ___" checklist:**
  - I can list the four InnoDB advantages over MyISAM.
  - I can define all four ACID letters.
  - I can write a CREATE TABLE with PK, FK, UNIQUE, NOT NULL, UNSIGNED, and CHECK.
  - I can explain when to use MEMORY vs InnoDB.
  - I can pick DATETIME vs TIMESTAMP correctly.
  - I can write a BEFORE INSERT trigger to enforce a business rule with `SIGNAL`.

**10. Quick Self-Check**

1. **Default engine in MySQL 8?** → **InnoDB.**
2. **Which engine has row-level locking + FKs + transactions?** → **InnoDB.**
3. **Does the MEMORY engine survive a restart?** → **No** — RAM only.
4. **Which date type is limited to 1970–2038?** → **TIMESTAMP** (use DATETIME for wider).
5. **From which version is `CHECK` enforced?** → **8.0.16.**

**📝 Provided Quiz Bank (Week 6 — Reading)**

| # | Question | Answer | Why |
|---|---|---|---|
| 1 | Both InnoDB and MyISAM support transactions (MySQL 8)? | **False** | MyISAM has no transactions |
| 2 | CSV engine good for large datasets, fast retrieval? | **False** | no indexes |
| 3 | MEMORY engine isn't on permanent storage? | **True** | RAM only |
| 4 | BLACKHOLE can be used to find performance bottlenecks? | **True** | discards writes, isolates cost |
| 5 | MEMORY good for temp data needing fast access? | **True** | in-RAM |
| 6 | Purpose of BLACKHOLE? | **discards all data** (benchmarking/filtering) | `/dev/null` analogue |
| 7 | "hello" uses less space as `char(15)` than `varchar(15)`? | **False** | VARCHAR stores actual+overhead |
| 8 | InnoDB fully supports transactions? | **True** | ACID |
| 9 | Store dates outside 1970–2038 → use TIMESTAMP over DATETIME? | **False** | use **DATETIME** |

---

✅ **Delivered so far:** Part 1 → Topics 1–6 complete.
▶ **Next chunk:** Topic 7 (Week 7 — Advanced Database Objects), then **Part 2 — Exam Worthiness Master Summary.**


# 🟢 TOPIC 7. Week 7 — Advanced Database Objects   [Exam Likelihood: 🔴 High]

**1. Big Picture (2–4 sentences)**
Beyond tables, MySQL stores logic and reusable queries as **objects**: **views** (saved SELECTs that behave like virtual tables), **stored procedures** and **stored functions** (server-side routines), and **triggers** (code that fires automatically on INSERT/UPDATE/DELETE). These objects abstract the schema, enforce security, and encode business rules where the application can't be trusted to. The exam loves **views vs tables**, **procedure vs function**, and **trigger timing (BEFORE vs AFTER)**.

**2. Key Terms and Definitions**

| Term | Precise definition | Common confusion with |
|---|---|---|
| View | A stored `SELECT` that acts as a virtual table | a base table (it holds no data) |
| Updatable view | A view MySQL can write through to the base table | all views (many are read-only) |
| Stored procedure | A named block of SQL run with `CALL`, supports IN/OUT/INOUT params | a stored function |
| Stored function | A routine that **returns a value**, usable inside `SELECT`/`WHERE` | a procedure |
| Trigger | Code auto-fired by a table event (BEFORE/AFTER × INSERT/UPDATE/DELETE) | a procedure (called manually) |
| `NEW` / `OLD` | Row image references inside triggers | "the table row" |
| `--routines` | `mysqldump` flag to include procedures/functions in a dump | `--triggers` (dumped by default) |

**3. Core Concepts Explained**

**Views.** A view is a **stored SELECT** — it stores the *query*, not the data. When queried, MySQL runs the underlying SELECT.

*Advantages:*
- **Column filtering** (hide sensitive columns)
- **Multi-table joins / unions** simplified into one object
- **Computed columns** (e.g. `price*qty AS total`)
- **Aggregations** pre-packaged
- **Schema abstraction** (apps reference the view; base tables can change)
- **Access security** (grant `SELECT` on the view, not the tables)

*Disadvantages:*
```
⚠ performance   → the underlying query runs every time
⚠ dependency    → base schema changes can break the view
⚠ updateability → aggregates/DISTINCT/GROUP BY/UNION ⇒ often read-only
```

**Stored routines (procedures & functions).** Server-side programs containing SQL + procedural logic (IF, LOOP, variables). Benefits: **pre-compiled** (less parse overhead), centralised logic, reduced network round-trips, and a security boundary (definer rights).

**Procedure vs Function (key distinctions):**

| Aspect | Procedure | Function |
|---|---|---|
| Invoke | `CALL p(...)` | used **inside** `SELECT`/`WHERE` |
| Returns | 0+ values via OUT/INOUT | exactly **one** value (`RETURNS`) |
| Params | IN, OUT, INOUT | IN only |
| Can run `COMMIT`/`ROLLBACK` | ✅ | ❌ |
| Determinism | n/a | often must be `DETERMINISTIC`/`NO SQL` for binlog safety |

**Backup gotcha:** `mysqldump` does **not** include routines unless you pass **`--routines`**. (Triggers are included by default; `--triggers` exists too.)

**Triggers.** A trigger fires automatically on a table event.

```
        BEFORE                    AFTER
INSERT  validate/set defaults      log/audit
UPDATE  validate/compare OLD/NEW   sync/audit
DELETE  prevent/log               audit
```
- `BEFORE` triggers can **modify** `NEW` (and validate/raise errors).
- `AFTER` triggers see the committed row and are used for audits/side-effects.
- Triggers may **`CALL` a stored procedure** to keep the body simple.
- Inside: `NEW.col` = incoming row; `OLD.col` = existing row (UPDATE/DELETE).

**4. Syntax Reference**

```sql
-- VIEWS
CREATE VIEW v_active AS
  SELECT c.id, c.name, SUM(o.total) AS spend
  FROM customers c JOIN orders o ON o.cust_id = c.id
  WHERE c.active = 1
  GROUP BY c.id, c.name;
SELECT * FROM v_active WHERE spend > 100;
CREATE OR REPLACE VIEW v_active AS SELECT ...;
SHOW CREATE VIEW v_active;
DROP VIEW v_active;

-- STORED PROCEDURE
DELIMITER //
CREATE PROCEDURE add_customer(IN nm VARCHAR(50), OUT new_id INT)
BEGIN
  INSERT INTO customers(name, active) VALUES (nm, 1);
  SET new_id = LAST_INSERT_ID();
END//
DELIMITER ;
CALL add_customer('Ana', @id);
SELECT @id;

-- STORED FUNCTION
DELIMITER //
CREATE FUNCTION with_tax(price DECIMAL(10,2))
RETURNS DECIMAL(10,2) DETERMINISTIC
BEGIN
  RETURN price * 1.13;
END//
DELIMITER ;
SELECT with_tax(100.00);   -- 113.00

-- TRIGGER
DELIMITER //
CREATE TRIGGER trg_stock_guard
BEFORE UPDATE ON products
FOR EACH ROW
BEGIN
  IF NEW.stock < 0 THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Stock cannot be negative';
  END IF;
END//
DELIMITER ;

-- Backups
-- mysqldump -u root -p --routines mydb > mydb_with_routines.sql
```

**5. Worked Examples (increasing difficulty)**

**Example 1 (basic) — a column-filtering view.**
```sql
CREATE VIEW v_public_emp AS
  SELECT id, first_name, last_name, dept FROM employees;   -- hides salary
SELECT * FROM v_public_emp LIMIT 2;
```
```text
+----+------------+-----------+-------+
| id | first_name | last_name | dept  |
+----+------------+-----------+-------+
|  1 | Ana        | Ruiz      | HR    |
|  2 | Ben        | Cole      | Sales |
+----+------------+-----------+-------+
```

**Example 2 (intermediate) — a stored function used inside a query.**
```sql
DELIMITER //
CREATE FUNCTION with_tax(price DECIMAL(10,2))
RETURNS DECIMAL(10,2) DETERMINISTIC
BEGIN RETURN price * 1.13; END//
DELIMITER ;

SELECT sku, price, with_tax(price) AS price_with_tax FROM products;
```
```text
+------+-------+----------------+
| sku  | price | price_with_tax |
+------+-------+----------------+
| A100 | 10.00 |          11.30 |
+------+-------+----------------+
```
*Why:* a **function** can live inside `SELECT`, unlike a procedure.

**Example 3 (exam-style) — a BEFORE INSERT trigger enforcing a business rule by calling a procedure.**
```sql
DELIMITER //
CREATE PROCEDURE ensure_capacity(IN cid INT)
BEGIN
  IF (SELECT COUNT(*) FROM enrollment WHERE course_id = cid) >= 30 THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Course full';
  END IF;
END//
CREATE TRIGGER trg_capacity BEFORE INSERT ON enrollment
FOR EACH ROW
BEGIN
  CALL ensure_capacity(NEW.course_id);
END//
DELIMITER ;
```
```text
mysql> INSERT INTO enrollment(student, course_id) VALUES ('Zed', 42);
ERROR 1644 (45000): Course full
```

**Example 4 (exam-style) — AFTER trigger audit.**
```sql
CREATE TABLE audit_log (id INT AUTO_INCREMENT PRIMARY KEY,
                        action VARCHAR(20), emp_id INT, at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);
DELIMITER //
CREATE TRIGGER trg_emp_audit AFTER DELETE ON employees
FOR EACH ROW
BEGIN
  INSERT INTO audit_log(action, emp_id) VALUES ('DELETE', OLD.id);
END//
DELIMITER ;
```

**Example 5 (exam-style) — verify routines survive a backup.**
```bash
mysqldump -u root -p --routines employees > employees_full.sql
grep -c "CREATE.*PROCEDURE\|CREATE.*FUNCTION" employees_full.sql
```
```text
3
```
*Why:* without `--routines`, that count would be **0** — the classic backup trap.

**6. Comparison Tables**

**View vs Table**

| Aspect | View | Base table |
|---|---|---|
| Stores data | ❌ (stores the query) | ✅ |
| Updates | limited (simple views) | full |
| Security | ✅ can hide columns/rows | ❌ all-or-nothing |
| Performance | overhead per query | direct |
| Dependency | on base tables | none |

**Procedure vs Function**

| Aspect | Procedure | Function |
|---|---|---|
| Call | `CALL` | in `SELECT`/`WHERE` |
| Returns | via OUT/INOUT (0+) | one value |
| Params | IN/OUT/INOUT | IN |
| Transaction control | ✅ | ❌ |
| Dumped with `--routines` | ✅ | ✅ |

**Trigger timing**

| | BEFORE | AFTER |
|---|---|---|
| Modify `NEW` | ✅ | ❌ |
| Validate/raise error | ✅ | ✅ |
| Audit/side-effects | possible | ✅ typical |
| Sees committed row | ❌ | ✅ |

**7. ⚠️ Traps, Quirks and Common Mistakes**

> ⚠️ **TRAP — "views always store data."** No — they store a query; no data of their own.
> ⚠️ **TRAP — "all views are updatable."** Aggregates, `DISTINCT`, `GROUP BY`, `UNION`, and many subqueries make a view read-only.
> ⚠️ **TRAP — `mysqldump` and routines.** Without **`--routines`**, procedures/functions are silently omitted from the dump.
> ⚠️ **TRAP — function inside `SELECT` must be deterministic-ish.** With binary logging, a non-deterministic function can require `DETERMINISTIC`/`READS SQL DATA`/`NO SQL` or it errors/logs unsafely.
> ⚠️ **TRAP — `NEW` in a DELETE trigger / `OLD` in an INSERT trigger** — they don't exist; use `OLD` for DELETE, `NEW` for INSERT.
> ⚠️ **TRAP — triggers fire for *every* affected row.** A bulk `UPDATE` fires the trigger per row — performance and recursion hazards.
> ⚠️ **TRAP — functions can't `COMMIT`/`ROLLBACK`.** Use a procedure if you need transaction control.
> ⚠️ **TRAP — `DELIMITER`.** You must change the delimiter to `//` (or similar) when defining routines/triggers, or the client stops at the first `;`.

**8. 💡 Memory Aids**

- **View = "a window, not a room"** (you see data; the data lives elsewhere).
- **Procedure = "does"; Function = "returns"** — *"Functions give you a value, procedures give you an action."*
- **Trigger timing:** *"BEFORE = bodyguard (can block/alter); AFTER = historian (records what happened)."*
- **Backup flag:** *"Routines need `--routines`; triggers tag along by default."*
- **`NEW`/`OLD`:** *"iNsert has NEW; Delete has OLD; Update has both."*

**9. 🎯 Exam Worthiness Analysis**

- **Likelihood: 🔴 High** — the final lab is entirely these objects; they combine with W6 constraints.
- **Likely formats:** multiple choice, true/false, short answer, **code-writing** (create view/trigger/function), scenario (updatable or not?).
- **Sub-points an examiner would test:** view = stored SELECT; view advantages vs disadvantages; view updateability limits; `--routines`; procedure vs function differences (return value, callability in SELECT, transaction control); trigger timing matrix; `NEW`/`OLD`; calling a procedure from a trigger.
- **"I can ___" checklist:**
  - I can create a view that hides columns and joins tables.
  - I can explain when a view is read-only.
  - I can write a stored function usable in `SELECT`.
  - I can write a BEFORE INSERT trigger with `SIGNAL` to enforce a rule.
  - I can state the six trigger timing/event combinations.
  - I can dump a schema **with** routines and verify it.

**10. Quick Self-Check**

1. **What does a view store?** → **A SELECT query**, not data.
2. **Which flag dumps stored procedures/functions?** → **`--routines`.**
3. **Can a function `COMMIT`?** → **No** — only procedures can control transactions.
4. **Which trigger timing can modify `NEW`?** → **BEFORE.**
5. **Which row images exist in an UPDATE trigger?** → **Both `OLD` and `NEW`.**

---

# 🔵 PART 2 — EXAM WORTHINESS MASTER SUMMARY

## 1. Priority Table

| Topic | Likelihood | Why | Typical question type | Est. weight | Study time |
|---|---|---|---|---|---|
| **W2 Architecture & System Mgmt** | 🔴 | biggest lab block; password reset; case sensitivity | MCQ, T/F, CLI scenario, Python | **High** | 3–4 h |
| **W4 Access Control & Roles** | 🔴 | 3 quiz banks; `*.*` vs `%.%`; role activation | MCQ, T/F, command-writing | **High** | 3–4 h |
| **W3 Import / Redirection / Recovery** | 🔴 | 2 quiz banks; `LOAD DATA`, recovery | MCQ, fill-in, CLI, code | **High** | 3 h |
| **W6 Storage Engines & Constraints** | 🔴 | 10 reading-quiz items; ACID; CHAR/DATETIME traps | MCQ, T/F, code-writing | **High** | 2–3 h |
| **W7 Advanced Objects** | 🔴 | final lab; views/procedures/triggers | code-writing, T/F | **High** | 2–3 h |
| **W5 Replication & HA** | 🔴 | AWS lab + reading quiz | MCQ, T/F, diagram | **Med–High** | 2–3 h |
| **W1 Intro & Licensing** | 🟠 | easy recall; port/config/license | MCQ, T/F | **Low–Med** | 1 h |

## 2. Top 10 Most Testable Concepts (ranked)

1. **`*.*` vs `%.%` in GRANT** — the single most-tested syntax trap (W4).
2. **`GRANT role` ≠ activated; `SET DEFAULT ROLE ALL` persists** (W4).
3. **Config changes need a restart** (W2).
4. **Root password reset: Windows `--init-file` vs Linux `--skip-grant-tables` + `FLUSH PRIVILEGES`** (W2).
5. **`secure_file_priv` gates `LOAD DATA INFILE`** (W3).
6. **`DROP DATABASE` = no warning, no undo** (W3).
7. **SQL injection → parameterized queries** (W3).
8. **InnoDB vs MyISAM** (transactions/row-lock/FK) (W6).
9. **ACID meanings** (W6).
10. **View = stored SELECT / procedure vs function / trigger timing** (W7).

## 3. Cross-Topic Connections (the "gotcha" questions)

| Combo | Example combined question |
|---|---|
| W2 + W3 | Reset root (W2), then import a dump (W3) — the lab sequence. |
| W3 + W4 | Create a least-privilege user (W4) who can only `LOAD DATA` (W3) — needs `INSERT` + `FILE` + `secure_file_priv`. |
| W4 + W5 | RDS `mainuser` lacks `SUPER` (W5) → you can't grant everything root can (W4). |
| W5 + W3 | Replication propagates a bad `DELETE` (W5) → recover from a dump (W3). |
| W6 + W7 | A trigger (W7) enforces a rule that a `CHECK` constraint can't express (W6). |
| W6 + W2 | Choose engine + set `lower_case_table_names` — portability of table names. |
| W4 + W2 | Locked-out user vs lost root password — two different recovery paths. |

## 4. "If You Only Have 2 Hours" Triage List

1. **(20 min)** Re-read W4 `*.*` vs `%.%` + role activation; memorize `GRANT/REVOKE/SET DEFAULT ROLE` syntax.
2. **(20 min)** Write both **password-reset workflows** from memory (W2) — most likely scenario question.
3. **(15 min)** `LOAD DATA INFILE` clause list + `secure_file_priv` + 3 import methods (W3).
4. **(15 min)** InnoDB vs MyISAM + ACID + CHAR/DATETIME traps (W6).
5. **(15 min)** View/procedure/function/trigger differences + timing matrix (W7).
6. **(15 min)** Replication "doesn't save you from DELETE" + Multi-AZ vs Read Replica (W5).
7. **(10 min)** Port 3306, `my.cnf`/`my.ini`, GPLv2, MIPS schemas (W1/W2).
8. **(10 min)** Skim the **quiz banks** above — your professor literally wrote them.

## 5. Last-Minute Cram Sheet

```text
PORTS/PATHS   MySQL port 3306 · Linux cfg my.cnf · Windows cfg my.ini
LICENSE       MySQL = GPLv2 (Community) + commercial (Enterprise); free as a web back end
SCHEMAS       default = mysql, information_schema, performance_schema, sys (MIPS)
CASE          Linux: DB/table case-SENSITIVE (lower_case_table_names=0); Windows: insensitive; cols/keywords always insensitive
AUTH          MySQL 8 default = caching_sha2_password (SHA-256); native = legacy SHA-1
RESTART       config edits need a service restart
PWRESET(Win)  stop svc → mysqld --defaults-file=... --init-file=reset.sql → restart → DELETE reset.sql
PWRESET(Lnx)  stop → mkdir /var/run/mysqld + chown mysql → mysqld_safe --skip-grant-tables --skip-networking → FLUSH PRIVILEGES; ALTER USER; → killall mysqld → start
IMPORT        (1) mysql -u root -p -t < f.sql  (2) source f.sql  (3) Workbench
LOAD DATA     ... FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES (c1,@dummy,c3);
SECURE FILE   SHOW VARIABLES LIKE 'secure_file_priv';  (Linux /var/lib/mysql-files/, Win ...\Uploads\)
DROP DB       no warning, no undo, no recycle bin; IF EXISTS only suppresses errors
INJECTION     use parameterized queries: cur.execute(sql, (params,))
GRANT SCOPE   all DBs/tables = *.*  ( NEVER %.% ) ; host wildcard = '%'
USAGE         = no privileges
ROLES         GRANT 'r' TO 'u';  SET ROLE ALL;  SET DEFAULT ROLE ALL TO 'u';  (assign ≠ activate)
UNDO GRANT    REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'u'@'h';
UNDO USER     DROP USER 'u'@'h';
ERROR 1142    "command denied" ; ERROR 1290 secure_file_priv
REPLICATION   Primary binlog → Secondary relay log (I/O + SQL threads); unique server_id; 1:1/1:N/cascaded/dual/ring/multi-source
REPL vs BACKUP replication propagates human error; backups recover it → use BOTH
RDS           Multi-AZ = auto failover; Read Replica = read scale; mainuser lacks SUPER; GTID via parameter group
ENGINES       InnoDB default: ACID, row locks, FK, crash-safe · MyISAM: none of those · MEMORY=RAM · CSV=no index · BLACKHOLE=discards
ACID          Atomicity, Consistency, Isolation, Durability
CONSTRAINTS   NOT NULL, UNIQUE, PRIMARY KEY, FOREIGN KEY, UNSIGNED, CHECK (8.0.16+)
TYPES         TIMESTAMP 1970–2038 (use DATETIME for wider) · CHAR fixed / VARCHAR lean
OBJECTS       view = stored SELECT (often read-only) · procedure CALL, function in SELECT (1 value, no txn control) · trigger BEFORE/AFTER × INSERT/UPDATE/DELETE
BACKUP FLAG   mysqldump --routines  (triggers included by default)
```

---

✅ **Delivered so far:** Part 1 (Topics 1–7) + Part 2 complete.
▶ **Next chunk:** Part 3 — **Practice Exam A (Foundations)** with full answer key and rubric.

# 🔵 PART 3 — PRACTICE EXAMS

# 📝 EXAM A — Foundations (Easier)

> **Course:** Database Administration (MySQL 8) — ACIT3910
> **Exam:** Practice Exam A — Foundations
> **Total marks:** **50** · **Time limit:** **60 minutes**
> **Allowed materials:** none (closed book). Assume a single self-managed MySQL 8 server unless stated.
> **Instructions:** Answer all questions. Marks are shown per question. Every question is tagged with its Week/Topic.

---

## Part I — Multiple Choice (10 marks · 10 × 1)

**Q1 (W1).** MySQL's default TCP/IP port is:
a) 1433  b) 3306  c) 5432  d) 8080

**Q2 (W2).** Which of the following is **NOT** a database that comes pre-installed with MySQL 8?
a) `mysql`  b) `sys`  c) `world`  d) `performance_schema`

**Q3 (W2).** The default authentication plugin in MySQL 8 is:
a) `mysql_native_password`  b) `caching_sha2_password`  c) `sha256_password`  d) `auth_socket`

**Q4 (W3).** Which statement reveals the folder where files must be placed for `LOAD DATA INFILE`?
a) `SHOW VARIABLES LIKE 'datadir';`  b) `SHOW VARIABLES LIKE 'secure_file_priv';`  c) `SELECT @@tmpdir;`  d) `SHOW GRANTS;`

**Q5 (W4).** In a `GRANT`, "all databases and all tables" is written as:
a) `%.%`  b) `*.*`  c) `all.all`  d) `%.*`

**Q6 (W4).** The `USAGE` privilege means:
a) unlimited access  b) no privileges  c) disk-usage metering  d) read-only access

**Q7 (W5).** A `DELETE` run by mistake on the primary will:
a) not affect replicas  b) be replicated to secondaries  c) trigger automatic rollback  d) be blocked by Multi-AZ

**Q8 (W6).** The default table storage engine in MySQL 8 is:
a) MyISAM  b) InnoDB  c) MEMORY  d) CSV

**Q9 (W6).** Which date/time type is limited to the range ~1970–2038?
a) DATETIME  b) TIMESTAMP  c) DATE  d) YEAR

**Q10 (W7).** A view stores:
a) a copy of the data  b) a `SELECT` query  c) an index  d) a physical backup

---

## Part II — True/False with Justification (10 marks · 5 × 2)

*1 mark for True/False, 1 mark for the justification.*

**Q11 (W2).** "If you can start/stop the MySQL service on a machine, you can reset the root password." (**T/F** + justify)

**Q12 (W3).** "`DROP DATABASE` gives a warning and a confirmation prompt before deleting." (**T/F** + justify)

**Q13 (W4).** "`'sammy1'@'10.0.0.233'` and `'sammy1'@'10.44.33.102'` can have different passwords and privileges." (**T/F** + justify)

**Q14 (W6).** "As of MySQL 8, both InnoDB and MyISAM tables support transactions." (**T/F** + justify)

**Q15 (W7).** "`mysqldump` includes stored procedures and functions in the dump by default." (**T/F** + justify)

---

## Part III — Fill in the Blank (6 marks · 6 × 1)

**Q16 (W1).** The MySQL configuration file for **Linux** is named ________ .

**Q17 (W2).** By default on Linux, database and table names are case-________ .

**Q18 (W3).** To skip a file's header row in `LOAD DATA INFILE`, add the clause ________ .

**Q19 (W4).** The privilege that means "no privileges" is ________ .

**Q20 (W5).** The primary server records all changes in its ________ log.

**Q21 (W6).** `CHECK` constraints are enforced as of MySQL version ________ .

---

## Part IV — Short Answer (9 marks · 3 × 3)

**Q22 (W2).** List the main steps to reset the **root** password on **Linux** using safe mode. *(3)*

**Q23 (W5).** Explain why replication alone does **not** protect against an accidental `DELETE`, and what you should combine it with. *(3)*

**Q24 (W7).** Give **two** differences between a stored procedure and a stored function. *(3)*

---

## Part V — Command / Code Writing (10 marks)

**Q25 (W4).** Write the SQL to (a) create user `'app'@'10.0.0.5'` with password `'AppPw!'`, (b) grant it `SELECT` and `INSERT` on the `shop` database only, and (c) display its grants. *(4)*

**Q26 (W3).** Write a `LOAD DATA INFILE` statement that imports a comma-separated CSV with a header row, text values wrapped in double quotes, into `words(word, rank)`, where the file's **third** column is unwanted. *(3)*

**Q27 (W7).** Write a `BEFORE INSERT` trigger on `products` that raises an error when `NEW.stock` is negative. *(3)*

---

## Part VI — Scenario / Multi-part (5 marks · 5 × 1)

**Q28.** A junior DBA edits `my.ini` to change the port to **3305**, but the server still listens on **3306**.
a) Why? *(1)*
b) What must they do? *(1)*
c) Give the command to connect on the new port. *(1)*
d) They now need to import a dump file `shop.sql`. *(1)*
e) Give the command to import it as root. *(1)*

---

## ✅ ANSWER KEY — EXAM A

### Part I — Multiple Choice

| Q | Ans | Why correct | Why the others are wrong |
|---|---|---|---|
| 1 | **b (3306)** | MySQL's compiled default | a=1433 SQL Server; c=5432 PostgreSQL; d=8080 common web proxy |
| 2 | **c (world)** | Only `mysql`, `information_schema`, `performance_schema`, `sys` are default | `world`/`sakila`/`employees` are **samples**, not default |
| 3 | **b** | MySQL 8 built-in default | a = legacy SHA-1; c requires explicit config; d = Linux socket auth for OS accounts |
| 4 | **b** | Names the allowed import dir | a = data dir; c = temp dir; d = privileges, not paths |
| 5 | **b (`*.*`)** | `*` = all DBs/tables | a/ d = invalid grant syntax; c = invented keyword |
| 6 | **b** | `USAGE` = connect-only | a/c/d are the classic misreads |
| 7 | **b** | Replication copies the mistake | a/c/d: replication is not a backup nor a guard |
| 8 | **b (InnoDB)** | Default since 5.5 | a legacy; c RAM-only; d interchange only |
| 9 | **b (TIMESTAMP)** | 32-bit UTC range 1970–2038 | a DATETIME goes to 9999; c/d not the limited type |
| 10 | **b** | A view stores the query | a/c/d describe base tables/backups |

**Rubric:** 1 mark each; no partial credit.

### Part II — True/False with Justification

| Q | T/F | Justification | Marks |
|---|---|---|---|
| 11 | **True** | With service control you can start `mysqld` with `--skip-grant-tables` (Linux) or `--init-file` (Windows) and change root's password without authenticating. | 1 (T/F) + 1 (why) |
| 12 | **False** | `DROP DATABASE` runs immediately — no prompt, no undo, no recycle bin; `IF EXISTS` only suppresses the "unknown database" error. | 2 |
| 13 | **True** | A MySQL account's identity is *name + host*; different hosts ⇒ independent accounts, passwords, and grants. | 2 |
| 14 | **False** | Only InnoDB (and other transactional engines) support transactions; **MyISAM has no transactions** and uses table-level locking, no FK enforcement. | 2 |
| 15 | **False** | Routines are omitted unless you pass **`--routines`**. (Triggers *are* included by default.) | 2 |

### Part III — Fill in the Blank

| Q | Answer |
|---|---|
| 16 | **`my.cnf`** |
| 17 | **sensitive** (case-sensitive) |
| 18 | **`IGNORE n LINES`** (e.g. `IGNORE 1 LINES`) |
| 19 | **`USAGE`** |
| 20 | **binary** (binlog) |
| 21 | **8.0.16** |

### Part IV — Short Answer

**Q22 (W2) — Linux root reset.**
1. `sudo service mysql stop`
2. `sudo mkdir -p /var/run/mysqld && sudo chown mysql:mysql /var/run/mysqld`
3. `sudo mysqld_safe --skip-grant-tables --skip-networking &`
4. `mysql -u root` → `FLUSH PRIVILEGES;` → `ALTER USER 'root'@'localhost' IDENTIFIED BY 'NewPw';`
5. Kill safe-mode: `ps -ef | grep mysqld` → `sudo killall mysqld` → `sudo service mysql start` → verify.
*Rubric:* 1 mark for stop+safe-mode start, 1 for FLUSH + ALTER USER, 1 for cleanup + restart.

**Q23 (W5) — replication vs DELETE.**
A mistaken `DELETE` is a **valid transaction**, so it is written to the binlog and **replayed on every replica** — replication faithfully copies the disaster. Replication is for **hardware** failures and read scaling. You must combine it with **scheduled/logical backups or snapshots** (point-in-time recovery) to recover from human error.
*Rubric:* 1 (delete is replicated), 1 (replication is for HW/HA), 1 (need backups).

**Q24 (W7) — procedure vs function (any two).**
- A **function returns exactly one value** and can be used *inside* `SELECT`/`WHERE`; a procedure is invoked with **`CALL`** and returns values only via OUT/INOUT params.
- A **procedure can control transactions** (`COMMIT`/`ROLLBACK`); a **function cannot**.
- Functions generally must be declared `DETERMINISTIC`/`NO SQL`/`READS SQL DATA` for binlog safety; procedures don't.
*Rubric:* 1 mark per correct distinction, max 3.

### Part V — Command / Code Writing

**Q25 (W4) — 4 marks.**
```sql
CREATE USER 'app'@'10.0.0.5' IDENTIFIED BY 'AppPw!';                       -- 1
GRANT SELECT, INSERT ON shop.* TO 'app'@'10.0.0.5';                        -- 1.5
SHOW GRANTS FOR 'app'@'10.0.0.5';                                          -- 1.5
```
*Rubric:* 1 create-user syntax; 1.5 grant scope `shop.*` + both privileges; 1.5 `SHOW GRANTS FOR`.
*Common wrong answers:* `GRANT ... ON shop.%.` or `ON %.%` (invalid) → lose the scope mark.

**Q26 (W3) — 3 marks.**
```sql
LOAD DATA INFILE '/var/lib/mysql-files/words.csv'
INTO TABLE words
FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"'      -- 1
LINES TERMINATED BY '\n'                                  -- 0.5
IGNORE 1 LINES                                            -- 0.5
(word, rank, @dummy);                                     -- 1
```
*Rubric:* 1 delimiter + enclosure; 0.5 line ending; 0.5 ignore header; 1 column mapping with `@dummy`.
*Common wrong answers:* omitting `@dummy` (raw "too many columns" error) → lose 1.

**Q27 (W7) — 3 marks.**
```sql
DELIMITER //                                              -- 1
CREATE TRIGGER trg_stock_guard BEFORE INSERT ON products
FOR EACH ROW                                              -- 0.5
BEGIN
  IF NEW.stock < 0 THEN                                   -- 0.75
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Stock cannot be negative';  -- 0.75
  END IF;
END//
DELIMITER ;
```
*Rubric:* 1 `DELIMITER` change; 0.5 correct timing/event/`FOR EACH ROW`; 0.75 `NEW.stock` logic; 0.75 `SIGNAL`.

### Part VI — Scenario (Q28)

| Part | Answer | Mark |
|---|---|---|
| a | Config changes don't apply until the server **restarts** — the daemon read `my.ini` at startup. | 1 |
| b | **Restart** the service (`net stop MySQL80` → `net start MySQL80`, or Services.msc). | 1 |
| c | `mysql -u root -p -P 3305` (capital `-P` = port). | 1 |
| d | The dump must be executed against the server (redirection/`source`/Workbench). | 1 |
| e | `mysql -u root -p -P 3305 < shop.sql` | 1 |

---

## 📊 Score Interpretation & Weak-Area Tracker — Exam A

| Topic | Marks available | Your score | If you scored < 60% → restudy |
|---|---|---|---|
| W1 Intro/Licensing | 2 (Q1, Q16) | ___ | port, config filenames, GPLv2 |
| W2 Architecture | 10 (Q2, Q3, Q11, Q17, Q22) | ___ | schemas, auth plugin, password reset |
| W3 Import/Recovery | 8 (Q4, Q12, Q18, Q26) | ___ | import methods, LOAD DATA clauses |
| W4 Access Control | 9 (Q5, Q6, Q13, Q19, Q25) | ___ | `*.*` vs `%.%`, USAGE, host identity |
| W5 Replication | 4 (Q7, Q20, Q23) | ___ | replication vs backups |
| W6 Engines/Constraints | 6 (Q8, Q9, Q14, Q21) | ___ | InnoDB vs MyISAM, date types, CHECK |
| W7 Advanced Objects | 6 (Q10, Q15, Q24, Q27) | ___ | views/routines/triggers, `--routines` |
| Scenario (W2+W3) | 5 (Q28) | ___ | restart + connect + import combo |
| **Total** | **50** | **___** | |

**Guidance:** ≥ 40/50 → move to Exam B. 30–39 → reread the low-topic notes before Exam B. < 30 → do the "2-hour triage list" (Part 2 §4) first, then retake Exam A.

---

▶ **Next chunk:** Exam B (Standard — realistic difficulty) with answer key + rubric.


# 📝 EXAM B — Standard (Realistic Exam Difficulty)

> **Course:** Database Administration (MySQL 8) — ACIT3910
> **Exam:** Practice Exam B — Standard
> **Total marks:** **60** · **Time limit:** **90 minutes**
> **Allowed materials:** none. Assume a MySQL 8.x server and the AWS RDS console available for scenarios.
> **Instructions:** Answer all questions. Marks are shown. Each question is tagged with its Week/Topic.

---

## Part I — Multiple Choice (12 marks · 12 × 1)

**Q1 (W1).** Which of the following is **NOT** one of the top databases in use today?
a) PostgreSQL  b) MySQL  c) MongoDB  d) dBase

**Q2 (W1).** MySQL Community Edition is licensed under:
a) MIT  b) GPLv2  c) Apache 2.0  d) BSD

**Q3 (W2).** On Linux with default settings, which statement is correct?
a) Table names are case-insensitive and column names case-sensitive
b) Table names are case-sensitive and column names case-insensitive
c) Both are case-sensitive
d) Both are case-insensitive

**Q4 (W2).** Which command confirms MySQL is listening on Linux?
a) `ifconfig`  b) `netstat -ltnp`  c) `top`  d) `df -h`

**Q5 (W3).** Which of the following is **NOT** a method to import data into MySQL?
a) Input redirection `< file.sql`  b) `source file.sql`  c) `LOAD DATA INFILE`  d) `sha256sum file.sql`

**Q6 (W3).** Loading a file from a directory not permitted by `secure_file_priv` returns which error?
a) 1045  b) 1142  c) 1290  d) 1064

**Q7 (W4).** `ERROR 1142` means:
a) access denied (bad password)  b) command denied to user  c) unknown database  d) SQL syntax error

**Q8 (W4).** Which command reloads the grant tables after directly editing `mysql.user`?
a) `REFRESH GRANTS;`  b) `FLUSH PRIVILEGES;`  c) `GRANT RELOAD;`  d) `UPDATE mysql.user;`

**Q9 (W5).** On a replica, replicated events are first written to the:
a) binary log  b) relay log  c) error log  d) undo log

**Q10 (W5).** Which RDS feature provides **automatic failover**?
a) Read Replica  b) Multi-AZ deployment  c) Snapshot  d) Parameter group

**Q11 (W6).** Which storage engine accepts writes but discards all data?
a) CSV  b) MEMORY  c) BLACKHOLE  d) ARCHIVE

**Q12 (W7).** Which trigger timing can **modify** the incoming `NEW` row?
a) BEFORE  b) AFTER  c) both  d) neither

---

## Part II — True/False with Justification (10 marks · 5 × 2)

**Q13 (W2).** "In Windows, the only way to start/stop MySQL Server is from the Command Prompt." (**T/F** + justify)

**Q14 (W3).** "There is no built-in recycle bin in MySQL to recover data lost from an accidental `DROP DATABASE`." (**T/F** + justify)

**Q15 (W4).** "`GRANT SELECT ON %.% TO 'user'@'%';` correctly grants SELECT on all databases and tables." (**T/F** + justify)

**Q16 (W5).** "Amazon RDS read replicas must reside in the same region as the primary." (**T/F** + justify)

**Q17 (W6).** "The string `'hello'` consumes less space stored as `CHAR(15)` than as `VARCHAR(15)`." (**T/F** + justify)

---

## Part III — Short Answer (12 marks · 4 × 3)

**Q18 (W2).** Give **two** ways to start/stop the MySQL service on **Windows** and **one** on **Linux**. *(3)*

**Q19 (W3).** Explain what SQL injection is and how **parameterized queries** prevent it. *(3)*

**Q20 (W4).** Explain why the default `'root'@'localhost'` account is restricted from remote connections and why using root for daily operations is discouraged. *(3)*

**Q21 (W6).** Name the four **ACID** properties; briefly define **atomicity** and **durability**. *(3)*

---

## Part IV — Command / Code Writing (16 marks · 4 × 4)

**Q22 (W3).** Write a `LOAD DATA INFILE` to import a **tab-separated, Windows CRLF** file `data.txt` with **two** header lines into `dictionary.words(word, rank)`, with the file's **third** column discarded. *(4)*

**Q23 (W4).** Write SQL to: (a) create roles `HR` and `Sales`; (b) grant `SELECT, INSERT, UPDATE, DELETE` on `company.employees` to `HR`; (c) grant `SELECT` on `company.*` to `Sales`; (d) create user `'ana'@'localhost'`, assign `HR`, and make it auto-activate at login. *(4)*

**Q24 (W6).** Write a `CREATE TABLE inventory` with: auto-increment primary key `id`, `sku VARCHAR(20) NOT NULL UNIQUE`, `qty INT UNSIGNED CHECK (qty >= 0)`, `supplier_id INT`, and a foreign key to `suppliers(id)`. *(4)*

**Q25 (W7).** (a) Write a view `v_emp_dept` that joins `employees` and `departments` and shows each employee's name + department name. (b) Write a deterministic function `with_tax(price)` returning `price*1.13`. *(4)*

---

## Part V — Scenario / Multi-part (10 marks · 2 × 5)

**Q26 (W2 + W4).** You are locked out of a self-managed server as root.
a) On **Windows**, which flag lets a startup script change the root password, and what file must also be referenced first? *(2)*
b) On **Linux**, which two flags let you start safely, and which command must run before `ALTER USER`? *(2)*
c) Besides resetting a password, name one other lockout cause and the command that fixes it. *(1)*

**Q27 (W5 + W3).** Your primary died at 02:00; at 06:00 someone ran `DELETE FROM orders WHERE 1=1` on a surviving primary **before** it was discovered.
a) Why didn't replication save the deleted rows? *(1)*
b) Which log on the primary holds the transaction history? *(1)*
c) Name the RDS feature that would have failed over automatically and the one used to scale reads. *(2)*
d) What must you restore from to recover the deleted rows, and via which RDS feature? *(1)*

---

## ✅ ANSWER KEY — EXAM B

### Part I — Multiple Choice

| Q | Ans | Why | Wrong options |
|---|---|---|---|
| 1 | **d (dBase)** | legacy desktop DB | a/b/c are top-tier |
| 2 | **b (GPLv2)** | Community = GPLv2 | others are unrelated licenses |
| 3 | **b** | `lower_case_table_names=0` on Linux → table names case-sensitive; columns/keywords insensitive | a/c/d invert the rule |
| 4 | **b** | `netstat -ltnp` lists listeners | a=interfaces; c=processes; d=disk |
| 5 | **d** | `sha256sum` is a checksum tool | a/b/c are the three import methods |
| 6 | **c (1290)** | `--secure-file-priv` violation | a=bad password; b=command denied; d=syntax |
| 7 | **b** | command denied to user | a=1045; c=1049; d=1064 |
| 8 | **b** | `FLUSH PRIVILEGES` reloads grant tables | a/c/d don't exist/work |
| 9 | **b (relay log)** | replica writes fetched events to relay log, then applies | a=on primary; c=diagnostics; d=InnoDB on-disk |
| 10 | **b (Multi-AZ)** | standby + auto failover | a=read scale; c=backup; d=config |
| 11 | **c (BLACKHOLE)** | discards writes | a stores CSV; b RAM; d compresses |
| 12 | **a (BEFORE)** | can modify NEW and raise errors | b sees committed row |

**Rubric:** 1 mark each.

### Part II — True/False with Justification

| Q | T/F | Justification |
|---|---|---|
| 13 | **False** | You can also use **Services.msc**, `net start/stop MySQL80`, or the MySQL Installer. |
| 14 | **True** | `DROP DATABASE` is immediate and irreversible; recovery requires a backup. |
| 15 | **False** | "All databases/tables" is **`*.*`**; `%.%` is invalid grant syntax (`%` is only a host wildcard). |
| 16 | **False** | Cross-region read replicas are supported. |
| 17 | **False** | `VARCHAR(15)` stores actual length + 1 byte (~6 bytes for 'hello'); `CHAR(15)` reserves 15 → CHAR uses **more**. |

### Part III — Short Answer

**Q18 (W2).** Windows: ① `net start MySQL80` / `net stop MySQL80`; ② Services console (`services.msc`) → MySQL80 → Start/Stop (also: MySQL Installer / Task Manager→Services). Linux: `sudo service mysql restart` (or `sudo systemctl restart mysql`). *Rubric:* 1 each for the two Windows methods; 1 for the Linux command.

**Q19 (W3).** SQL injection is when user input is concatenated into a query so it changes the query's logic (e.g. `' OR 1=1 -- `). **Parameterized queries** send the SQL text and the data **separately**, so the driver treats the value as data, never as SQL syntax — the placeholder (`%s`) can never become logic. *Rubric:* 1 definition; 1 example/effect; 1 why parameters fix it.

**Q20 (W4).** `'root'@'localhost'` is limited to the local host so the all-powerful admin account is **not reachable over the network** (blocks remote brute force / internet exposure). Using root daily violates **least privilege**: any mistake, injected query, or compromised app would have full destructive power. *Rubric:* 1 localhost restriction purpose; 1 security risk; 1 least-privilege principle.

**Q21 (W6).** Atomicity, Consistency, Isolation, Durability. **Atomicity** — a transaction is all-or-nothing. **Durability** — committed changes survive crashes (persisted). *Rubric:* 1 naming four; 1 atomicity; 1 durability.

### Part IV — Command / Code Writing

**Q22 (W3) — 4 marks.**
```sql
LOAD DATA INFILE '/var/lib/mysql-files/data.txt'
INTO TABLE dictionary.words
FIELDS TERMINATED BY '\t'                      -- 1
LINES TERMINATED BY '\r\n'                     -- 1  (Windows CRLF)
IGNORE 2 LINES                                 -- 1
(word, rank, @dummy);                          -- 1
```
*Common wrong answers:* `'\n'` for a Windows file (leaves `\r`), or omitting `@dummy`.

**Q23 (W4) — 4 marks.**
```sql
CREATE ROLE 'HR', 'Sales';                                              -- 1
GRANT SELECT, INSERT, UPDATE, DELETE ON company.employees TO 'HR';      -- 1
GRANT SELECT ON company.* TO 'Sales';                                   -- 0.5
CREATE USER 'ana'@'localhost' IDENTIFIED BY 'AnaPw!';                   -- 0.5
GRANT 'HR' TO 'ana'@'localhost';                                        -- 0.5
SET DEFAULT ROLE ALL TO 'ana'@'localhost';                              -- 0.5
```
*Rubric:* 1 roles; 1 HR CRUD grant; 0.5 Sales grant; 0.5 create user; 0.5 assign role; 0.5 default role.

**Q24 (W6) — 4 marks.**
```sql
CREATE TABLE inventory (
  id          INT AUTO_INCREMENT PRIMARY KEY,                        -- 1
  sku         VARCHAR(20) NOT NULL UNIQUE,                           -- 1
  qty         INT UNSIGNED CHECK (qty >= 0),                         -- 1
  supplier_id INT,
  CONSTRAINT fk_supplier FOREIGN KEY (supplier_id) REFERENCES suppliers(id)  -- 1
);
```
*Common wrong answers:* `CHECK` placed as a column default; missing `UNSIGNED`; FK without matching type on parent.

**Q25 (W7) — 4 marks.**
```sql
CREATE VIEW v_emp_dept AS                                            -- 2
  SELECT e.first_name, e.last_name, d.dept_name
  FROM employees e JOIN departments d ON e.dept_id = d.id;

DELIMITER //
CREATE FUNCTION with_tax(price DECIMAL(10,2)) RETURNS DECIMAL(10,2) DETERMINISTIC  -- 2
BEGIN RETURN price * 1.13; END//
DELIMITER ;
```
*Rubric:* 2 view (correct join + columns); 2 function (RETURNS + DETERMINISTIC + body).

### Part V — Scenario

**Q26 (W2 + W4)**
| Part | Answer | Marks |
|---|---|---|
| a | Flag **`--init-file`**, and **`--defaults-file`** must be referenced first. | 2 |
| b | **`--skip-grant-tables`** and **`--skip-networking`**; run **`FLUSH PRIVILEGES;`** before `ALTER USER`. | 2 |
| c | The account is **locked** → `ALTER USER 'u'@'h' ACCOUNT UNLOCK;` (accept: expired password → `ALTER USER ... IDENTIFIED BY`). | 1 |

**Q27 (W5 + W3)**
| Part | Answer | Marks |
|---|---|---|
| a | Replication **replays** valid data changes — a `DELETE` is valid, so it was propagated. | 1 |
| b | The **binary log** (binlog). | 1 |
| c | Auto failover = **Multi-AZ**; read scaling = **Read Replica**. | 2 |
| d | Restore from a **snapshot** (or point-in-time backup) via RDS → *Restore from snapshot*. | 1 |

---

## 📊 Score Interpretation & Weak-Area Tracker — Exam B

| Topic | Marks | Your score | If < 60% → restudy |
|---|---|---|---|
| W1 | 2 | ___ | license/port |
| W2 | 12 | ___ | restart, case rules, netstat, reset |
| W3 | 10 | ___ | import methods, LOAD DATA, injection |
| W4 | 12 | ___ | `*.*`, roles, ERROR codes, root risks |
| W5 | 8 | ___ | relay log, Multi-AZ vs Read Replica |
| W6 | 8 | ___ | engines, ACID, CHAR/VARCHAR, constraints |
| W7 | 8 | ___ | views, functions, trigger timing |
| **Total** | **60** | **___** | |

**Guidance:** ≥ 48/60 → attempt Exam C. 36–47 → target the two weakest rows, then redo those questions. < 36 → return to the relevant Part-1 topics before moving on.

---

▶ **Next chunk:** Exam C (Challenge — harder than the real exam) with answer key + rubric, then the master Weak-Area Tracker and Part 4 (Study Plan).

# 📝 EXAM C — Challenge (Harder Than the Real Exam)

> **Course:** Database Administration (MySQL 8) — ACIT3910
> **Exam:** Practice Exam C — Challenge
> **Total marks:** **70** · **Time limit:** **120 minutes**
> **Allowed materials:** none. Multi-topic integration required.
> **Instructions:** Answer all questions. Marks are shown. Every question blends two or more weeks.

---

## Part I — Multiple Choice (10 marks · 10 × 1)

**Q1 (W4+W5).** On AWS RDS, the `mainuser` is **unable** to perform which action?
a) `CREATE USER`  b) `GRANT` privileges  c) `SHUTDOWN` the server  d) `CREATE DATABASE`

**Q2 (W3+W4).** To bulk-load a file with `LOAD DATA INFILE`, a user minimally needs:
a) only `INSERT`  b) `INSERT` + `FILE`  c) `SELECT`  d) `SUPER`

**Q3 (W2+W6).** Which variable controls whether Linux treats table names case-insensitively?
a) `code_page`  b) `lower_case_table_names`  c) `sql_mode`  d) `character_set`

**Q4 (W5).** Enabling `gtid_mode = ON` requires also setting:
a) `binlog_format = STATEMENT`  b) `enforce-gtid-consistency = ON`  c) `log_bin = OFF`  d) `skip_networking = ON`

**Q5 (W6+W7).** Which view is **NOT** updatable?
a) `SELECT * FROM employees`  b) `SELECT dept, COUNT(*) FROM employees GROUP BY dept`  c) both  d) neither

**Q6 (W3).** The correct SQL literal for the Windows path `C:\data\f.txt` in `LOAD DATA INFILE` is:
a) `'C:\data\f.txt'`  b) `'C:\\data\\f.txt'`  c) `'C:/data\\f.txt'`  d) `"C:\data\f.txt"`

**Q7 (W4).** A user granted `... WITH GRANT OPTION` can:
a) bypass authentication  b) pass their granted privileges to other users  c) drop any database  d) change server variables

**Q8 (W2).** `mysqld --verbose --help` is useful because it:
a) starts the server  b) prints compiled-in defaults and all options  c) resets the root password  d) lists tables

**Q9 (W7).** Which `mysqldump` flag ensures stored routines are exported?
a) `--triggers`  b) `--routines`  c) `--events`  d) `--all-databases`

**Q10 (W3).** `SELECT b.* FROM backup.orders b LEFT JOIN prod.orders p ON b.id=p.id WHERE p.id IS NULL` returns:
a) matching rows only  b) rows in backup missing from production  c) rows in production missing from backup  d) all rows

---

## Part II — Debugging: Find and Fix (12 marks · 4 × 3)

*For each, state the error and give the corrected statement.*

**D1 (W4).** 
```sql
GRANT SELECT, INSERT ON %.% TO 'analyst'@'%';
```
*Intended: all databases/tables.*

**D2 (W3).**
```sql
LOAD DATA INFILE 'C:\Users\pm\words.txt'
INTO TABLE dictionary.words
FIELDS TERMINATED BY ',';
```
*Intended: import on a Windows server.* (Two problems.)

**D3 (W7).**
```sql
CREATE TRIGGER trg_log
AFTER INSERT ON employees
BEGIN
  INSERT INTO audit_log(action, emp_id) VALUES ('INSERT', OLD.id);
END;
```
*Intended: log new employees.*

**D4 (W6).**
```sql
CREATE TABLE inventory (
  id  INT PRIMARY KEY,
  sku VARCHAR(20) NOT NULL,
  qty VARCHAR(10) CHECK (qty >= 0)
);
```
*Intended: `qty` must be a non-negative integer, `sku` unique.* (Two problems.)

---

## Part III — Code/Output Tracing (9 marks · 3 × 3)

**T1 (W2).** A fresh MySQL 8 server, logged in as root. What does each line output?
```sql
SHOW DATABASES;
SELECT VERSION();
```
*(Full expected output.)*

**T2 (W4).** After `CREATE USER 'zoe'@'localhost' IDENTIFIED BY 'Pw';`, what does the following output, and what can zoe do?
```sql
SHOW GRANTS FOR 'zoe'@'localhost';
```

**T3 (W5).** A replica reports:
```text
Replica_IO_Running:  Yes
Replica_SQL_Running: Yes
Seconds_Behind_Source: 0
```
Interpret the state, and state what `No` for either thread would imply.

---

## Part IV — Command / Scenario Writing (20 marks · 5 × 4)

**Q11 (W2+W3).** Root's password is lost on a Linux server, and afterwards you must import `employees.sql`. Give the ordered command sequence only (reset + import + verify).

**Q12 (W4).** Create a least-privilege reporting account `'report'@'10.0.0.20'` that may `SELECT` only the columns `first_name, last_name` of `hr.employees`.

**Q13 (W5).** Write the steps (console + SQL) to enable **GTID** on an RDS instance via a parameter group, then configure the replica to auto-position.

**Q14 (W6+W7).** Write a `BEFORE INSERT` trigger on `enrollment` that calls a procedure `check_capacity(NEW.course_id)` raising `'45000 / Course full'` when a course already has ≥ 30 students.

**Q15 (W3).** Rows 100–150 were deleted from `prod.orders`; a backup was loaded into `backup.orders`. Write the LEFT-JOIN query to find the deleted IDs and the `INSERT ... SELECT` to restore them.

---

## Part V — Multi-part Integration (19 marks)

**Q16 (W1, W2, W4, W6, W7 — 9 marks).** You must deploy a new **sales** database. Design it:
a) Which **storage engine** will you choose, and why? *(1)*
b) Write `CREATE TABLE orders` with an auto-increment PK, a `NOT NULL` customer name, a `CHECK`ed positive total, and an FK to `customers(id)`. *(3)*
c) Create role `sales_ro` (read-only on `sales.*`), create user `'jane'@'%'`, assign the role, and auto-activate it. *(2)*
d) Choose an **HA topology** (name it) and justify it for a small business with one primary + two replicas read-heavy traffic. *(2)*
e) State your **backup** strategy in one sentence. *(1)*

**Q17 (W3+W5+W6 — 10 marks).** Post-incident forensics: a bad `DELETE` ran at 06:00; replication propagated it; a nightly dump exists from 00:00.
a) Identify the counterintuitive behaviour of replication here. *(2)*
b) Why is the **binlog** insufficient on its own to "undo" the delete (hint: position-based replay)? *(2)*
c) Describe the recovery procedure from the 00:00 dump into a staging DB and back into production, including the tools. *(3)*
d) Which MySQL feature lets you restore to a precise moment (e.g. 05:59:59)? *(2)*
e) Give one command to verify row counts match after recovery. *(1)*

---

## ✅ ANSWER KEY — EXAM C

### Part I — Multiple Choice

| Q | Ans | Why | Wrong options |
|---|---|---|---|
| 1 | **c (SHUTDOWN)** | RDS `mainuser` lacks `SUPER`/`SHUTDOWN` | a/b/d are permitted |
| 2 | **b (`INSERT`+`FILE`)** | `FILE` reads server files; `INSERT` writes rows | a misses FILE; c/d insufficient |
| 3 | **b** | `lower_case_table_names` | a/c/d unrelated |
| 4 | **b** | GTID needs `enforce-gtid-consistency` | a/c/d break GTID/logging |
| 5 | **b** | aggregate + GROUP BY ⇒ read-only | a is a simple updatable view |
| 6 | **b** | backslash is an escape char → double it | a/c/d are wrong/ambiguous |
| 7 | **b** | propagate privileges | a/c/d are unrelated powers |
| 8 | **b** | prints compiled defaults/options | a/c/d wrong |
| 9 | **b (`--routines`)** | routines excluded by default | a=triggers; c=events; d=scope |
| 10 | **b** | `backup LEFT JOIN prod ... IS NULL` = in backup, not prod | a/c/d misread the join |

**Rubric:** 1 mark each.

### Part II — Debugging

**D1.** *Error:* `%.%` is invalid grant scope. *Fix:*
```sql
GRANT SELECT, INSERT ON *.* TO 'analyst'@'%';
```
*Rubric:* 1 identify, 2 correct statement.

**D2.** *Errors:* ① unescaped backslashes in the Windows path; ② no `FIELDS TERMINATED BY`/mapping may be fine but the file must live in `secure_file_priv` and a Windows path needs `\\` or `/`. *Fix:*
```sql
LOAD DATA INFILE 'C:\\Users\\pm\\words.txt'   -- or 'C:/Users/pm/words.txt'
INTO TABLE dictionary.words
FIELDS TERMINATED BY ',';
-- plus: confirm SHOW VARIABLES LIKE 'secure_file_priv'; and place the file there
```
*Rubric:* 1 escaping, 1 secure_file_priv awareness, 1 corrected statement.

**D3.** *Errors:* ① `OLD` doesn't exist in an `INSERT` trigger → use `NEW`; ② missing `DELIMITER //` around the trigger body so the client stops at the first `;`. *Fix:*
```sql
DELIMITER //
CREATE TRIGGER trg_log AFTER INSERT ON employees
FOR EACH ROW
BEGIN
  INSERT INTO audit_log(action, emp_id) VALUES ('INSERT', NEW.id);
END//
DELIMITER ;
```
*Rubric:* 1 NEW vs OLD, 1 DELIMITER, 1 full fix.

**D4.** *Errors:* ① `qty VARCHAR(10)` should be numeric (`INT UNSIGNED`); ② `sku` isn't `UNIQUE`. *Fix:*
```sql
CREATE TABLE inventory (
  id  INT PRIMARY KEY,
  sku VARCHAR(20) NOT NULL UNIQUE,
  qty INT UNSIGNED CHECK (qty >= 0)
);
```
*Rubric:* 1 type fix, 1 UNIQUE, 1 corrected statement.

### Part III — Tracing

**T1 (expected output — MySQL 8.x):**
```text
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.00 sec)

mysql> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 8.0.36    |
+-----------+
```
*Rubric:* 1 the four MIPS schemas, 1 formatting/no extra DB, 1 version string.

**T2:**
```text
mysql> SHOW GRANTS FOR 'zoe'@'localhost';
+---------------------------------------------+
| Grants for zoe@localhost                    |
+---------------------------------------------+
| GRANT USAGE ON *.* TO `zoe`@`localhost`     |
+---------------------------------------------+
```
Zoe can **connect** and change her own password, but has **no database access** (`USAGE` = no privileges).
*Rubric:* 1 USAGE-only output, 1 interpretation, 1 "can connect but no data access."

**T3:** Both threads running and lag = 0 ⇒ the replica is **healthy and fully caught up**. `Replica_IO_Running: No` ⇒ the replica can't fetch events from the primary (network/credentials). `Replica_SQL_Running: No` ⇒ it fetched but failed to apply events (data conflict/error).
*Rubric:* 1 healthy, 1 IO meaning, 1 SQL meaning.

### Part IV — Command / Scenario Writing

**Q11 (W2+W3) — 4 marks.**
```bash
sudo service mysql stop                                          # 0.5
sudo mkdir -p /var/run/mysqld && sudo chown mysql:mysql /var/run/mysqld   # 0.5
sudo mysqld_safe --skip-grant-tables --skip-networking &         # 0.5
mysql -u root -e "FLUSH PRIVILEGES; ALTER USER 'root'@'localhost' IDENTIFIED BY 'NewPw';"  # 1
ps -ef | grep mysqld; sudo killall mysqld                        # 0.5
sudo service mysql start                                         # 0.5
mysql -u root -p < employees.sql                                 # 0.5
```
*Rubric:* award per step; verify with `SHOW DATABASES; SELECT COUNT(*) ...`.

**Q12 (W4) — 4 marks.**
```sql
CREATE USER 'report'@'10.0.0.20' IDENTIFIED BY 'RpPw!';          -- 1
GRANT SELECT(first_name, last_name) ON hr.employees TO 'report'@'10.0.0.20';  -- 2
SHOW GRANTS FOR 'report'@'10.0.0.20';                            -- 1
```
*Rubric:* 1 create; 2 column-level grant; 1 verify.

**Q13 (W5) — 4 marks.**
```
1. RDS → Parameter groups → Create → gtid-replication-parameters (family mysql8.4).   # 1
2. Set gtid-mode = ON and enforce-gtid-consistency = ON; Apply immediately.           # 1
3. Associate with instance → Reboot.                                                  # 1
4. On replica: CALL mysql.rds_set_source_auto_position(1); then verify
   SHOW VARIABLES LIKE '%gtid%';                                                      # 1
```

**Q14 (W6+W7) — 4 marks.**
```sql
DELIMITER //
CREATE PROCEDURE check_capacity(IN cid INT)
BEGIN
  IF (SELECT COUNT(*) FROM enrollment WHERE course_id = cid) >= 30 THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Course full';
  END IF;
END//
CREATE TRIGGER trg_capacity BEFORE INSERT ON enrollment
FOR EACH ROW
BEGIN
  CALL check_capacity(NEW.course_id);
END//
DELIMITER ;
```
*Rubric:* 1 procedure with SIGNAL; 1 trigger BEFORE INSERT; 1 `CALL ... NEW.course_id`; 1 DELIMITER handling.

**Q15 (W3) — 4 marks.**
```sql
SELECT b.id FROM backup.orders b
LEFT JOIN prod.orders p ON b.id = p.id
WHERE p.id IS NULL AND b.id BETWEEN 100 AND 150;                 -- 2

INSERT INTO prod.orders
SELECT * FROM backup.orders b
WHERE b.id BETWEEN 100 AND 150
  AND b.id NOT IN (SELECT id FROM prod.orders);                  -- 2
```

### Part V — Integration

**Q16 (9 marks)**
| Part | Answer | Marks |
|---|---|---|
| a | **InnoDB** — ACID transactions, row-level locking, FK enforcement, crash safety. | 1 |
| b | ```sql\nCREATE TABLE orders (\n  id INT AUTO_INCREMENT PRIMARY KEY,\n  customer_name VARCHAR(100) NOT NULL,\n  total DECIMAL(10,2) CHECK (total > 0),\n  customer_id INT,\n  CONSTRAINT fk_cust FOREIGN KEY (customer_id) REFERENCES customers(id)\n);``` | 3 |
| c | ```sql\nCREATE ROLE 'sales_ro';\nGRANT SELECT ON sales.* TO 'sales_ro';\nCREATE USER 'jane'@'%' IDENTIFIED BY 'JanePw!';\nGRANT 'sales_ro' TO 'jane'@'%';\nSET DEFAULT ROLE ALL TO 'jane'@'%';``` | 2 |
| d | **1 Primary → N Secondaries** (one primary, two replicas): replicas scale reads + give failover; simple, low-conflict. | 2 |
| e | Nightly `mysqldump` **with `--routines`/`--single-transaction`** to offsite storage + periodic binlog/PITR; keep replication for HA. | 1 |

**Q17 (10 marks)**
| Part | Answer | Marks |
|---|---|---|
| a | A `DELETE` is a **valid, deterministic change**, so it's written to the binlog and **replayed on every replica** — replication multiplies the damage instead of preventing it. | 2 |
| b | The binlog stores **events to redo**, not an inverse; replaying it re-runs the `DELETE`. You can't "rewind" by replaying forward — you must restore a prior state and replay only *up to* a chosen point. | 2 |
| c | Load the 00:00 dump into a **staging DB** (`mysql staging < dump.sql`), copy the deleted rows back with `INSERT INTO prod.orders SELECT * FROM staging.orders WHERE id NOT IN (SELECT id FROM prod.orders)`, then `DROP DATABASE staging;`. | 3 |
| d | **Point-in-time recovery (PITR)** using **binary logs** — restore the base backup then replay binlogs to 05:59:59. | 2 |
| e | `SELECT COUNT(*) FROM prod.orders;` (compare to staging). | 1 |

---

## 📊 MASTER WEAK-AREA TRACKER (A + B + C)

| Topic | Exam A (of 50) | Exam B (of 60) | Exam C (of 70) | Weak? (Y/N) | Restudy target |
|---|---|---|---|---|---|
| W1 | /2 | /2 | — | ___ | port, license |
| W2 | /10 | /12 | /9+ | ___ | reset, case, restart |
| W3 | /8 | /10 | /11 | ___ | LOAD DATA, recovery, injection |
| W4 | /9 | /12 | /10 | ___ | `*.*`, roles, ERROR codes |
| W5 | /4 | /8 | /8 | ___ | relay log, GTID, RDS |
| W6 | /6 | /8 | /10 | ___ | engines, ACID, constraints |
| W7 | /6 | /8 | /9 | ___ | views/routines/triggers |
| Scenario | /5 | /10 | /19 | ___ | combined workflows |
| **Total** | **/50** | **/60** | **/70** | | |

**Guidance:** Any topic where you scored **< 60% on two exams** is your priority — reread that Part-1 topic *and* redo only its questions. Topics ≥ 80% on all three are "dump and go."

---

▶ **Final chunk:** Part 4 — 7-day study plan, compressed 2-day plan, and a day-by-day task list keyed to your Oct 28 midterm.

✅ **Delivered so far:** Part 1 → Topics 1–2 complete (Big Picture → Self-Check + quiz banks).
▶ **Next chunk:** Topic 3 (Week 3 — Data Import, Redirection & Recovery), Topic 4 (Week 4 — Access Control & Roles), then Topics 5–7, then Parts 2–4.

# 🔵 PART 4 — STUDY PLAN

> **Your fixed constraint:** 5 midterms land in one window — Applied Algorithms **Mon Oct 26**, Cloud **Tue Oct 27**, **Database Wed Oct 28 (9 am–12 pm)**, Statistics **Thu Oct 29**, Windows Server **Fri Oct 30**. That means the **7 days right before Oct 28 are shared**, so front-load Database. The plan below anchors Day 1 = **Wed Oct 15** and finishes Oct 21 — well before the crunch — leaving Oct 22–27 for consolidation and cross-course triage.

---

## A. 7-Day Plan (start Oct 15 · Database-primary)

**Daily rhythm:** ~2.5–3 h. Always **30% reading / 70% retrieval** — you already have the notes; the marks come from *recall*. Every session ends with a closed-book blank-page dump.

| Day | Date | Focus (priority order) | Specific tasks | Target |
|---|---|---|---|---|
| **1** | Wed Oct 15 | **W2 Architecture + W4 Access** (🔴🔴) | Read Part-1 W2 & W4. Rewrite the two **password-reset workflows** from memory. Write 5 `GRANT/REVOKE/SET DEFAULT ROLE` statements closed-book. Do **Exam A Q2,3,5,6,13,22,25**. | ≥ 80% on those items |
| **2** | Thu Oct 16 | **W3 Import/Recovery** (🔴) | Read W3. Memorise the `LOAD DATA` clause order + `secure_file_priv`. Blank-dump the 3 import methods. Redo the **LEFT JOIN + INSERT…SELECT** recovery. Do **Exam A Q4,12,18,26**. | Trace recovery with no notes |
| **3** | Fri Oct 17 | **W6 Engines + W7 Objects** (🔴🔴) | Read W6 & W7. Redraw the **InnoDB vs MyISAM** table + **trigger timing matrix** from memory. Write one view, one function, one trigger. Do **Exam A Q8,9,10,14,15,24,27**. | 6/7 correct |
| **4** | Sat Oct 18 | **RETAKE EXAM A** + fix | Closed-book **Exam A** (60 min, timed). Mark with the key. Re-read only the topics you lost marks on. Log weak areas in the Master Tracker. | ≥ 40/50 |
| **5** | Sun Oct 19 | **W5 Replication + W1 Intro** (🔴/🟠) | Read W5 & W1. Draw the **binlog→relay log** flow. Recite Multi-AZ vs Read Replica. Do **Exam B Q1,2,9,10,16,20,27**. | Binlog flow from memory |
| **6** | Mon Oct 20 | **RETAKE EXAM B** + cross-topic traps | Closed-book **Exam B** (90 min, timed). Do the **Cross-Topic Connections** (Part 2 §3). Re-quiz every quiz bank you got wrong. | ≥ 48/60 |
| **7** | Tue Oct 21 | **RETAKE EXAM C** + cram sheet | Closed-book **Exam C** (120 min). Then read the **Last-Minute Cram Sheet** (Part 2 §5) aloud. Write the top-10 concepts from memory. | ≥ 56/70 |

**After Oct 21 (consolidation while juggling other exams):** On **Oct 26–27 evenings**, spend 20–30 min re-reading only the Cram Sheet + writing the two password-reset workflows and one `LOAD DATA`, one `GRANT`, one trigger. That is *retrieval*, not re-reading — it's what survives to exam morning.

---

## B. Compressed 2-Day Plan (use if you're behind)

### Day −1 (the day before, e.g. Oct 27 evening — ~2 h, but split around your Cloud midterm)
| Block | Time | Task |
|---|---|---|
| 1 | 30 min | **W4** — write `GRANT … ON *.*`, role create/assign/activate, `REVOKE ALL …, GRANT OPTION` from memory. |
| 2 | 30 min | **W2** — both password-reset workflows, on paper. Plus `my.cnf`/`my.ini`, port 3306, restart rule. |
| 3 | 25 min | **W3** — 3 import methods + full `LOAD DATA` clause list + `secure_file_priv`. |
| 4 | 20 min | **W6/W7** — InnoDB vs MyISAM, ACID, CHAR vs VARCHAR, DATETIME vs TIMESTAMP, trigger timing matrix. |
| 5 | 15 min | **W5** — relay log + "replication replicates the DELETE" + Multi-AZ vs Read Replica. |

### Day 0 (exam morning — 6:30–8:15 am, ~45 min only, no cramming after)
| Block | Time | Task |
|---|---|---|
| 1 | 20 min | Read the **Cram Sheet** aloud once. |
| 2 | 15 min | Write the **top-10 concepts** from memory (Part 2 §2). |
| 3 | 10 min | Blank-dump `*.*` vs `%.%`, `SET DEFAULT ROLE`, `FLUSH PRIVILEGES`, `--routines`. |

---

## C. The "10 things I must be able to write with my eyes closed"

1. `GRANT SELECT, INSERT ON shop.* TO 'u'@'10.0.0.5';` and `... ON *.*`
2. `CREATE ROLE` → `GRANT role TO user` → `SET DEFAULT ROLE ALL TO user`
3. `REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'u'@'h';` / `DROP USER`
4. Windows root reset (`--defaults-file`, `--init-file`, delete file)
5. Linux root reset (`--skip-grant-tables`, `--skip-networking`, `FLUSH PRIVILEGES`, `ALTER USER`)
6. `LOAD DATA INFILE … FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '"' LINES TERMINATED BY '\n' IGNORE 1 LINES (c1,@dummy,c3);`
7. `SHOW VARIABLES LIKE 'secure_file_priv';`
8. Recovery: `LEFT JOIN … WHERE p.id IS NULL` + `INSERT INTO prod SELECT * FROM backup …`
9. A `BEFORE INSERT` trigger with `SIGNAL SQLSTATE '45000'`
10. `mysqldump … --routines` and, on a replica, `SHOW REPLICA STATUS\G`

---

## D. Day-of exam tactics

- **Read the whole paper first** (2 min). Answer high-confidence recall items (W1/W2 MCQs, T/F) first to bank marks.
- **Tag-as-you-go:** mark any question you hesitate on; return after finishing.
- **Command questions:** write the **scope explicitly** (`ON *.*`) — that's where marks hide.
- **Scenario questions:** list **numbered ordered steps**; examiners give partial credit per step.
- Leave 10 min to check that every marks line adds up and no part is blank.

---

✅ **The full pack is now complete:** Part 1 (Topics 1–7) · Part 2 (Master Summary) · Part 3 (Exams A/B/C + keys + rubrics + trackers) · Part 4 (Study Plan).

Want me to (a) generate a **fresh set of 30 brand-new closed-book practice questions** you *haven't* seen (best for retrieval, since you'd be seeing new Socratic prompts), or (b) export this entire pack as a **downloadable Markdown file**? Say the word and I'll produce it.
