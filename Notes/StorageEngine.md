# Storage Engine

When using Oracle Database or Microsoft SQL Server, a single set of code is responsible for low-level database operations, such as retrieving a particular row from a table based on primary key value.

The MySQL server, however, has been designed so that multiple storage engines may be utilized to provide low-level database functionality, including resource locking and transaction management.

As of version 6.0, MySQL includes the following storage engines:

## Storage Engines

| Storage Engine | Description                                                                                                           |
| -------------- | --------------------------------------------------------------------------------------------------------------------- |
| MyISAM         | A nontransactional engine employing table locking                                                                     |
| MEMORY         | A nontransactional engine used for in-memory tables                                                                   |
| BDB            | A transactional engine employing page-level locking                                                                   |
| InnoDB         | A transactional engine employing row-level locking                                                                    |
| Merge          | A specialty engine used to make multiple identical MyISAM tables appear as a single table (a.k.a. table partitioning) |
| Maria          | A MyISAM replacement included in version 6.0.6 that adds full recovery capabilities                                   |
| Falcon         | A new (as of 6.0.4) high-performance transactional engine employing row-level locking                                 |
| Archive        | A specialty engine used to store large amounts of unindexed data, mainly for ar- chival purposes                      |

**MySQL** supports table-by-table storage engine choices.

For any tables that might take part in transactions, you should choose the **InnoDB** or **Falcon** storage engine, which uses row-level locking and versioning to provide the highest level of concurrency across the different storage engines.

Show all available engines:

```mysql
SHOW ENGINES;
```

To see which engine is used for a table,

```mysql
SHOW TABLE STATUS LIKE 'transaction';
```

### Set Storage Engine for A Table

```mysql
ALTER TABLE transaction ENGINE = INNODB;
```
