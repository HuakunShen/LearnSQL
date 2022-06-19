# Transactions

Read [Locking](./Locking.md) first.

> Transaction is a device for grouping together multiple SQL statements such that either all or none of the statements succeed.

Consider this scenario:

You attempt to transfer $500 from your savings account to your checking account, you would be a bit upset if the money were successfully withdrawn from your savings account but never made it to your checking account.

A transaction can be used to protect against this kind of error. The transaction (group of SQL statements) is executed, 

- if every statement succeeds, end the transaction by issuing the `commit` command. 
- if something unexpected happens, execute `rollback` command, which instructs the server to undo all changes made since the transaction began.

## Psuedo Code

```sql
START TRANSACTION;
/* withdraw money from first account, making sure balance is sufficient */ UPDATE account SET avail_balance = avail_balance - 500 WHERE account_id = 9988   AND avail_balance > 500;
IF <exactly one row was updated by the previous statement> THEN   
	/* deposit money into second account */   
	UPDATE account SET avail_balance = avail_balance + 500 WHERE account_id = 9989;   
	IF <exactly one row was updated by the previous statement> THEN     
		/* everything worked, make the changes permanent */     
		COMMIT;   
	ELSE
		/* something went wrong, undo all changes in this transaction */     			
		ROLLBACK;
	END IF;
ELSE
	/* insufficient funds, or error encountered during update */
	ROLLBACK;
END IF;
```

## Other Cases

| If                                                                                                                                  | Then                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `update` statements complete but server shuts down before a `commit` or `rollback` are executed                                     | Transaction will be rolled back when the server comes back online. (One of the tasks that a database server must complete before coming online is to find any incomplete transactions that were underway when the server shut down and to roll them back.) |
| A transaction completed, issues a `commit`, but server shuts down before the changes have been applied to permanent storage (disk). | Database server must reapply the changes from the transaction when the server is restarted (*durability* feature)                                                                                                                                          |

## Two Ways of Transaction Creation

1. An active transaction is always associated with a database session, so there is no need or method to explicitly begin a transaction. When the current transaction ends, the server automatically begins a new transaction for your session.
2. **auto-commit**. You explicitly begin a transaction, individual SQL statements are automatically committed independently of one another. To begin a transaction, you must first issue a command.

| Database             | Approach |
| -------------------- | -------- |
| Microsoft SQL Server | 1        |
| Oracle Database      | 2        |
| MySQL                | 2        |

The advantage of  Approach 1 is that even if you are issuing only a single SQL command, you can roll back the changes. With Approach 2, once executed, changes are permanent (unless there is a backup)

| Database             | Command             |
| -------------------- | ------------------- |
| Microsoft SQL Server | `start transaction` |
| MySQL                | `begin transaction` |

These 2 servers (approach 2) are in `auto-commit mode`, individual statements are automatically committed by the server.

### Turn Off Auto-Commit

Boath MySQL and SQL Server can turn off auto-commit mode for individual sessions (acting just like Oracle Database regarding transactions).

| Database             | Command                        |
| -------------------- | ------------------------------ |
| Microsoft SQL Server | `SET IMPLICIT_TRANSACTIONS ON` |
| MySQL                | `SET AUTOCOMMIT=0`             |

After this, all SQL commands take place within the scope of a transaction and must be explicitly **committed** or **rolled back**. 

## Ending a Transaction

You must explicitly end a transaction for the changes to become permanent. You can do this with `commit` or `rollback`.

### commit

Instructs the server to mark the changes as permanent and release any resources (i.e., page or row locks) used during the transaction.

### rollback

Instructs the server to return the data to its pretransaction state. After the rollback has been completed, any resources used by your session are released.

### Other Scenarios

1. Server shuts down, transaction will be rolled back automatically when the server is restarted.

2. Issue an SQL schema statement, such as `alter table`, which will cause the current transaction to be committed and a new transaction to be started.

    Alternamtions to a DB (new table/index, removal of column, etc.) can not be rolled back, so commands that alter the schema must take place outside a transaction.

3. Issuing another `start transaction` command will cause the previous transaction to be committed.

4. Server prematurely ends a transaction because it detects a ***deadlock*** and decides that the transaction is the culprit, transaction will be rolled back and return an error message.

    A deadlock occurs when two different transactions are waiting for resources that the other transaction currently holds.
	<details>
	<summary>Example of Deadlock</summary>
	Transaction A might have just updated the account table and is waiting for a write lock on the transaction table, while transaction B has inserted a row into the transaction table and is waiting for a write lock on the account table. If both transactions happen to be modifying the same page or row (depending on the lock granularity in use by the database server), then they will each wait forever for the other transaction to finish and free up the needed resource.
	</details>
    When a deadlock is detected, one of the transactions is chosen (either arbitrarily or by some criteria) to be rolled back so that the other transaction may proceed. Most of the time, the terminated transaction can be restarted and will succeed without encountering another deadlock situation.
    
    If deadlocks become fairly common, then you may need to modify the applications that access the database to decrease the probability of deadlocks (one common strategy is to ensure that data resources are always accessed **in the same order**, such as always modifying account data before inserting transaction data)

## Transaction Savepoints

You may not want to rollback a entire transaction, but to a certain stage of it.

One or multiple *savepoints* can be established within a transaction and use them to roll back to a particular location rather than rolling back everything.

Read [Storage Engine](./StorageEngine.md).

All *savepoints* must be given a name.

For **MySQL**

```sql
SAVEPOINT my_savepoint;							-- set a savepoint
ROLLBACK TO SAVEPOINT my_savepoint	-- rollback to a particular savepoint
```

For **SQL Server**

```sql
save transaction my_savepoint;							-- set a savepoint
rollback transaction TO SAVEPOINT my_savepoint	-- rollback to a particular savepoint
```

### Example

```sql
START TRANSACTION;
UPDATE product SET date_retired = CURRENT_TIMESTAMP() 
WHERE product_cd = 'XYZ'; 

SAVEPOINT before_close_accounts;

UPDATE account SET status = 'CLOSED', close_date = CURRENT_TIMESTAMP(),   last_activity_date = CURRENT_TIMESTAMP() 
WHERE product_cd = 'XYZ'; 

ROLLBACK TO SAVEPOINT before_close_accounts;

COMMIT;
```

In this example, the mythical XYZ product is retired but none of the accounts are closed.

### Notes

- A `savepoint` doesn't save anything, a `commit` has to be executed for transaction to be made permanently.
- If `rollback` is executed without naming a savepoint, all savepoints within the transaction will be ignored and the entire transaction will be undone.



















