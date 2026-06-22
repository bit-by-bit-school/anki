---
name: Database Theory — Backend Engineering
---

# What are the ACID properties of a database transaction?

ACID is an acronym for the four properties a reliable transaction system guarantees:

| Letter | Property    | Guarantee                                                                                                                       |
| ------ | ----------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **A**  | Atomicity   | A transaction either fully completes or fully fails — no partial writes.                                                        |
| **C**  | Consistency | A transaction can only move the database from one valid state to another, respecting all constraints, triggers, and invariants. |
| **I**  | Isolation   | Concurrent transactions don't see each other's intermediate, uncommitted state.                                                 |
| **D**  | Durability  | Once a transaction commits, its effects survive crashes, power loss, or restarts.                                               |

```
Example: transferring $100 from account A to account B

BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 'A';
UPDATE accounts SET balance = balance + 100 WHERE id = 'B';
COMMIT;

Atomicity:   both updates happen, or neither does — money is never "lost in transit"
Consistency: total balance across both accounts is unchanged
Isolation:   another transaction reading account A mid-transfer doesn't see a
             half-updated state
Durability:  once COMMIT returns, the transfer survives a server crash
```

The term was coined by Härder and Reuter in their 1983 paper formalizing transaction-oriented recovery principles.

**Source:** <https://www.postgresql.org/docs/current/tutorial-transactions.html>
<https://dl.acm.org/doi/10.1145/289.291>

<AnkiTags ACID transactions fundamentals junior/>

# What does "Atomicity" mean and how is it implemented?

Atomicity guarantees a transaction is all-or-nothing — if any part of it fails, the entire transaction is rolled back as if it never happened.

```
BEGIN;
INSERT INTO orders (customer_id, total) VALUES (42, 99.99);
INSERT INTO inventory SET stock = stock - 1 WHERE sku = 'WIDGET-1';
-- if this second statement fails (e.g. constraint violation),
-- the order INSERT above is undone too
COMMIT;  -- only reached if both statements succeeded
```

**How it's implemented:** Most databases use a **write-ahead log (WAL)** — before any change is applied to the actual data files, it's recorded in a sequential log. WAL is primarily what makes **crash recovery** durable: if the system crashes mid-transaction, the database replays the WAL to redo committed work and discard uncommitted work. The everyday `ROLLBACK` case (no crash, just an explicit abort) typically relies on additional mechanisms layered on top of or alongside WAL — PostgreSQL uses MVCC row-version visibility (an aborted transaction's row versions are simply never made visible to others), while other engines like MySQL/InnoDB maintain separate **undo logs** specifically for reversing in-progress changes.

Atomicity is about the **transaction boundary**, not about individual statements being "safe" — a single `UPDATE` affecting a million rows is also atomic: it's all rows or none.

**Source:** <https://www.postgresql.org/docs/current/wal-intro.html>

<AnkiTags ACID atomicity WAL fundamentals junior/>

# What does "Consistency" mean in ACID, and how is it different from CAP's consistency?

In ACID, **consistency** means a transaction can only transition the database between states that satisfy all defined integrity rules: constraints, foreign keys, unique indexes, triggers, and check constraints. The database itself enforces this — it's not something the application guarantees independently.

```
CREATE TABLE accounts (
    id      TEXT PRIMARY KEY,
    balance NUMERIC CHECK (balance >= 0)   -- invariant enforced by the DB
);

-- This transaction is rejected because it would violate the CHECK constraint,
-- preserving consistency even though atomicity would otherwise allow it:
UPDATE accounts SET balance = balance - 1000 WHERE id = 'A';
-- ERROR: new row violates check constraint
```

**This is a different "C" from the CAP theorem.** ACID consistency is about respecting **application-defined invariants** within a single node. CAP consistency is about **all nodes agreeing on the same value** for a piece of data in a distributed system. They share a name but answer completely different questions — interviewers often probe this exact distinction.

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html>

<AnkiTags ACID consistency CAP common-confusion intermediate/>

# What does "Durability" mean and how do databases guarantee it?

Durability guarantees that once a transaction commits, its changes survive any subsequent crash, power failure, or restart — the data is permanently recorded.

**How it's typically implemented:**

1. Before commit, the transaction's changes are written to the **write-ahead log (WAL)** and **fsynced to disk** — forcing the OS to flush its buffers to physical storage, not just to a cache.
2. Only after the WAL write is confirmed durable does the database acknowledge the commit to the client.
3. The actual data file pages may still be updated lazily/asynchronously — if the system crashes, the WAL is replayed on restart to reconstruct any committed-but-not-yet-flushed changes.

```
Without proper fsync:        commit "succeeds" → power loss → data lost
                              (this happens if WAL writes are cached but not
                               flushed to physical disk before acknowledging)

With proper fsync:           commit "succeeds" → power loss → WAL replay on
                              restart restores the committed transaction
```

**Trade-off:** `fsync` is slow (millisecond-scale disk I/O). Systems that disable or delay it (e.g. `synchronous_commit = off` in PostgreSQL) trade durability for write throughput — a deliberate choice for some workloads, not a bug.

**Source:** <https://www.postgresql.org/docs/current/wal-reliability.html>

<AnkiTags ACID durability WAL fundamentals junior intermediate/>

# What is a database transaction and why does it exist?

A transaction is a sequence of one or more operations treated as a **single logical unit of work**. It exists to let an application reason about multi-step changes to the database as if they happened instantaneously and indivisibly, even though the underlying execution involves multiple separate reads and writes.

```
BEGIN;
  -- multiple statements grouped into one unit
  SELECT balance FROM accounts WHERE id = 'A';     -- read
  UPDATE accounts SET balance = balance - 100 WHERE id = 'A';  -- write
  UPDATE accounts SET balance = balance + 100 WHERE id = 'B';  -- write
COMMIT;   -- all changes become visible and permanent at once
-- or
ROLLBACK; -- all changes are discarded, as if nothing happened
```

**Without transactions:** if the application crashes between the two `UPDATE` statements, money disappears from account A without appearing in account B — a real, observable inconsistency.

**With transactions:** the database guarantees the pair of updates either both happen or neither does, regardless of crashes, concurrent access, or errors mid-way.

**Source:** <https://www.postgresql.org/docs/current/tutorial-transactions.html>

<AnkiTags transactions fundamentals junior/>

# What are the four standard SQL transaction isolation levels?

Isolation levels control **how much one transaction can "see" of another transaction's uncommitted or concurrent work.** Stricter isolation gives stronger correctness guarantees at the cost of concurrency/throughput.

| Level                | Prevents                                                                                        |
| -------------------- | ----------------------------------------------------------------------------------------------- |
| **Read Uncommitted** | Nothing — dirty reads are possible                                                              |
| **Read Committed**   | Dirty reads                                                                                     |
| **Repeatable Read**  | Dirty reads, non-repeatable reads                                                               |
| **Serializable**     | Dirty reads, non-repeatable reads, phantom reads — behaves as if transactions ran one at a time |

```
Weaker, faster, more concurrent
Read Uncommitted → Read Committed → Repeatable Read → Serializable
Stronger, slower, less concurrent
```

In practice, **PostgreSQL doesn't implement Read Uncommitted at all** — it treats it identically to Read Committed, because PostgreSQL's MVCC design never exposes uncommitted data. MySQL's InnoDB does implement all four.

**Source:** <https://www.postgresql.org/docs/current/transaction-iso.html>
<https://www.iso.org/standard/63556.html>

<AnkiTags isolation-levels transactions concurrency-control intermediate/>

# What is a dirty read?

A **dirty read** occurs when a transaction reads data written by another transaction that **hasn't committed yet** — and may never commit.

```
Transaction A:                    Transaction B:
BEGIN;
UPDATE accounts
  SET balance = 1000
  WHERE id = 'A';
                                   BEGIN;
                                   SELECT balance FROM accounts
                                     WHERE id = 'A';
                                   -- reads 1000 (uncommitted!)
ROLLBACK;  -- A's update undone
                                   -- B now has a value (1000) that
                                   -- never actually existed in the
                                   -- database's committed history
```

Only the **Read Uncommitted** isolation level permits dirty reads. Every other standard isolation level prevents them. Dirty reads are dangerous because decisions can be made based on data that's later rolled back and effectively never happened.

**Source:** <https://www.postgresql.org/docs/current/transaction-iso.html>

<AnkiTags isolation-levels dirty-read concurrency-control intermediate/>

# What is a non-repeatable read?

A **non-repeatable read** occurs when a transaction reads the same row twice and gets **different values**, because another transaction committed a change to that row in between the two reads.

```
Transaction A:                    Transaction B:
BEGIN;
SELECT balance FROM accounts
  WHERE id = 'A';  -- returns 500
                                   BEGIN;
                                   UPDATE accounts SET balance = 700
                                     WHERE id = 'A';
                                   COMMIT;
SELECT balance FROM accounts
  WHERE id = 'A';  -- returns 700!
                    -- same row, same transaction, different value
COMMIT;
```

**Read Committed** allows this — each individual statement sees only committed data, but different statements within the same transaction can see different committed snapshots. **Repeatable Read** and **Serializable** prevent it by guaranteeing a transaction sees a consistent snapshot for its entire duration.

**Source:** <https://www.postgresql.org/docs/current/transaction-iso.html>

<AnkiTags isolation-levels non-repeatable-read concurrency-control intermediate/>

# What is a phantom read?

A **phantom read** occurs when a transaction re-runs a query with a `WHERE` condition and gets a **different set of rows**, because another transaction inserted or deleted rows matching that condition in between.

```
Transaction A:                          Transaction B:
BEGIN;
SELECT COUNT(*) FROM orders
  WHERE status = 'pending';  -- returns 5
                                         BEGIN;
                                         INSERT INTO orders (status)
                                           VALUES ('pending');
                                         COMMIT;
SELECT COUNT(*) FROM orders
  WHERE status = 'pending';  -- returns 6!
                              -- a "phantom" row appeared
COMMIT;
```

**Difference from non-repeatable read:** a non-repeatable read is about an existing row's value changing; a phantom read is about the **set of rows matching a condition** changing — new rows appearing or disappearing. Only **Serializable** isolation fully prevents phantom reads in the standard model (though PostgreSQL's Repeatable Read also prevents them via its snapshot implementation, which is stricter than the SQL standard requires).

**Source:** <https://www.postgresql.org/docs/current/transaction-iso.html>

<AnkiTags isolation-levels phantom-read concurrency-control intermediate advanced/>

# What is a lost update, and which isolation level prevents it?

A **lost update** occurs when two transactions read the same value, both compute a new value based on it, and the second transaction's write **silently overwrites** the first transaction's write — without either transaction knowing about the other's change.

```
Transaction A:                    Transaction B:
BEGIN;                            BEGIN;
SELECT stock FROM products
  WHERE id = 1;  -- reads 10
                                   SELECT stock FROM products
                                     WHERE id = 1;  -- also reads 10
UPDATE products SET stock = 9
  WHERE id = 1;  -- 10 - 1
COMMIT;
                                   UPDATE products SET stock = 9
                                     WHERE id = 1;  -- ALSO 10 - 1
                                   COMMIT;
-- Two units were sold, but stock only decreased by 1, not 2.
-- B's update silently clobbered A's update.
```

**Prevention:** Use `SELECT ... FOR UPDATE` (pessimistic locking) to lock the row on read, or rely on **Repeatable Read**/**Serializable** isolation combined with retry-on-conflict logic, or use an atomic update expression (`UPDATE products SET stock = stock - 1`) that doesn't require a separate read step at all.

**Caveat:** exactly which isolation levels prevent lost updates by default is **database-specific** — PostgreSQL's Repeatable Read and MySQL/InnoDB's Repeatable Read are not identical implementations and don't give identical guarantees here. Always verify the specific behavior of the database you're using rather than assuming the SQL standard's isolation-level names mean the same thing everywhere.

**Source:** <https://www.postgresql.org/docs/current/explicit-locking.html#LOCKING-ROWS>

<AnkiTags isolation-levels lost-update concurrency-control intermediate advanced/>

# What is MVCC (Multi-Version Concurrency Control)?

MVCC is a concurrency control technique where the database keeps **multiple versions of each row** so that readers never have to wait for writers, and writers never have to wait for readers.

```
Row history for id=1:
  version 1: balance=100, created_by_tx=5,  deleted_by_tx=12
  version 2: balance=80,  created_by_tx=12, deleted_by_tx=null  (current)

Transaction 20 (started before tx 12 committed) sees version 1 (balance=100)
Transaction 21 (started after tx 12 committed)  sees version 2 (balance=80)
```

**Key idea:** instead of locking a row to prevent concurrent access, each transaction gets a consistent **snapshot** of the database as of some point in time. Writes create new row versions rather than overwriting in place; old versions are eventually cleaned up (PostgreSQL's `VACUUM`, MySQL/InnoDB's purge thread).

**Benefits:** readers never block writers and vice versa — dramatically improving concurrency compared to a purely lock-based approach. **Cost:** old row versions must be stored and eventually garbage-collected, and long-running transactions can prevent cleanup ("vacuum bloat" in PostgreSQL).

**Source:** <https://www.postgresql.org/docs/current/mvcc-intro.html>

<AnkiTags MVCC concurrency-control intermediate/>

# How does MVCC interact with isolation levels — why don't readers block writers?

Under MVCC, each transaction operates against a **snapshot** of the database rather than the live, currently-being-modified data. This is what allows readers and writers to proceed without blocking each other.

```
Time →
Tx A (Read Committed): BEGIN ─── SELECT (snapshot taken HERE, per statement) ─── COMMIT
Tx B:                       BEGIN ── UPDATE row ── COMMIT (new version created)
Tx A (Repeatable Read): BEGIN ── SELECT (snapshot taken HERE, for whole tx) ── SELECT (same snapshot) ── COMMIT
```

- **Read Committed:** a fresh snapshot is taken at the start of **each statement** — so a transaction can see different committed data across different statements (allowing non-repeatable reads).
- **Repeatable Read / Serializable:** a single snapshot is taken at the start of the **transaction** and reused for every statement within it — rows committed by other transactions afterward are invisible.

In a pure MVCC system, a writer never has to wait for a reader to finish, because the writer is creating a brand-new row version, not modifying the one the reader is looking at. This is fundamentally different from lock-based systems, where a writer must wait for all readers holding a shared lock to release it.

**Source:** <https://www.postgresql.org/docs/current/mvcc-intro.html>
<https://www.postgresql.org/docs/current/transaction-iso.html>

<AnkiTags MVCC isolation-levels concurrency-control intermediate advanced/>

# What is the difference between pessimistic and optimistic concurrency control?

**Pessimistic concurrency control** assumes conflicts are likely, so it **locks** data before allowing it to be modified — other transactions must wait for the lock to be released.

```sql
BEGIN;
SELECT * FROM seats WHERE id = 42 FOR UPDATE;  -- locks the row immediately
-- other transactions trying to UPDATE or SELECT ... FOR UPDATE this row will block
UPDATE seats SET booked = true WHERE id = 42;
COMMIT;  -- lock released
```

**Optimistic concurrency control (OCC)** assumes conflicts are rare, so it lets transactions proceed without locking, then **checks for conflicts at commit time** — typically using a version number or timestamp column.

```sql
-- Read the row along with its current version
SELECT id, version, booked FROM seats WHERE id = 42;  -- version = 5

-- Attempt the update, conditioned on the version being unchanged
UPDATE seats SET booked = true, version = 6
WHERE id = 42 AND version = 5;
-- if another transaction already changed it, version no longer matches 5,
-- 0 rows are affected, and the application must retry
```

**When to use which:** pessimistic locking suits high-contention workloads where retries are expensive (e.g. seat booking); optimistic concurrency suits low-contention workloads where most attempts succeed on the first try and locking overhead would be wasted (e.g. most web form edits).

**Source:** <https://www.postgresql.org/docs/current/explicit-locking.html>

<AnkiTags concurrency-control optimistic-locking pessimistic-locking intermediate/>

# What is a deadlock and how do databases detect/resolve it?

A deadlock occurs when two or more transactions are each waiting for a lock held by the other, so **none of them can ever proceed**.

```
Transaction A:                    Transaction B:
BEGIN;                            BEGIN;
UPDATE accounts SET ...
  WHERE id = 1;  -- locks row 1
                                   UPDATE accounts SET ...
                                     WHERE id = 2;  -- locks row 2
UPDATE accounts SET ...
  WHERE id = 2;  -- BLOCKS, waiting
                  -- for B's lock on row 2
                                   UPDATE accounts SET ...
                                     WHERE id = 1;  -- BLOCKS, waiting
                                                     -- for A's lock on row 1
-- Both transactions are now waiting on each other forever.
```

**Detection:** databases periodically check for **cycles in the wait-for graph** — if transaction A is waiting on B, and B is waiting on A, that's a cycle. PostgreSQL checks every `deadlock_timeout` (default 1 second).

**Resolution:** the database automatically picks one transaction as the **victim**, aborts it (returning a deadlock error to that client), and lets the others proceed. The aborted transaction's application code is expected to **retry**.

**Prevention:** always acquire locks in a **consistent order** across all code paths (e.g. always lock lower IDs first) to avoid the circular wait condition entirely.

**Source:** <https://www.postgresql.org/docs/current/explicit-locking.html#LOCKING-DEADLOCKS>

<AnkiTags deadlock concurrency-control locking intermediate advanced/>

# What is two-phase locking (2PL)?

Two-phase locking is a concurrency control protocol that guarantees **serializability** by dividing a transaction's lifetime into two distinct phases:

1. **Growing phase:** the transaction can acquire locks but **never release** any.
2. **Shrinking phase:** the transaction can release locks but **never acquire** any new ones.

```
Time →
Transaction:  [acquire lock A] [acquire lock B] [acquire lock C] | [release A] [release B] [release C]
              └──────────── growing phase ──────────────────────┘└──────── shrinking phase ─────────┘
                                                          ↑
                                              once any lock is released,
                                              no new locks may be acquired
```

**Why this guarantees serializability:** once a transaction starts releasing locks, it can no longer expand its "footprint" — this prevents certain interleavings that would otherwise produce non-serializable results.

**Strict 2PL** (used by most real databases) holds **all locks until commit or rollback**, rather than releasing them as soon as the shrinking phase begins — this also prevents cascading rollbacks, since no other transaction can see a lock-protected row until the holder has definitively committed or aborted.

**Source:** <https://www.postgresql.org/docs/current/explicit-locking.html>

<AnkiTags 2PL locking concurrency-control serializability advanced/>

# What is the difference between shared locks and exclusive locks?

**Shared lock (read lock, `S`):** Multiple transactions can hold a shared lock on the same row simultaneously — readers don't block other readers.

**Exclusive lock (write lock, `X`):** Only one transaction can hold an exclusive lock on a row at a time, and no other transaction can hold any lock (shared or exclusive) on it concurrently — writers block everyone.

|                            | Shared (S)             | Exclusive (X)                               |
| -------------------------- | ---------------------- | ------------------------------------------- |
| Compatible with another S? | ✅ Yes                 | ❌ No                                       |
| Compatible with another X? | ❌ No                  | ❌ No                                       |
| Typical trigger            | `SELECT ... FOR SHARE` | `UPDATE`, `DELETE`, `SELECT ... FOR UPDATE` |

```sql
-- Transaction A: shared lock — allows other readers, blocks writers
SELECT * FROM accounts WHERE id = 1 FOR SHARE;

-- Transaction B: exclusive lock — blocks everyone else until commit/rollback
UPDATE accounts SET balance = balance - 50 WHERE id = 1;
```

This S/X locking model is the foundation of pessimistic concurrency control in lock-based databases; MVCC-based systems use it more sparingly since version snapshots handle most read/write conflicts without locking.

**Source:** <https://www.postgresql.org/docs/current/explicit-locking.html#LOCKING-ROWS>

<AnkiTags locking shared-lock exclusive-lock concurrency-control intermediate/>

# What is "select for update" and when do you need it?

`SELECT ... FOR UPDATE` reads rows and immediately takes an **exclusive lock** on them, preventing other transactions from modifying (or also locking) those rows until the current transaction commits or rolls back.

```sql
BEGIN;
-- Lock the row so no one else can also "claim" this seat concurrently
SELECT * FROM seats WHERE id = 42 AND booked = false FOR UPDATE;

-- Safe to proceed: we know no one else can modify this row right now
UPDATE seats SET booked = true WHERE id = 42;
COMMIT;
```

**Why it's needed:** without it, a plain `SELECT` followed by an `UPDATE` has a race condition — two transactions could both read `booked = false`, both decide to book the seat, and both succeed, resulting in a double-booking (a classic **lost update**).

**`SKIP LOCKED` variant:** useful for queue-like workloads — instead of waiting for a locked row, skip it and grab the next available one:

```sql
SELECT * FROM job_queue WHERE status = 'pending'
ORDER BY created_at LIMIT 1 FOR UPDATE SKIP LOCKED;
```

This lets multiple workers pull from the same queue table without blocking each other.

**Source:** <https://www.postgresql.org/docs/current/sql-select.html#SQL-FOR-UPDATE-SHARE>

<AnkiTags locking select-for-update concurrency-control intermediate advanced/>

# What is a write skew anomaly, and why does it slip past Repeatable Read?

Write skew occurs when two transactions each read overlapping data, then make decisions and write to **disjoint** rows based on what they read — and the combination of their two independent writes violates an invariant that neither transaction individually violated.

```
Constraint: at least one doctor must always be on call.

Transaction A:                       Transaction B:
BEGIN;                                BEGIN;
SELECT COUNT(*) FROM doctors
  WHERE on_call = true;  -- returns 2
                                       SELECT COUNT(*) FROM doctors
                                         WHERE on_call = true;  -- also 2
-- "2 on call, safe for me to go off"
UPDATE doctors SET on_call = false
  WHERE name = 'Alice';
COMMIT;
                                       -- "2 on call, safe for me to go off"
                                       UPDATE doctors SET on_call = false
                                         WHERE name = 'Bob';
                                       COMMIT;
-- Both committed successfully. Now ZERO doctors are on call —
-- the invariant is violated, even though each transaction only
-- touched its own row and Repeatable Read prevented direct conflicts.
```

**Why Repeatable Read misses it:** each transaction only writes to a row the _other_ transaction never wrote to, so there's no direct write-write conflict to detect. **Only true Serializable isolation** (using techniques like Serializable Snapshot Isolation) detects this by tracking read-write dependencies across transactions, not just write-write conflicts.

**Source:** <https://www.postgresql.org/docs/current/transaction-iso.html#XACT-SERIALIZABLE>

<AnkiTags isolation-levels write-skew serializability advanced/>

# What is Serializable Snapshot Isolation (SSI) and how does PostgreSQL implement true Serializable?

Traditional Serializable isolation (via strict two-phase locking) guarantees correctness but can be slow due to heavy lock contention. **Serializable Snapshot Isolation (SSI)** achieves the same guarantee with better concurrency by combining MVCC snapshots with **runtime dependency tracking**.

**How it works:**

1. Transactions run using ordinary MVCC snapshots (no blocking on reads).
2. The database tracks which rows each transaction **read** and **wrote**.
3. At commit time, it checks for specific dangerous patterns of read-write dependencies between concurrent transactions (the kind that cause write skew).
4. If such a pattern is detected, **one of the transactions is aborted** with a serialization failure, and the application must retry.

```sql
BEGIN ISOLATION LEVEL SERIALIZABLE;
-- ... reads and writes ...
COMMIT;
-- may return: ERROR: could not serialize access due to read/write dependencies
-- application must catch this and retry the transaction
```

**Trade-off:** SSI doesn't block transactions during execution (good for throughput), but it **can abort transactions at commit time** that a lock-based approach would have simply made wait. Applications using Serializable isolation must always be prepared to retry on serialization failures.

**Source:** <https://www.postgresql.org/docs/current/transaction-iso.html#XACT-SERIALIZABLE>

<AnkiTags isolation-levels SSI serializability PostgreSQL advanced/>

# What is the difference between a statement timeout and a lock timeout?

Both are protective mechanisms against runaway or stuck queries, but they guard against different problems.

**Statement timeout:** aborts a query if it runs longer than a threshold, regardless of _why_ it's slow (e.g. a full table scan, a complex join, or being blocked on a lock).

```sql
SET statement_timeout = '5s';  -- PostgreSQL: abort any statement after 5 seconds
```

**Lock timeout:** aborts a query specifically if it has been **waiting to acquire a lock** for longer than a threshold — it doesn't count time spent actually executing.

```sql
SET lock_timeout = '2s';  -- PostgreSQL: give up waiting for a lock after 2 seconds
```

```
Query lifecycle:  [waiting for lock: 3s] → [executing: 1s]
lock_timeout=2s:      ABORTED here (waited too long for the lock)
statement_timeout=5s: would NOT have aborted (total time was 4s)
```

**Why both matter in production:** a lock timeout catches contention problems early (e.g. another long transaction holding a row lock) without waiting for the full statement timeout, giving faster, more specific failure signals.

**Source:** <https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT>
<https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT>

<AnkiTags locking timeouts concurrency-control intermediate advanced/>

# What is the CAP theorem?

The CAP theorem, proposed by Eric Brewer, states that a **distributed data store** can provide at most **two** of the following three guarantees simultaneously:

- **Consistency (C):** every read receives the most recent write or an error — all nodes see the same data at the same time.
- **Availability (A):** every request receives a (non-error) response, without guarantee that it contains the most recent write.
- **Partition tolerance (P):** the system continues to operate despite an arbitrary number of dropped or delayed messages between nodes (a network partition).

**The practical framing used in interviews:** network partitions _will_ happen in any real distributed system — so partition tolerance isn't really optional. The actual design choice CAP forces is: **when a partition occurs, do you sacrifice consistency or availability?**

```
Partition occurs between two data centers:

CP choice: reject requests on the minority side until the partition heals
           (consistency preserved, availability sacrificed)

AP choice: keep serving requests on both sides, potentially with stale or
           conflicting data, and reconcile afterward
           (availability preserved, consistency sacrificed)
```

**Source:** <https://dl.acm.org/doi/10.1145/564585.564601>
<https://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf>

<AnkiTags CAP distributed-systems fundamentals intermediate/>

# What is the difference between CAP's "Consistency" and ACID's "Consistency"?

These two C's are commonly confused because they share a name, but they describe entirely different guarantees.

|                 | ACID Consistency                                                                                                       | CAP Consistency                                                            |
| --------------- | ---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **Scope**       | A single transaction on a single node                                                                                  | Reads across multiple nodes/replicas in a distributed system               |
| **Guarantee**   | Transactions only move the database between states that satisfy defined constraints (foreign keys, checks, uniqueness) | Every read sees the most recent write, regardless of which replica answers |
| **Violated by** | A bad transaction bypassing application logic                                                                          | Replication lag causing a stale read from a lagging replica                |

```
ACID consistency example: a CHECK constraint prevents a negative balance,
                           regardless of how many transactions run concurrently

CAP consistency example:  Node A has the latest write; Node B hasn't
                          replicated it yet. A read routed to Node B
                          violates CAP consistency, even though no ACID
                          constraint was ever broken
```

A system can have perfect ACID consistency on every individual node while still being CAP-inconsistent across nodes due to replication lag.

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html>
<https://dl.acm.org/doi/10.1145/564585.564601>

<AnkiTags CAP ACID common-confusion distributed-systems intermediate advanced/>

# What is PACELC and how does it extend CAP?

PACELC (proposed by Daniel Abadi) extends CAP by recognizing that CAP only describes behavior **during a network partition** — it says nothing about the trade-offs a system makes the rest of the time, when everything is working normally.

**PACELC reads as:** "if there is a **P**artition, choose between **A**vailability and **C**onsistency; **E**lse (no partition), choose between **L**atency and **C**onsistency."

```
PA/EL — Prioritizes availability during a partition, prioritizes low latency
        otherwise. Common "best effort" position. (e.g. Cassandra, DynamoDB)

PC/EL — Prioritizes consistency during a partition, but optimizes for
        latency in the normal case. Rare in practice.

PC/EC — Prioritizes consistency in both situations — maximum correctness,
        at the cost of latency and availability. (e.g. traditional
        synchronously-replicated relational systems)

PA/EC — Prioritizes availability during partitions but consistency otherwise.
        A "best of both worlds" position some systems target.
```

**Why it matters more than CAP for real system design:** most of the time, there's no partition — so the latency/consistency trade-off a system makes during normal operation (e.g. whether a write must be acknowledged by all replicas before returning) affects users far more often than the rare partition event CAP focuses on.

**Source:** <https://www.cs.umd.edu/~abadi/papers/abadi-pacelc.pdf>

<AnkiTags PACELC CAP distributed-systems advanced/>

# What is the BASE model and how does it contrast with ACID?

BASE is an alternative set of guarantees, common in NoSQL systems, that intentionally trades strict correctness for **availability and scalability**.

|                          | ACID                                           | BASE                                                                                             |
| ------------------------ | ---------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **B**asically Available  | —                                              | The system guarantees availability, even under failure, possibly returning stale or partial data |
| **S**oft state           | —                                              | The system's state may change over time even without new input, as replicas converge             |
| **E**ventual consistency | —                                              | Given enough time without new writes, all replicas will converge to the same value               |
| Underlying philosophy    | Strong guarantees, pessimistic about conflicts | Availability first, optimistic that conflicts can be resolved later                              |

```
ACID system (e.g. PostgreSQL primary):
  Write → strongly consistent, immediately visible everywhere, but the
  system may become unavailable rather than risk an inconsistent state

BASE system (e.g. Cassandra, DynamoDB):
  Write → acknowledged quickly, propagates to replicas asynchronously;
  a read shortly after might see the old value, but it converges eventually
```

**Where each fits:** ACID for financial transactions, inventory counts, anything requiring strict correctness. BASE for activity feeds, view counters, product catalogs, shopping carts — domains that tolerate brief staleness in exchange for always being responsive.

**Source:** <https://www.allthingsdistributed.com/2007/12/eventually_consistent.html>

<AnkiTags BASE ACID NoSQL eventual-consistency intermediate/>

# What is eventual consistency?

Eventual consistency guarantees that **if no new writes are made to a data item, all replicas will eventually converge to the same value** — but offers no guarantee about how long convergence takes, or what intermediate reads will return in the meantime.

```
t=0: Write "X=5" goes to replica 1
t=1: A read from replica 2 might still return the old value "X=3"
     (replica 2 hasn't received the update yet)
t=5: Replica 2 receives the update via asynchronous replication
t=6: A read from replica 2 now returns "X=5" — converged
```

**Important nuance:** eventual consistency says nothing about ordering, causality, or how stale a read can be — it's a fairly weak guarantee on its own. Real systems typically layer stronger guarantees on top, such as:

- **Read-your-writes consistency:** a client always sees its own writes, even if other clients might see stale data.
- **Monotonic reads:** once a client has seen a value, it never sees an older value on a subsequent read.
- **Causal consistency:** operations that are causally related are seen by everyone in the same order.

**Source:** <https://www.allthingsdistributed.com/2007/12/eventually_consistent.html>

<AnkiTags eventual-consistency consistency-models distributed-systems intermediate/>

# What is the difference between strong consistency, causal consistency, and eventual consistency?

These represent points along a spectrum from strictest to weakest guarantee:

| Model                                    | Guarantee                                                                                                                                                   | Cost                                                                                                                |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Strong consistency** (linearizability) | Every read sees the latest write, as if there were only one copy of the data                                                                                | Highest latency; often requires coordination (quorum, consensus) on every operation                                 |
| **Causal consistency**                   | Operations that are causally related (e.g. a reply to a comment) are seen by all observers in the same order; unrelated operations can be seen in any order | Requires tracking causal dependencies (e.g. via vector clocks), but allows more concurrency than strong consistency |
| **Eventual consistency**                 | All replicas converge given enough time and no new writes; no ordering or recency guarantee in the meantime                                                 | Lowest latency, highest availability; weakest guarantee                                                             |

```
Example — a social media comment thread:

Strong consistency:  every user sees the exact same comment order globally,
                     instantly, no matter which server they hit

Causal consistency:  a reply always appears after the comment it replies to,
                      but two unrelated top-level comments might appear in
                      different orders to different users

Eventual consistency: comments eventually show up for everyone, but a reply
                       might briefly appear before the comment it replies to
```

**Choosing between them:** causal consistency is often the practical sweet spot for collaborative or social applications — it preserves the orderings users actually notice (cause-and-effect) without paying for full strong consistency.

**Source:** <https://www.allthingsdistributed.com/2007/12/eventually_consistent.html>

<AnkiTags consistency-models causal-consistency distributed-systems intermediate advanced/>

# What is quorum-based consistency (the N/W/R model)?

Quorum-based systems (used by Cassandra, DynamoDB-style databases) tune consistency vs. availability by configuring three numbers:

- **N:** the number of replicas a piece of data is stored on.
- **W:** the number of replicas that must acknowledge a **write** before it's considered successful.
- **R:** the number of replicas that must respond to a **read** before a value is returned to the client.

```
N = 3  (data replicated to 3 nodes)

W = 3, R = 1  → strong write guarantee, fast reads, but a single slow/down
                replica blocks all writes
W = 1, R = 3  → fast writes, but reads must check all 3 replicas to be sure
                they have the latest value
W = 2, R = 2  → "quorum" config: W + R > N guarantees every read overlaps
                with at least one replica that has the latest write
                (strong consistency, balanced latency)
W = 1, R = 1  → fastest possible, but reads can return stale data
                (eventual consistency)
```

**The key formula:** if **W + R > N**, every read is guaranteed to overlap with at least one node holding the latest write, giving strong consistency. If `W + R ≤ N`, reads might miss the latest write entirely.

**Source:** <https://cassandra.apache.org/doc/latest/cassandra/architecture/dynamo.html>
<https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadConsistency.html>

<AnkiTags quorum consistency-models distributed-systems Cassandra DynamoDB advanced/>

# What is a vector clock and what problem does it solve?

A vector clock is a mechanism for tracking **causality** between events across distributed nodes, without requiring synchronized physical clocks. It helps a system determine whether one version of data **caused**, was **caused by**, or is **concurrent with (conflicting with)** another version.

```
Each node maintains a vector of counters, one per node:
  Node A's clock: [A:1, B:0, C:0]
  Node A writes → increments its own counter: [A:2, B:0, C:0]
  Node A sends this to Node B
  Node B writes, incorporating A's clock: [A:2, B:1, C:0]

Comparing two vector clocks:
  [A:2, B:1, C:0] vs [A:2, B:0, C:0]
  → the first "dominates" the second (every counter ≥), so it's CAUSALLY AFTER it

  [A:2, B:1, C:0] vs [A:1, B:0, C:1]
  → neither dominates the other → these are CONCURRENT (conflicting) writes
```

**Why it matters:** in an eventually-consistent, multi-writer system (e.g. Dynamo-style databases), two replicas might receive conflicting writes to the same key. Vector clocks let the system distinguish "this write supersedes that one" from "these are genuinely conflicting writes that need application-level resolution" (e.g. shopping cart merge logic, or last-write-wins, or CRDTs).

**Source:** <https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html>

<AnkiTags vector-clocks causal-consistency conflict-resolution distributed-systems advanced/>

# What is last-write-wins (LWW) conflict resolution, and what's its downside?

Last-write-wins resolves conflicting concurrent writes to the same key by simply keeping the write with the **latest timestamp** and discarding the others.

```
Replica A receives: SET cart_items = ["shoes"]       at t=100
Replica B receives: SET cart_items = ["shoes", "hat"] at t=101 (concurrent write)

LWW resolution: keep the write at t=101 → cart_items = ["shoes", "hat"]
                the write at t=100 is silently discarded
```

**The downside:** LWW is simple to implement but **silently loses data** whenever concurrent writes genuinely both contained meaningful information. In the example above, if instead the t=100 write had added "hat" and the t=101 write (from a different device) had added "gloves", LWW would discard one of them — neither item list is "wrong," but the user loses an item they added.

**Clock skew risk:** LWW depends on accurate, synchronized clocks across nodes. If clocks drift, a genuinely earlier write with a skewed-forward clock can incorrectly "win" over a later write.

**Alternatives:** CRDTs (Conflict-free Replicated Data Types) merge concurrent writes intelligently (e.g. a set-union for a shopping cart) instead of discarding one side; application-level merge logic; or surfacing the conflict to the user.

**Source:** <https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html>

<AnkiTags conflict-resolution last-write-wins eventual-consistency distributed-systems advanced/>

# What is a CRDT (Conflict-free Replicated Data Type)?

A CRDT is a data structure specifically designed so that **concurrent updates from different replicas can always be merged automatically**, without conflicts and without coordination, while guaranteeing all replicas converge to the same final state.

```
Example — a G-Counter (grow-only counter) CRDT:
  Each replica tracks its own increment count separately:
    Replica A: {A: 5, B: 0, C: 0}
    Replica B: {A: 0, B: 3, C: 0}
  Merging two replicas = element-wise MAX of each node's count:
    merge({A:5,B:0,C:0}, {A:0,B:3,C:0}) = {A:5, B:3, C:0}
  Total value = sum of all counters = 8
  This merge is commutative, associative, and idempotent —
  it produces the same result no matter the order replicas sync in.

Example — an OR-Set (observed-remove set) CRDT used for shopping carts:
  Adding "shoes" on replica A and "hat" on replica B concurrently
  merges to {"shoes", "hat"} automatically — no item is lost,
  unlike with last-write-wins.
```

**Why this matters:** CRDTs let multi-writer, eventually-consistent systems avoid the silent-data-loss problem of last-write-wins, at the cost of being limited to specific data structures (counters, sets, sequences) that have well-defined merge semantics — you can't make an arbitrary JSON document into a CRDT for free.

**Source:** <https://hal.inria.fr/inria-00609399/document>

<AnkiTags CRDT conflict-resolution distributed-systems advanced/>

# What are the database normal forms (1NF, 2NF, 3NF)?

Normalization is the process of structuring relational tables to **reduce data redundancy** and prevent certain classes of update anomalies, by progressively enforcing stricter rules.

**1NF (First Normal Form):** every column holds a single, atomic value — no repeating groups or arrays in a single column.

```
Violates 1NF:                          Satisfies 1NF:
| id | phones           |              | id | phone        |
| 1  | "555-1, 555-2"   |    →         | 1  | 555-1        |
                                        | 1  | 555-2        |
```

**2NF (Second Normal Form):** must be in 1NF, and every non-key column must depend on the **entire** primary key, not just part of it (relevant for composite keys).

```
Violates 2NF (composite key: order_id + product_id):    Satisfies 2NF:
| order_id | product_id | product_name | quantity |      Split into:
   product_name depends only on product_id,              orders(order_id, product_id, quantity)
   not on the full composite key → 2NF violation          products(product_id, product_name)
```

**3NF (Third Normal Form):** must be in 2NF, and no non-key column may depend on another **non-key** column (no transitive dependency).

```
Violates 3NF:                                  Satisfies 3NF:
| emp_id | dept_id | dept_location |           employees(emp_id, dept_id)
   dept_location depends on dept_id,           departments(dept_id, dept_location)
   not directly on emp_id → transitive dependency
```

These forms were originally defined by E.F. Codd in his foundational relational model papers.

**Source:** <https://dl.acm.org/doi/10.1145/362384.362685>

<AnkiTags normalization 1NF 2NF 3NF schema-design intermediate/>

# What is BCNF (Boyce-Codd Normal Form) and how does it differ from 3NF?

BCNF is a stricter version of 3NF. A table is in BCNF if, for **every** functional dependency `X → Y`, `X` is a **superkey** (a candidate key or a superset of one) — even dependencies involving candidate keys other than the chosen primary key must satisfy this.

**The gap 3NF leaves open:** 3NF only restricts dependencies on **non-key** attributes. It's possible to satisfy 3NF while still having a functional dependency where the determinant isn't a key at all, if the dependent attribute happens to be part of some candidate key.

```
Table: course_enrollment(student_id, course_id, instructor)
Assume: each course has exactly one instructor (course_id → instructor)
        and each instructor teaches exactly one course (instructor → course_id)

Candidate keys: {student_id, course_id} and {student_id, instructor}

This satisfies 3NF (instructor is part of a candidate key, so the
transitive-dependency rule doesn't flag it), BUT it violates BCNF because
course_id → instructor exists and course_id alone is not a superkey.

Fix: split into
  enrollments(student_id, course_id)
  course_instructors(course_id, instructor)
```

**Trade-off:** BCNF eliminates more redundancy than 3NF but can occasionally make certain multi-attribute constraints harder to enforce declaratively — in practice, most schemas stop at 3NF unless a specific BCNF violation is identified.

**Source:** <https://dl.acm.org/doi/10.1145/356635.356646>

<AnkiTags normalization BCNF 3NF schema-design advanced/>

# What are functional dependencies and why do they matter for normalization?

A functional dependency `X → Y` means: **given a value for X, there is exactly one corresponding value for Y** — knowing X always tells you Y, unambiguously.

```
employees(emp_id, name, dept_id, dept_name)

emp_id → name        (each employee ID maps to exactly one name)
emp_id → dept_id      (each employee belongs to exactly one department)
dept_id → dept_name   (each department ID maps to exactly one department name)

Notice: emp_id → dept_name is also technically true, but it's a
TRANSITIVE dependency — it only holds because emp_id → dept_id → dept_name.
This transitive dependency is exactly what 3NF eliminates by splitting
dept_name into its own table keyed by dept_id.
```

**Why this matters:** every normal form is defined in terms of functional dependencies — identifying them is the actual analytical work of normalization. Without correctly identifying functional dependencies in your data, you can't determine whether a schema is in 2NF, 3NF, or BCNF.

**Source:** <https://dl.acm.org/doi/10.1145/362384.362685>

<AnkiTags functional-dependencies normalization schema-design intermediate/>

# What is the difference between a candidate key, primary key, alternate key, and super key?

```
Table: employees(emp_id, ssn, email, name, dept_id)

Candidate keys — any minimal set of columns that uniquely identifies a row:
  {emp_id}  — unique, minimal
  {ssn}     — unique, minimal
  {email}   — unique, minimal
  (there are 3 candidate keys in this table)

Primary key — the ONE candidate key chosen as the table's main identifier:
  {emp_id}  ← chosen as PRIMARY KEY

Alternate keys — candidate keys that were NOT chosen as the primary key:
  {ssn}, {email}   ← still unique, just not the "official" identifier
                      (typically enforced via UNIQUE constraints)

Super key — ANY set of columns that uniquely identifies a row, including
            redundant/non-minimal combinations:
  {emp_id, name}        — a super key (unique, but not minimal — name is extra)
  {emp_id, ssn, email}  — also a super key
  (every candidate key is a super key, but not every super key is minimal
   enough to be a candidate key)
```

**Composite key:** a candidate key (or primary key) made of **more than one column**, where no single column alone is unique — e.g. `{student_id, course_id}` in an enrollment table.

**Source:** <https://dl.acm.org/doi/10.1145/362384.362685>

<AnkiTags keys candidate-key primary-key schema-design junior intermediate/>

# What is denormalization and when is it appropriate?

Denormalization is the deliberate introduction of **redundant data** into a normalized schema — typically by duplicating a value or precomputing an aggregate — to improve read performance at the cost of extra storage and write complexity.

```
Normalized:
  orders(order_id, customer_id, total)
  customers(customer_id, name)
  -- getting an order's customer name requires a JOIN every time

Denormalized:
  orders(order_id, customer_id, customer_name, total)
  -- customer_name is duplicated here; no JOIN needed to read it,
  -- but it must be kept in sync if the customer's name ever changes
```

**When it's worth it:**

- **Read-heavy, write-light workloads** (analytics dashboards, reporting) where avoiding expensive joins on every read outweighs the cost of occasionally updating redundant copies.
- **OLAP systems** generally favor denormalized (often star-schema) designs for query simplicity and speed.
- **OLTP systems** generally favor normalized designs, since they have frequent writes and normalization keeps those writes simple and consistent.

**The risk:** every denormalized copy of data is a place where inconsistency can creep in if updates aren't applied everywhere consistently — denormalization trades data integrity guarantees for read speed, and that trade should be deliberate, not accidental.

**Source:** <https://www.postgresql.org/docs/current/ddl-constraints.html>

<AnkiTags denormalization normalization schema-design OLTP OLAP intermediate/>

# What is the difference between OLTP and OLAP database workloads?

|                   | OLTP (Online Transaction Processing)             | OLAP (Online Analytical Processing)                         |
| ----------------- | ------------------------------------------------ | ----------------------------------------------------------- |
| **Purpose**       | Day-to-day operational transactions              | Historical analysis, reporting, business intelligence       |
| **Query pattern** | Many small reads/writes, single-row lookups      | Few large queries, scanning/aggregating millions of rows    |
| **Schema**        | Normalized (3NF) — minimizes write anomalies     | Denormalized (star/snowflake schema) — optimizes read speed |
| **Examples**      | Order placement, inventory updates, user login   | Monthly sales trends, cohort analysis, dashboards           |
| **Typical tech**  | PostgreSQL, MySQL, DynamoDB                      | Snowflake, BigQuery, Redshift, ClickHouse                   |
| **Optimized for** | Low-latency point queries, high write throughput | High-throughput scans, complex aggregations                 |

```
OLTP query:  SELECT * FROM orders WHERE order_id = 12345;
             -- single row, indexed lookup, sub-millisecond

OLAP query:  SELECT region, SUM(revenue) FROM sales
             WHERE date BETWEEN '2024-01-01' AND '2024-12-31'
             GROUP BY region;
             -- scans potentially billions of rows, takes seconds
```

**Why the distinction matters architecturally:** trying to run heavy OLAP-style analytical queries directly against an OLTP production database can degrade performance for the transactional workload it's meant to serve — this is why companies typically maintain a separate data warehouse, fed by ETL/CDC pipelines, for analytics.

**Source:** <https://www.postgresql.org/docs/current/queries-overview.html>

<AnkiTags OLTP OLAP schema-design data-warehouse intermediate/>

# What is a write-ahead log (WAL) and why is it central to database durability and crash recovery?

A write-ahead log records every change **before** it's applied to the actual data files, as a sequential append-only log. The core rule: a change must be durably written to the WAL _before_ the corresponding data page is modified.

```
Transaction commits:
1. Write "UPDATE accounts SET balance=80 WHERE id=1" to WAL
2. fsync the WAL to disk (durability guaranteed HERE)
3. Acknowledge commit to client
4. (later, possibly asynchronously) apply the change to the actual data page on disk
```

**Why this ordering matters for crash recovery:** if the system crashes after step 2 but before step 4, the data file on disk might still have the old value. On restart, the database **replays the WAL** from the last checkpoint, reapplying any committed changes that didn't make it to the data files — recovering to a consistent, durable state.

**Bonus benefits:** WAL also enables efficient replication (replicas can apply the same log stream) and point-in-time recovery (replaying the log up to a specific timestamp).

**Source:** <https://www.postgresql.org/docs/current/wal-intro.html>

<AnkiTags WAL storage-internals durability crash-recovery intermediate/>

# What is the difference between a B-tree and an LSM-tree as a storage engine structure?

Both are data structures used to organize on-disk storage for fast lookups, but they make very different trade-offs between read and write performance.

**B-tree (used by PostgreSQL, MySQL/InnoDB, most traditional RDBMSs):**

```
- Data is stored in sorted pages forming a balanced tree.
- Writes modify pages IN PLACE — find the right leaf page, update it.
- Reads: O(log n) traversal directly to the data.
- Pro: excellent read performance, no extra read-side merging needed.
- Con: writes require random disk I/O (updating a page wherever it lives),
  and each write touches multiple pages (the leaf, plus any rebalancing).
```

**LSM-tree (used by Cassandra, RocksDB, LevelDB, and as an option in some others):**

```
- Writes are appended sequentially to an in-memory structure (memtable),
  then periodically flushed to disk as immutable, sorted files (SSTables).
- Reads may need to check the memtable AND multiple SSTables, merging results.
- Background COMPACTION periodically merges SSTables to reduce read overhead
  and reclaim space from deleted/overwritten data.
- Pro: writes are fast — always sequential, never random disk seeks.
- Con: reads can be slower (checking multiple SSTables) unless mitigated
  with techniques like Bloom filters.
```

**Rule of thumb:** B-trees favor read-heavy workloads with in-place updates; LSM-trees favor write-heavy workloads (logging, time-series, high-ingest systems) at some cost to read latency.

**Source:** <https://www.postgresql.org/docs/current/btree.html>
<https://github.com/facebook/rocksdb/wiki/Rocksdb-Architecture-Guide>

<AnkiTags storage-internals B-tree LSM-tree performance advanced/>

# What is a Bloom filter and how does it speed up LSM-tree reads?

A Bloom filter is a space-efficient probabilistic data structure that answers the question **"might this key exist in this dataset?"** with either "definitely not" or "maybe" — it never produces a false negative, but it can produce false positives.

```
Without a Bloom filter:
  A read for key "X" must check EVERY SSTable on disk to see if "X" exists,
  even if "X" was never written — expensive, especially with many SSTables.

With a Bloom filter (one per SSTable):
  Before reading an SSTable from disk, check its in-memory Bloom filter:
    "definitely not present" → skip this SSTable entirely (no disk read!)
    "maybe present"          → actually read the SSTable to check

  Result: reads only touch SSTables that might actually contain the key,
  dramatically reducing unnecessary disk I/O.
```

**How it works internally:** a Bloom filter is a bit array plus several hash functions. Inserting a key sets several bits (based on hashing the key multiple ways); checking a key tests whether all those same bits are set. If any required bit is 0, the key is definitely absent. If all are 1, the key is _probably_ present (could be a coincidental overlap from other keys — a false positive).

**Source:** <https://github.com/facebook/rocksdb/wiki/RocksDB-Bloom-Filter>

<AnkiTags storage-internals bloom-filter LSM-tree performance advanced/>

# What is a buffer pool / page cache and why does it matter for database performance?

The buffer pool (also called the page cache) is an in-memory cache of disk pages that the database keeps to avoid hitting slower disk storage for frequently accessed data.

```
Read request for a row:
1. Check buffer pool — is the containing page already in memory?
   YES → return data directly from memory (fast, microseconds)
   NO  → read the page from disk into the buffer pool, then return data
         (slow, milliseconds — orders of magnitude slower than RAM)

Write request:
1. Modify the page IN THE BUFFER POOL (and log the change to WAL)
2. The modified ("dirty") page is NOT immediately written back to disk —
   it's flushed later, either periodically or when evicted
```

**Why this matters operationally:** a database's effective performance depends heavily on how much of the "hot" working set fits in the buffer pool. If frequently accessed data doesn't fit in available memory, the database constantly evicts and re-reads pages from disk — a phenomenon sometimes called "thrashing," which severely degrades throughput.

```sql
-- PostgreSQL: buffer pool size is configured via shared_buffers
-- common guidance: ~25% of system RAM for a dedicated DB server
SHOW shared_buffers;
```

**Source:** <https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-SHARED-BUFFERS>

<AnkiTags storage-internals buffer-pool page-cache performance intermediate/>

# What is a heap file and how does it relate to clustered vs non-clustered storage?

A **heap file** is the default way most relational databases physically store table rows — as an unordered (or insertion-ordered) collection of pages, with no guaranteed relationship between a row's logical key and its physical location on disk.

```
PostgreSQL's default table storage IS a heap:
  Row physical location is identified by a "ctid" (page number, offset)
  that has NO relationship to any column's value — rows are placed
  wherever there's free space, typically in insertion order.

  This means: querying by primary key still requires consulting an
  INDEX (a B-tree) that maps key values → ctid locations, then a
  separate fetch from the heap to get the actual row data.
```

**Contrast with clustered storage** (e.g. MySQL/InnoDB's default table organization): the table data itself **is** physically sorted by the primary key — there's no separate heap; the primary key index and the data are the same physical structure. Looking up by primary key requires no extra "jump" to a separate heap location, since the leaf nodes of the primary key's B-tree directly contain the full row.

```
Heap-organized (PostgreSQL):           Clustered (InnoDB):
Primary key index → ctid → heap page   Primary key index leaf nodes
(2 lookups for a PK query)             directly contain row data
                                        (1 lookup for a PK query)

Secondary indexes in InnoDB store
the PRIMARY KEY value (not a physical
location) as their "pointer," since
rows can move during clustered re-sorts
```

**Source:** <https://www.postgresql.org/docs/current/storage-page-layout.html>
<https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html>

<AnkiTags storage-internals heap-file clustered-index B-tree advanced/>

# What is database replication and what problem does it solve?

Replication is the practice of maintaining **copies of the same data on multiple machines**, primarily to improve fault tolerance (surviving a node failure) and read scalability (spreading read load across multiple replicas).

```
Without replication:           With replication:
Single DB node                 Primary (writes) ──┬──► Replica 1 (reads)
   │                                                ├──► Replica 2 (reads)
   X  node fails →                                  └──► Replica 3 (reads)
   total outage, all data
   potentially lost            If the primary fails, a replica can be
                                promoted to take over — no total outage,
                                and no data loss if replication was
                                synchronous up to the failure point
```

**The fundamental trade-off replication introduces:** keeping replicas in sync costs either **latency** (waiting for replicas to confirm before acknowledging a write) or **consistency** (acknowledging immediately, accepting that replicas may briefly lag behind). This is exactly the trade-off explored by CAP/PACELC.

**Source:** <https://www.postgresql.org/docs/current/high-availability.html>

<AnkiTags replication distributed-systems fundamentals junior intermediate/>

# What is the difference between synchronous and asynchronous replication?

**Synchronous replication:** the primary waits for one or more replicas to **confirm they've received and durably stored** a write before acknowledging the write as successful to the client.

```
Client → Primary: WRITE x=5
Primary → Replica: forward the write
Replica → Primary: ACK (write durably stored)
Primary → Client: WRITE SUCCESSFUL  (only after replica ACK)

Guarantee: if the primary fails immediately after acknowledging, the
replica definitely has the data — zero data loss on failover.
Cost: every write's latency now includes a round-trip to the replica.
```

**Asynchronous replication:** the primary acknowledges the write to the client immediately, and forwards it to replicas in the background, without waiting.

```
Client → Primary: WRITE x=5
Primary → Client: WRITE SUCCESSFUL  (immediately, no replica wait)
Primary → Replica: forward the write (happens shortly after, asynchronously)

Guarantee: low write latency.
Cost: if the primary fails before the replica receives the write, that
write is LOST on failover — the replica becomes the new primary
without ever having seen it.
```

**Common middle ground — "semi-synchronous":** wait for at least **one** replica to confirm (not all), balancing some durability guarantee against not paying the full latency cost of waiting for every replica.

**Source:** <https://www.postgresql.org/docs/current/warm-standby.html#SYNCHRONOUS-REPLICATION>

<AnkiTags replication synchronous-replication asynchronous-replication intermediate advanced/>

# What is the difference between leader-follower, multi-leader, and leaderless replication?

**Leader-follower (primary-replica):** one node (the leader) accepts all writes; followers replicate from it and typically serve only reads.

```
Writes → Leader → replicated to → Follower 1, Follower 2, ...
Simple to reason about. Single point of write contention; failover
requires promoting a follower (briefly unavailable for writes during this).
```

**Multi-leader:** multiple nodes can each accept writes independently, then replicate to each other.

```
Writes → Leader A (datacenter 1)  ─┐
Writes → Leader B (datacenter 2)  ─┴─► replicate to each other
Useful for multi-datacenter setups where writing locally (low latency)
matters. Introduces WRITE CONFLICTS when the same data is modified on
two leaders concurrently — requires conflict resolution (LWW, CRDTs,
or application-level merge logic).
```

**Leaderless (Dynamo-style):** any replica can accept a write; consistency is achieved via quorum reads/writes (W + R > N) rather than a designated leader.

```
Client writes to multiple replicas directly (or via a coordinator);
no single node is "the" authority. Highly available — no leader
election needed on failure. Requires read-repair / anti-entropy
processes to resolve replicas that fall out of sync.
```

|                     | Leader-follower                         | Multi-leader                            | Leaderless                |
| ------------------- | --------------------------------------- | --------------------------------------- | ------------------------- |
| Write conflicts?    | No (single writer)                      | Yes — needs resolution                  | Yes — needs resolution    |
| Failover complexity | Moderate (promote a follower)           | N/A (no single leader to lose)          | N/A (no leader)           |
| Example systems     | PostgreSQL streaming replication, MySQL | Multi-region CouchDB, some MySQL setups | Cassandra, DynamoDB, Riak |

**Source:** <https://www.postgresql.org/docs/current/high-availability.html>
<https://cassandra.apache.org/doc/latest/cassandra/architecture/dynamo.html>

<AnkiTags replication leader-follower multi-leader leaderless distributed-systems intermediate advanced/>

# What is replication lag and what problems does it cause?

Replication lag is the delay between when a write is committed on the primary/leader and when that write becomes visible on a given replica/follower.

```
t=0:  Write "balance=100" committed on primary
t=0.1s: Replica A has received and applied the write (low lag)
t=2.0s: Replica B is still processing a backlog and hasn't applied it yet
        (high lag — 2 seconds behind)

A read routed to Replica B at t=1.0s would return the STALE value,
even though the write already succeeded on the primary 1 second earlier.
```

**Real-world problems this causes:**

- **Read-after-write violations:** a user submits a form, then immediately reloads the page and sees their own change missing, because the read was routed to a lagging replica.
- **Monotonic read violations:** a user refreshes twice and sees a _newer_ value, then an _older_ value, if the second read happens to hit a more-lagged replica than the first.
- **Inconsistent cross-entity reads:** reading two related pieces of data from different replicas with different lag can produce a combination that never actually existed together.

**Common mitigations:** route a user's own reads to the primary for some window after they write (read-your-writes); track replica lag and route reads away from replicas that fall too far behind; use causal/session consistency tokens.

**Source:** <https://www.postgresql.org/docs/current/warm-standby.html>

<AnkiTags replication replication-lag consistency-models intermediate advanced/>

# What is database sharding (horizontal partitioning)?

Sharding splits a single logical dataset across **multiple independent database instances** ("shards"), where each shard holds a disjoint subset of the rows — as opposed to replication, where each node holds a copy of the _same_ data.

```
Unsharded:                         Sharded by user_id range:
Single DB: all users 1-10,000,000  Shard 1: users 1–2,500,000
                                    Shard 2: users 2,500,001–5,000,000
                                    Shard 3: users 5,000,001–7,500,000
                                    Shard 4: users 7,500,001–10,000,000
```

**Why shard:** a single machine has finite CPU, memory, disk I/O, and storage. Past a certain scale, no amount of vertical scaling (bigger machine) keeps up — sharding lets you scale writes and storage horizontally by adding more machines, each handling a fraction of the total data and load.

**The core complexity sharding introduces:** queries that need data from multiple shards (cross-shard joins, global aggregates, multi-shard transactions) become significantly harder and slower than the equivalent query on an unsharded database — this complexity is the main reason engineers avoid sharding until it's actually necessary.

**Source:** <https://www.mongodb.com/docs/manual/sharding/>

<AnkiTags sharding partitioning scaling distributed-systems intermediate/>

# What is the difference between horizontal and vertical partitioning?

**Horizontal partitioning (sharding):** splits a table by **rows** — each partition has the same columns but a different subset of rows.

```
users table split by user_id range:
Shard 1: rows where user_id < 1,000,000   (all columns)
Shard 2: rows where user_id >= 1,000,000  (all columns)
```

**Vertical partitioning:** splits a table by **columns** — each partition has the same rows but a different subset of columns, often separating frequently-accessed columns from rarely-accessed ones.

```
users table split by column groups:
Partition A: user_id, name, email          (accessed on every request)
Partition B: user_id, bio, profile_photo   (accessed only on profile page)
```

**Why vertical partitioning helps:** if a table has some columns that are large (e.g. a long bio, a BLOB) and rarely read, but other columns are small and read constantly, separating them means common queries don't have to read past the large, rarely-needed columns — improving cache efficiency and I/O for the hot path.

**They're often combined:** a large system might vertically partition a table into a "hot" and "cold" set of columns, then horizontally shard the hot set across many machines for scale.

**Source:** <https://www.postgresql.org/docs/current/ddl-partitioning.html>

<AnkiTags partitioning sharding schema-design scaling intermediate/>

# What are the main sharding strategies (range, hash, and geographic), and what's the trade-off between them?

**Range-based sharding:** assign contiguous ranges of the shard key to each shard.

```
Shard 1: user_id 1 – 1,000,000
Shard 2: user_id 1,000,001 – 2,000,000

Pro: range queries (e.g. "all users created last week" if sharded by
     signup date) stay within one or few shards.
Con: prone to HOTSPOTS — if shard keys are sequential (auto-increment IDs,
     timestamps), all new writes land on the single newest/last shard.
```

**Hash-based sharding:** apply a hash function to the shard key, then assign based on the hash value (often via consistent hashing).

```
shard = hash(user_id) % num_shards

Pro: spreads writes evenly across shards — no hotspots from sequential
     keys.
Con: range queries become impossible to satisfy from one shard — a query
     for "all users created last week" would need to fan out and query
     every shard, then merge results.
```

**Geographic/directory-based sharding:** assign shards based on a real-world attribute, often via an explicit lookup ("directory") rather than a pure function.

```
Shard EU: customers in European Union (data residency requirement)
Shard US: customers in the United States

Pro: satisfies data residency/regulatory requirements; can co-locate
     data near the users who access it most, reducing latency.
Con: uneven shard sizes if user distribution across regions is skewed;
     the directory/lookup service becomes a critical dependency and
     potential single point of failure.
```

**The core trade-off across all strategies:** write distribution (avoiding hotspots) vs. read locality (keeping related data queryable from a single shard) — optimizing one tends to hurt the other.

**Source:** <https://www.mongodb.com/docs/manual/core/sharding-shard-key/>

<AnkiTags sharding partitioning shard-key scaling intermediate advanced/>

# How do you choose a good shard key?

A shard key (also called a partition key) determines which shard a given row lives on. Choosing it well is one of the highest-leverage decisions in a sharded system's design, because it's expensive to change later.

**Properties of a good shard key:**

1. **High cardinality:** many distinct values, so data can actually spread across many shards (a boolean column would be a terrible shard key — only 2 possible shards).

2. **Even distribution:** avoids hotspots — if 90% of traffic is for one value of the shard key (e.g. one mega-customer in a multi-tenant system), that shard becomes a bottleneck no matter how many other shards exist.

3. **Aligns with your query patterns:** if most queries filter by `customer_id`, sharding by `customer_id` keeps those queries within a single shard. Sharding by an unrelated column would force most queries to fan out across all shards.

4. **Avoids monotonically increasing keys for hash-free schemes:** sequential keys (auto-increment IDs, timestamps) under range-based sharding concentrate all new writes on the single "newest" shard.

```
Bad shard key:  status (low cardinality: 'active'/'inactive' — only 2 buckets)
Bad shard key:  created_at with range sharding (all new writes hit one shard)
Good shard key: hash(customer_id) — high cardinality, even spread,
                aligns with "fetch all data for customer X" queries
```

**The hardest part in practice:** a shard key that's good for today's query patterns and traffic distribution may become wrong as the application evolves — re-sharding an already-large dataset is a major operational undertaking.

**Source:** <https://www.mongodb.com/docs/manual/core/sharding-shard-key/>

<AnkiTags sharding shard-key scaling schema-design advanced/>

# What is resharding/rebalancing, and why is it operationally difficult?

Resharding is the process of moving data between shards — either to add capacity (more shards) or to fix an uneven distribution (hotspots).

```
Before rebalancing:           After adding a 5th shard:
Shard 1: 25% of data          Shard 1: 20% of data
Shard 2: 25% of data          Shard 2: 20% of data
Shard 3: 25% of data    →     Shard 3: 20% of data
Shard 4: 25% of data          Shard 4: 20% of data
                               Shard 5: 20% of data (new)

Some fraction of existing data must physically MOVE from the old
shards to the new shard to reach the new even distribution.
```

**Why naive `hash(key) % N` sharding makes this painful:** when `N` (the number of shards) changes, nearly **every** key's `hash(key) % N` result changes too — meaning almost all data would need to move, even though only 1/Nth of it strictly needs to, to populate the new shard.

```
N=4: hash(key) % 4    N=5: hash(key) % 5
key A → shard 2        key A → shard 4   (moved, even though it didn't have to)
key B → shard 0        key B → shard 3   (moved)
...nearly all keys land on a different shard after N changes
```

**The standard fix — consistent hashing:** maps both shards and keys onto a hash ring, so adding or removing a shard only requires moving the keys between the new shard and its immediate neighbor on the ring — roughly `1/N` of the data, not nearly all of it.

**Operational difficulty beyond the data movement itself:** rebalancing must typically happen **without downtime**, meaning the system needs to serve reads/writes correctly while some keys are mid-migration — usually handled via dual-write/dual-read periods or a migration coordinator that tracks which keys have moved.

**Source:** <https://www.mongodb.com/docs/manual/core/sharding-balancer-administration/>

<AnkiTags sharding rebalancing consistent-hashing scaling advanced/>

# What is a distributed transaction, and why are they hard across shards?

A distributed transaction spans multiple independent nodes (e.g. multiple shards), and must still provide atomicity — all participating nodes commit, or all roll back, even though no single node can unilaterally guarantee that outcome on its own.

```
Example: transferring money between two users on DIFFERENT shards

Shard A: UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
Shard B: UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;

Within a single shard, this would be one easy local transaction.
Across two shards, what happens if Shard A commits successfully but
Shard B then crashes before committing? Money has vanished.
```

**The classic solution — Two-Phase Commit (2PC):**

```
Phase 1 (prepare): a coordinator asks every participant "can you commit?"
                    each participant locks resources and replies yes/no
Phase 2 (commit):   if ALL replied yes, coordinator tells everyone to commit;
                    if ANY replied no (or timed out), coordinator tells
                    everyone to abort
```

**Why 2PC is avoided when possible:** it's blocking — if the coordinator crashes between phases, participants can be left holding locks indefinitely, waiting for a coordinator that may never come back. It also adds significant latency (multiple round trips) to every cross-shard transaction.

**Common alternative in practice:** avoid distributed transactions by designing the shard key so that data needing transactional consistency together always lives on the **same shard** (e.g. shard by `account_id`, ensuring a transfer between two of the same user's accounts stays local).

**Source:** <https://www.postgresql.org/docs/current/sql-prepare-transaction.html>

<AnkiTags distributed-transactions two-phase-commit sharding advanced/>

# What is the difference between Paxos and Raft, and what problem do they both solve?

Both are **consensus algorithms** — protocols that let a cluster of nodes agree on a single value (or a sequence of values, like a replicated log) even when some nodes fail or messages are delayed, while guaranteeing all surviving nodes agree on the same outcome.

**The core problem they solve:** in leader-follower replication, if the leader fails, how do the remaining nodes safely agree on who the new leader is, and on exactly which writes were and weren't durably committed before the failure — without risking two nodes both believing they're the leader (split-brain) or losing acknowledged writes?

**Paxos** (Lamport, 1998): the foundational consensus algorithm, mathematically proven correct, but notoriously difficult to understand and implement correctly — its original presentation is famously dense, and most real systems use heavily adapted variants (Multi-Paxos) rather than the base algorithm directly.

**Raft** (Ongaro & Ousterhout, 2014): designed explicitly as a more **understandable** alternative to Paxos, while providing equivalent guarantees. It decomposes consensus into three clearer sub-problems:

```
1. Leader election — nodes vote; a candidate becomes leader with a
   majority of votes for a given "term" (logical clock for elections)
2. Log replication — the leader appends entries to its log and
   replicates them to followers, only considering an entry committed
   once a MAJORITY of nodes have durably stored it
3. Safety — guarantees that committed entries are never lost or
   reordered, even across leader changes
```

**Why this matters for backend engineers:** etcd, Consul, CockroachDB, and many other systems used for distributed coordination and leader election are built on Raft specifically because of its relative implementability and well-documented correctness properties.

**Source:** <https://raft.github.io/raft.pdf>

<AnkiTags consensus Raft Paxos distributed-systems advanced/>

# What is a "split-brain" scenario in a database cluster, and how is it prevented?

Split-brain occurs when a network partition causes a cluster to incorrectly end up with **two nodes simultaneously believing they are the leader/primary**, each independently accepting writes — leading to diverging, conflicting data that's extremely difficult to reconcile afterward.

```
Before partition:           During partition:
   Leader: Node A            Node A (isolated, can't reach others)
   Followers: B, C              "I haven't heard a heartbeat... but I'll
                                  keep acting as leader anyway"
                              Nodes B, C (can talk to each other)
                                  "We can't reach A — let's elect Node B
                                   as the new leader"

Result: BOTH Node A and Node B now believe they are the leader and
accept writes independently. Data written to A is invisible to B's
followers and vice versa — a genuine split-brain.
```

**How quorum-based consensus (Raft/Paxos) prevents this:** a node can only become or remain leader if it can communicate with a **majority** of the cluster. In the scenario above, the isolated Node A can only reach itself (1 node) — not a majority of a 3-node cluster — so it correctly **steps down** rather than continuing to act as leader, even though it can't be sure why it lost contact.

```
3-node cluster: majority = 2 nodes
Node A isolated alone:        1/3 — NOT a majority → A steps down, refuses writes
Nodes B+C together:           2/3 — IS a majority → may elect a new leader safely
```

**Source:** <https://raft.github.io/raft.pdf>

<AnkiTags split-brain consensus availability distributed-systems advanced/>

# What are the main NoSQL database categories, and what is each optimized for?

| Category                        | Data model                                                               | Optimized for                                                                  | Examples                   |
| ------------------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------ | -------------------------- |
| **Key-value**                   | Opaque value retrieved by a single key                                   | Simple, extremely fast lookups; caching                                        | Redis, DynamoDB, Riak      |
| **Document**                    | Semi-structured documents (JSON/BSON), can be queried by internal fields | Flexible schemas, nested/hierarchical data                                     | MongoDB, Couchbase         |
| **Column-family (wide-column)** | Rows with dynamic, sparse sets of columns, grouped into column families  | Very high write throughput, time-series, wide sparse data                      | Cassandra, HBase, Bigtable |
| **Graph**                       | Nodes and edges with properties, optimized for traversal                 | Highly connected data — social graphs, recommendation engines, fraud detection | Neo4j, Amazon Neptune      |

```
Key-value:    GET user:1234 → {opaque blob}

Document:     db.users.find({ "address.city": "Berlin" })
              { _id: 1234, name: "Alice", address: { city: "Berlin" } }

Column-family: row key "user1234" has a flexible set of columns that can
               differ from other rows entirely — no fixed schema across rows

Graph:        MATCH (a:Person)-[:FRIENDS_WITH]->(b:Person)
              WHERE a.name = 'Alice' RETURN b
              -- efficient traversal of relationships, something relational
                 JOINs handle poorly at deep traversal depths
```

**Why "NoSQL" is a fairly unhelpful umbrella term:** these four categories solve genuinely different problems and make different trade-offs — there's no single unifying property beyond "not the traditional relational/SQL model."

**Source:** <https://www.mongodb.com/resources/basics/databases/nosql-explained>
<https://cassandra.apache.org/doc/latest/cassandra/data_modeling/index.html>

<AnkiTags NoSQL key-value document column-family graph-database intermediate/>

# When would you choose a relational (SQL) database over a NoSQL database, and vice versa?

**Favor a relational database when:**

- Data has clear, stable relationships that benefit from joins (orders ↔ customers ↔ products).
- You need strong transactional guarantees (ACID) across multiple related entities — e.g. financial systems.
- Your query patterns are varied and ad hoc — relational databases let you query by any column/combination without redesigning the schema.
- Data integrity constraints (foreign keys, uniqueness, check constraints) should be enforced by the database itself, not solely by application code.

**Favor a NoSQL database when:**

- You need to scale writes horizontally beyond what a single (even well-tuned) relational primary can handle, and the data model tolerates it (e.g. sharding-friendly access patterns).
- The schema is genuinely variable or evolves frequently per-record (different documents have different fields).
- Your access pattern is overwhelmingly simple key-based lookups, and you want to trade query flexibility for raw lookup speed (key-value stores).
- The data is fundamentally graph-shaped, and deep relationship traversal is core to your queries.
- You can tolerate eventual consistency in exchange for very high availability (e.g. shopping cart, activity feed, view counters).

```
Good fit for relational: banking ledger, e-commerce order management,
                          inventory with referential integrity needs

Good fit for NoSQL:      session storage (key-value), product catalog
                          with wildly varying attributes per category
                          (document), IoT sensor time-series at massive
                          write volume (column-family), social network
                          friend recommendations (graph)
```

**The honest middle ground:** most production systems at scale use **both** — a relational primary store for the core transactional data, plus one or more NoSQL stores for specific access patterns (caching, search, time-series, graph traversal) that the relational store isn't well-suited for.

**Source:** <https://www.mongodb.com/resources/basics/databases/nosql-explained>

<AnkiTags SQL NoSQL schema-design decision-making intermediate/>

# What is point-in-time recovery (PITR) and how does it work?

Point-in-time recovery lets you restore a database to its exact state at **any specific moment in the past** (not just the time of the last full backup), by combining a base backup with a replay of the write-ahead log up to the desired point.

```
Timeline:
t=0    Full base backup taken
t=1h   ... normal operations, WAL continuously archived ...
t=2h   ... normal operations, WAL continuously archived ...
t=2h15m  Accidental "DROP TABLE customers" executed!
t=2h30m  Disaster discovered

Recovery process:
1. Restore the base backup from t=0
2. Replay archived WAL segments from t=0 up to t=2h14m59s
   (just BEFORE the accidental DROP TABLE)
3. Database is now in the exact state it was in, one second before
   the mistake — the DROP TABLE is never replayed
```

**Why this is more powerful than restoring from a nightly backup alone:** a nightly backup only lets you go back to fixed snapshot times (e.g. "as of last midnight"), losing all changes since then. PITR lets you recover to **any second**, as long as the WAL archive covers that period — minimizing data loss to seconds or minutes rather than up to a full day.

**Source:** <https://www.postgresql.org/docs/current/continuous-archiving.html>

<AnkiTags backup-recovery PITR WAL disaster-recovery intermediate advanced/>

# What is the difference between a full backup, an incremental backup, and a snapshot?

**Full backup:** a complete copy of the entire dataset at a point in time.

```
Pro: simplest to restore from — just one file/set to recover.
Con: slow to create and store for large datasets; wasteful if most
     data hasn't changed since the last full backup.
```

**Incremental backup:** captures only the data that **changed** since the last backup (full or incremental).

```
Sunday:    Full backup (100 GB)
Monday:    Incremental (only the ~2 GB that changed since Sunday)
Tuesday:   Incremental (only the ~2 GB that changed since Monday)

Pro: much faster and smaller than repeated full backups.
Con: restoring requires replaying the full backup PLUS every
     incremental since then, in order — more complex, and a single
     corrupted incremental breaks the whole chain after it.
```

**Snapshot:** a near-instantaneous, often filesystem- or storage-layer point-in-time view of the data, frequently implemented via copy-on-write — the snapshot initially shares all data blocks with the live system, only diverging as blocks are modified afterward.

```
Pro: very fast to create (doesn't copy all data immediately), commonly
     used for storage volumes (e.g. cloud block storage snapshots).
Con: typically tied to the specific storage system that created it;
     less portable than a full logical backup.
```

**Source:** <https://www.postgresql.org/docs/current/backup.html>

<AnkiTags backup-recovery snapshot incremental-backup disaster-recovery intermediate/>

# What is read replica scaling and what are its limitations?

Read replica scaling adds additional replicas of a primary database specifically to **handle more read traffic** by spreading queries across multiple machines, while all writes still go to the single primary.

```
Application:
  Writes  → Primary
  Reads   → Load-balanced across Replica 1, Replica 2, Replica 3
```

**What it solves well:** read-heavy workloads (the vast majority of typical web applications) can scale read capacity nearly linearly by adding more replicas, without touching the application's write path at all.

**What it does NOT solve:**

- **Write scaling:** every replica still ultimately depends on the same single primary for all writes — adding more read replicas does nothing to help a write-bottlenecked system.
- **Storage scaling:** every replica stores a full copy of the entire dataset — this doesn't help when the problem is the dataset no longer fitting comfortably on one machine.
- **Strong consistency for replica reads:** reads from replicas are subject to replication lag (see the dedicated replication lag card) unless specifically routed to the primary or a synchronous replica.

**When you've outgrown read replicas alone:** if writes (not reads) are the bottleneck, or if total data volume exceeds what fits well on a single primary, sharding becomes necessary — read replicas and sharding solve different scaling problems and are often used together (each shard can have its own set of read replicas).

**Source:** <https://www.postgresql.org/docs/current/high-availability.html>

<AnkiTags replication read-replicas scaling intermediate/>

# What is CQRS (Command Query Responsibility Segregation) and how does it relate to database scaling?

CQRS is an architectural pattern that separates the **write path** (commands — operations that change state) from the **read path** (queries — operations that retrieve state), often using entirely different data models or even different physical data stores for each.

```
Traditional (single model):
  Writes and reads both go through the same schema/table structure,
  even though they often have very different access patterns
  (writes: normalized, transactional; reads: often need denormalized,
  pre-aggregated views).

CQRS:
  Command side:  writes go to a normalized, transactional store
                 (e.g. PostgreSQL) — optimized for write correctness

  Query side:    a separate, often denormalized read model is built
                 and kept up to date (e.g. via events or CDC), optimized
                 specifically for the read patterns the application
                 actually needs (e.g. a search index, a materialized
                 view, a cache)
```

**Why this helps at scale:** the write model and read model can each be optimized independently and even scaled independently — a write-heavy command store doesn't need to also serve complex denormalized read queries, and a read-optimized store doesn't need to enforce the full weight of transactional write constraints.

**The cost:** the read model is no longer perfectly in sync with the write model in real time — there's some propagation delay (similar to replication lag), and the system now has two models to keep consistent and two code paths to maintain instead of one.

**Source:** <https://martinfowler.com/bliki/CQRS.html>

<AnkiTags CQRS scaling architecture-patterns advanced/>

# Why can't you just keep raising max_connections to handle more traffic?

Every database connection consumes real resources on the server — memory for connection state, buffers, and (in process-per-connection architectures like PostgreSQL) a dedicated OS process. Raising `max_connections` without bound eventually degrades performance rather than improving it.

```
PostgreSQL: each connection is a separate OS process
  - Each process needs its own memory (work_mem, temp buffers, etc.)
  - The OS scheduler has to context-switch between hundreds/thousands
    of processes, and CPU cache locality suffers as more processes compete
  - Past a certain number of concurrent connections, throughput actually
    DROPS as connections increase — more time is spent context-switching
    and managing contention than doing real work

Rough rule of thumb cited by PostgreSQL maintainers:
  optimal_connections ≈ (CPU cores × 2) + effective_spindle_count
  far lower than the thousands of connections an application server
  fleet might naively want to open
```

**Why this surprises people coming from the application side:** an application server can often handle thousands of concurrent lightweight requests/coroutines cheaply, but the database underneath has a much lower ceiling for concurrent _active_ connections before contention costs outweigh added parallelism.

**Source:** <https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-MAX-CONNECTIONS>

<AnkiTags connection-pooling max-connections scaling performance intermediate advanced/>

# What is a connection pooler like PgBouncer, and why is it placed in front of the database?

A connection pooler sits between application servers and the database, maintaining a small, fixed pool of actual database connections and **multiplexing** many more application-side connections onto them.

```
Without a pooler:
  1000 application worker threads → 1000 direct DB connections
  (the database now pays the per-connection memory/process overhead
   for all 1000, even if only a few are doing real work at any instant)

With a pooler (e.g. PgBouncer):
  1000 application worker threads → PgBouncer → 50 actual DB connections
  PgBouncer holds a small pool of real connections and hands them out
  to application connections only while a query is actively running,
  then returns them to the pool immediately after
```

**Pooling modes (PgBouncer-specific, but the concept generalizes):**

- **Session pooling:** a DB connection is assigned to a client for the lifetime of its session — safest, but doesn't reduce connection count much.
- **Transaction pooling:** a DB connection is assigned only for the duration of a single transaction, then returned to the pool — much higher multiplexing, but breaks session-level features like advisory locks or `SET` statements that aren't transaction-scoped.
- **Statement pooling:** a DB connection is held only for a single statement — highest multiplexing, most restrictive (no multi-statement transactions at all).

**Why this matters operationally:** as an application scales out to many instances/workers, a pooler is usually what keeps the actual database connection count manageable, rather than relying on every application instance to self-limit its own connection count perfectly.

**Source:** <https://www.pgbouncer.org/features.html>

<AnkiTags connection-pooling PgBouncer scaling infrastructure intermediate advanced/>

# What does a query planner/optimizer do, and what is cost-based optimization?

When a database receives a SQL query, it doesn't execute it literally as written — it first generates multiple possible **execution plans** (different orders of operations, different algorithms for joins/scans) and picks the one it estimates will be **cheapest** to run.

```
Query: SELECT * FROM orders o JOIN customers c ON o.customer_id = c.id
       WHERE c.country = 'DE';

Possible plans the optimizer considers:
  Plan A: scan all of customers, filter country='DE', then for each
          matching customer, look up their orders via an index
  Plan B: scan all of orders, then for each order, look up the customer
          and check if country='DE'
  Plan C: scan both tables fully and hash-join them, filtering after

The optimizer ESTIMATES the cost of each plan (using table size statistics,
index availability, and selectivity estimates), then picks the
lowest-estimated-cost plan — this is cost-based optimization.
```

**What feeds the cost estimate:** row count statistics per table, the selectivity of `WHERE` predicates (what fraction of rows match), the presence and selectivity of relevant indexes, and the estimated cost of disk I/O vs. in-memory operations. These statistics are gathered by commands like `ANALYZE` and can go stale, leading the optimizer to choose a bad plan — a common real-world cause of "this query was fast yesterday and slow today."

**Source:** <https://www.postgresql.org/docs/current/planner-stats.html>
<https://www.postgresql.org/docs/current/runtime-config-query.html>

<AnkiTags query-planner cost-based-optimization storage-internals intermediate advanced/>

# What are nested loop join, hash join, and merge join, and when does the optimizer pick each?

These are the three classic algorithms a query planner chooses between to execute a `JOIN`, each with different performance characteristics depending on table sizes, indexes, and sort order.

**Nested loop join:** for each row in the outer table, scan (or index-lookup) the inner table for matches.

```
for each row in outer_table:
    for each row in inner_table where join_condition matches:
        emit combined row

Best when: the outer table is small AND there's a good index on the
join column of the inner table — each "inner lookup" becomes a cheap
indexed lookup rather than a full scan.
Cost grows badly (O(n × m)) if there's no usable index and both
tables are large — this becomes the worst-case option at scale.
```

**Hash join:** build an in-memory hash table from the smaller input (keyed on the join column), then scan the larger input, probing the hash table for matches.

```
build phase: hash_table = { row.join_key: row for row in smaller_table }
probe phase: for each row in larger_table:
                 if row.join_key in hash_table: emit combined row

Best when: no useful index exists, but the smaller table fits comfortably
in memory — avoids the O(n × m) cost of nested loop by paying a one-time
O(n + m) hashing cost instead.
Cost: requires building and holding a hash table in memory; degrades if
the build-side table is too large to fit (spills to disk).
```

**Merge join:** requires both inputs to already be **sorted** on the join key; then merges them in a single linear pass, similar to merging two sorted lists.

```
Both inputs sorted by join_key:
  pointer_a = start of table_a, pointer_b = start of table_b
  advance both pointers together, matching equal keys, in one pass

Best when: both inputs are already sorted (e.g. via an index scan that
naturally returns sorted order) — extremely efficient, O(n + m), no
hash table needed.
Cost: if the inputs AREN'T already sorted, the optimizer must add an
explicit sort step first, which can be expensive for large tables.
```

**Source:** <https://www.postgresql.org/docs/current/planner-optimizer.html>
<https://www.postgresql.org/docs/current/explicit-locking.html>

<AnkiTags query-planner joins nested-loop hash-join merge-join intermediate advanced/>

# How do you safely run a database schema migration on a live production system without downtime?

A schema migration changes a database's structure (adding/removing columns, changing types, adding constraints) while the application is actively reading and writing — done carelessly, it can lock tables, break currently-running application code, or cause data loss.

**The core risk:** application code and database schema are deployed independently in most systems. During a rolling deploy, **old application code and new application code run simultaneously against the same database** for some window of time — a migration that isn't backward-compatible with the old code will break it mid-deploy.

**The standard safe pattern — expand/contract (parallel change):**

```
1. EXPAND: add the new structure WITHOUT removing the old one.
   e.g. adding a new column as NULLABLE (or with a default),
   never as NOT NULL in the same step — old code that doesn't know
   about the new column must keep working unmodified.

2. MIGRATE/BACKFILL: populate the new structure from existing data,
   typically in small batches to avoid long table locks.
   UPDATE users SET full_name = first_name || ' ' || last_name
   WHERE full_name IS NULL LIMIT 1000;   -- repeat until done

3. DUAL-WRITE (if needed): deploy application code that writes to
   BOTH old and new structures, so both stay in sync during transition.

4. CONTRACT: once ALL application instances are confirmed running
   the new code (old code path is fully retired), remove the old
   structure — drop the old column, add the NOT NULL constraint, etc.
```

**Operations that are dangerous to run directly on a large, live table:**

```sql
-- DANGEROUS: adding a column with a non-null default can rewrite
-- every row on older PostgreSQL versions (fixed in PG 11+, but still
-- worth checking your specific engine/version)
ALTER TABLE orders ADD COLUMN status TEXT NOT NULL DEFAULT 'pending';

-- DANGEROUS: building an index locks writes on older engines/configs
CREATE INDEX idx_orders_status ON orders(status);

-- SAFER: build the index without blocking writes (PostgreSQL)
CREATE INDEX CONCURRENTLY idx_orders_status ON orders(status);
```

**Why rollback plans matter:** every migration should have a tested rollback path — for the expand/contract pattern, rolling back is usually as simple as not yet running the "contract" step, since the old structure was never removed while both code paths might still need it.

**Source:** <https://www.postgresql.org/docs/current/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY>
<https://www.postgresql.org/docs/current/sql-altertable.html>

<AnkiTags schema-migration zero-downtime backward-compatibility expand-contract intermediate advanced/>
