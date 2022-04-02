# 02.04.2022 - Transaction Isolation Levels in PostgreSQL

### Transaction Isolation

There are four levels of transaction isolation in PostgreSQL:
1. Read uncommitted
2. Read committed
3. Repeatable read
4. Serializable

A transaction isolation level is defined by the following phenomena:
- Dirty read
- Non-repeatable read
- Phantom read
- Serialization anomaly

PostgreSQL doesn’t implement **read uncommitted**, which allows dirty reads, and instead defaults to read committed.

#### Dirty read

With dirty read, a transaction can reads data by other concurrent, uncommitted transactions.

#### Non-repeatable read

Non-repeatable read occurs when a transaction reads the same row/s twice and get different result each time. Example: there are 2 transactions **T1** and **T2**. **T1** reads data from row 1, get result **R1**, **T2** concurrently updates row 1, change the data from **R1** to **R2**, and commits. Now **T1** reads row 1 again and got **R2** instead of **R1**.

####  Phantom read

Similar to non-repeatable read, phantom read happens when a transaction executes two same queries, but the rows retrieved by the two are different.

#### Serialization anomaly

Serialization anomaly means that transactions **T1** and **T2** interfere and result in an anomaly state (a state that cannot be achieved if you run **T1** before **T2** or **T2** before **T1**).

### Isolation Levels Table

| Isolation Level  | Dirty Read             | Nonrepeatable Read | Phantom Read           | Serialization Anomaly |
| ---------------- | ---------------------- | ------------------ | ---------------------- | --------------------- |
| Read uncommitted | Allowed, but not in PG | Possible           | Possible               | Possible              |
| Read committed   | Not possible           | Possible           | Possible               | Possible              |
| Repeatable read  | Not possible           | Not possible       | Allowed, but not in PG | Possible              |
| Serializable     | Not possible           | Not possible       | Not possible           | Not possible          |

### References

- https://www.postgresql.org/docs/current/transaction-iso.html
- https://sqlperformance.com/2014/04/t-sql-queries/the-serializable-isolation-level
