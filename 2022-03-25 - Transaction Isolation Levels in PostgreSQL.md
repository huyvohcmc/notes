# 25.03.2022 - Transaction Isolation Levels in PostgreSQL

### Database transactions

A transaction is a unit of work that either being executed in "full" or not at all. It is a way of representing a state change in your database. Transactions have four properties (known as ACID):
- *Atomicity* (the whole unit of work is committed, or none is committed)
- *Consistency* (state change can only happen if it is valid, so before and after the change, the data stay valid)
- *Isolation* (concurrent transactions don’t interfere with or affect one another, from the outsider point-of-view, it looks like concurrent transactions happen one by one, while in reality they are actually happening simultaneously)
- *Durability* (if a transaction executed successfully, your database data will change state no matter what)

### TBC
