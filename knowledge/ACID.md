---
tags:
  - database
  - transactions
  - concurrency
summary: atomicity, consistency, isolation, durability, concurrency anomalies
use_cases:
  - oltp
  - distributed_systems
---

# ACID

## Overview

ACID defines the four fundamental properties that guarantee reliable transaction processing in database systems.

ACID = Atomicity + Consistency + Isolation + Durability

## Transaction Basics

A transaction is a sequence of operations treated as a single logical unit.

Typical lifecycle:
- BEGIN
- READ / WRITE
- COMMIT or ROLLBACK

## Atomicity

**Definition**: All operations in a transaction succeed or none take effect.

- Ensures no partial updates
- Failure -> rollback to previous state

**Mechanism**:
- Undo log (rollback log: records the previous values before modification, used for transaction rollback and <span class="abbr" data-title="ulti-Version Concurrency Control">MVCC</span> support)

## Consistency

**Definition**: A transaction transforms the database from one valid state to another.

- All constraints must hold:
	- Primary key
	- Foreign key
	- Unique
	- Business rules

## Isolation

**Definition**: Concurrent transactions do not interfere with each other.

**Concurrency Anomalies**
1. ==Dirty Read==: Reading data that has been modified by another transaction but not yet committed.
	- Cause: Another transaction modifies data but has not committed yet
	- The data may later be rolled back
2. ==Non-repeatable Read==: Reading the same row multiple times within a transaction yields different results.
	- Cause: Another transaction updates and commits the data between reads
	- Data is not stable within a transaction
3. ==Phantom Read==: Re-running a query returns a different set of rows.
	- Cause: Another transaction inserts or deletes rows that match the query condition
	- Result set size changes

**Isolation Levels**
1. Read Uncommitted  
	- Can see any data, even uncommitted
	- -> dirty read, non-repeatable read, phantom read
2. Read Committed
	- Only sees committed data
	- -> non-repeatable read, phantom read
3. Repeatable Read
	- Same row stays consistent within a transaction
	- -> phantom read
4. Serializable
	- Transactions behave as if executed one by one
	- -> no dirty read, no non-repeatable read, no phantom read

**Mechanisms**:
1. Locking (<span class="abbr" data-title="Two-Phase Locking">2PL</span>)
	- Growing phase: acquire locks only
	- Shrinking phase: release locks only
	- Trade-off: strong consistency but possible deadlocks and lower concurrency
2. MVCC
	- Maintains multiple versions of data
	- Reads access a consistent snapshot (old versions)
	- Writes create new versions instead of overwriting
	- Trade-off: eliminates read-write conflicts, write-write still needs locking

## Durability

**Definition**: Once a transaction is committed, its changes are permanent.

**Mechanisms**:
- Write-Ahead Logging (write changes to a log before writing to data pages, enabling crash recovery)
- Disk persistence (store data on durable storage so it survives system failures)
- Checkpoint (periodically flush in-memory data to disk to limit recovery work)

## ACID Summary

| Property    | Goal                     | Mechanism         |
|------------|--------------------------|-------------------|
| Atomicity  | All or nothing           | Undo log          |
| Consistency| Valid state transition   | Constraints       |
| Isolation  | No interference          | Locks / MVCC      |
| Durability | Permanent storage        | WAL / Disk        |
