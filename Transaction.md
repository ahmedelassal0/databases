# Atomicity
- All the queries in a transaction must success.
- If one **single query failed**, the transaction must **roll back**.
- If the **database went down**, all the successful queries in a transaction must **roll back**.

Some databases will write to disk before the transaction success, it would assume the transaction will success, some others will write to memory so the rollback and write will be so fast but the commits will be so slow.

If a **failure** happened the database have to clean the **half garbage** done because of not completed transaction, some databases wait until the half transaction roll back, some others let you do operations on other tables until the database becomes in consistent state, Long transactions is a bad idea because of this.

# Isolation
- Can my inflight transaction see changes made by other transactions? **it depends** If you want to see them you well, other wise you won't
### Read phenomena
- **Dirty read:** you wrote something -as an inflight transaction- that other transaction read but didn't commit yet.
- **Non-repeatable reads:** You read a value more than one in the same transaction and **the second read defers from the first read**.
- **phantom-read:** reads that you can't read because it doesn't exist yet, for example if another transaction inserted a record and committed and that record you didn't read in the first query of your transaction but you read in the second query
- **Lost updates**: If you made updates inside a transaction but another transaction updated it again and then you read the values that is updated by other transaction.
### Isolation levels
- **Read uncommitted:** No isolation, any change is visible committed or not - you can get dirty read here - it is very fast.
- **Read committed:** Each query in a transaction can only see committed changes by other transactions - the most popular isolation level - the default isolation level in many databases.
- **Repeatable reads:** when you read a value the database will make sure that this row is not changing while the transaction is running.
- **Snapshot:** Takes a snapshot of the entire database for each transaction - this gets rid of all read phenomena.
- **Serializable:** No concurrency anymore, all transaction works one after one, which one should work first? this is the question the database have to answer.
# Consistency
Database consistency is 2 major things
**1- data consistency**: the data should be consistent over all the database, for example you can't have reference to user_id that is not existing.
**2- reads consistency:** the data read have to be consistent, the read might be inconsistent even if the data is consistent.
# Durability
Is the process of persisting the rights clients make in the database to a non volatile system storage, even I lost power -as a client- or my client crashed after my transaction committed I can see changes I made.

### Durability techniques:
- **WAL (write ahead log):** writing changes of the transaction in a log file and then write these changes to disk in the background.
- **Asynchronous snapshot:** we keep every thing in memory but asynchronously we write every thing in disk, this might cause some data lose.
- **AOF (Append-only file):** in Redis databases, this appends commands (SET, UPDATE. ...) into a file and then this file can be used to restore data if any error happened.

### Durability - OS cache
sometimes when the DBMS requests the OS to write changes into a file the OS write it to its cache to optimize performance -it is an OS thing not related to databases- and then the database deals as the changes committed to disk, this might cause data loss if a crash happened before OS write to the actual disk, to solve this problem there is a command called **Fsync** this makes sure the OS actually writes to disk not to its cache.

# Eventual consistency
Eventual consistency means that our database is not consistent **yet** but it would be consistent in future time, an example for that is when you have 3 database instances over 3 servers, and there is a change happened in one database on one server, but other database instances did not get that change so the data now is inconsistent across these database instances, but now or later the data will be consistent when the other instances listen to change that happened in the changed database instance.
- **Once your data in two places you are inconsistent**
- **Once you use cache you are inconsistent**
