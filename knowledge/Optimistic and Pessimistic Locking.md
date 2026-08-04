---
tags:
  - database
  - transactions
  - concurrency
summary: optimistic locking vs. pessimistic locking
use_cases:
  - oltp
  - database_transactions
  - system_design
---

# Optimistic and Pessimistic Locking

## Pessimistic Locking

Pessimistic locking assumes that concurrent conflicts are likely.

It locks the data before processing it. Other transactions must wait until the lock is released.

```text
Transaction A: lock → read → update → commit → unlock
Transaction B:        wait...                → continue
```

For example, an order is locked while its status is being updated:

```sql
BEGIN;

SELECT status
FROM orders
WHERE order_id = 100
FOR UPDATE;

UPDATE orders
SET status = 'shipped'
WHERE order_id = 100;

COMMIT;
```

`FOR UPDATE` locks the selected row until the transaction ends.

Pessimistic locking is suitable when:

- Conflicts happen frequently.
- An operation must complete without interference.
- Transactions are short.

Its disadvantages include:

- Other transactions may need to wait.
- Long transactions reduce concurrency.
- Multiple locks may cause deadlocks.

## Optimistic Locking

Optimistic locking assumes that concurrent conflicts are uncommon.

It does not keep the data locked while it is being processed. Before updating the data, it checks whether another transaction has changed it.

A version field is commonly used:

```sql
SELECT status, version
FROM orders
WHERE order_id = 100;
```

Suppose the result is:

```text
status  = pending
version = 3
```

The update includes the version that was originally read:

```sql
UPDATE orders
SET status = 'shipped',
    version = version + 1
WHERE order_id = 100
  AND version = 3;
```

The application then checks the number of affected rows:

```text
1 row affected  → update succeeded
0 rows affected → another transaction changed the order
```

If a conflict occurs, the application can reload the data, retry the operation, or report the conflict to the user.

Optimistic locking is suitable when:

- Conflicts are uncommon.
- Data may remain open for a long time before being saved.
- High concurrency is important.

Its disadvantages include:

- Conflicts may waste completed work.
- The application must explicitly handle conflicts.
- A version field or equivalent comparison is required.

> Optimistic locking does not mean that the database uses no internal locks. The database still uses locks while executing and committing an update. The difference is that the application does not hold a long-lived lock from the initial read to the final update.
