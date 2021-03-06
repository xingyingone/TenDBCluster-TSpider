# TSpider Variables Reference

TSpider compatibility MySQL Variables, the following mainly introduces the new parameters.

**`log_sql_use_mutil_partition`**  
Scope: GLOBAL  
Default Value: OFF  
If true, all cross-sharding querys will be treated as slow query 

**`spider_max_connections`**  
Scope: GLOBAL  
Default Value: 0  
the values, as the max conncetion from TSpider to TenDB. Default 0, mean unlimit the connections. General recommend set to 200.

**`spider_index_hint_pushdown`**  
Scope: SESSION|GLOBAL  
Default Value: OFF  
switch to control if push down index hint, like force_index

**`spider_auto_increment_mode_switch`**  
Scope: GLOBAL  
Default Value: OFF  
Dynamic: No  
Whether the auto increment is enabled. The value can be 0 (or OFF) to disable or 1 (or ON) to enable. If on, TSpider will generate a global non-continuous unique identity for new rows. Identity only ensure incremental on the same TSpider node.

**`spider_auto_increment_step`**  
Scope: GLOBA  
Default Value: 17  
Dynamic: No
The step of the global non-continuous unique identity generated by TSpider node. All TSpider nodes in cluster must be the same.

**`spider_auto_increment_mode_value`**  
Scope: GLOBAL  
Default Value: 1  
Dynamic: No  
TSpider node generate global non-continuous unique identity's start number. All TSpider's value must be different. Valid value can be computed by TSpider's increment value modulo spider_auto_increment_step.  
For example, if cluster's spider_auto_increment_step is 17, they 4 TSpider nodes, first TSpider node's mode values is 1(1%17), others are 2(2%17), 3(3%17), 4(4%17). 

**`spider_net_read_timeout`**  
Scope: SESSION|GLOBAL  
Default Value: 3600  
Wait timeout in microseconds of receiving data from TenDB node

**`spider_net_write_timeout`**  
Scope: SESSION|GLOBAL  
Default Value: 3600  
Wait timeout in microseconds of sending data to TenDB node 

**`SPIDER_SUPPORT_XA`**  
Scope: GLOBAL  
Default Value: ON  
Dynamic: No  
XA support

<a id="spider_bgs_mode"></a>

**`spider_bgs_mode`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
Whether the parallel distribute is enabled. The value can be 0 (or OFF) to disable or 1 (or ON) to enable. If on, after TSpider node received SQL, it will distribute to TenDB node which need to execute in parallel, at last merge the result and return to application.  
limit x,y, multi table join, transaction, etc. which not allowed parallel and not affected by this variable.

<a id="spider_bgs_dml"></a>

**`spider_bgs_dml`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
If enable, INSERT, UPDATE, DELETE will be execute in parallel.


<a id="SPIDER_INTERNAL_XA"></a>

**`SPIDER_INTERNAL_XA`**  
Scope: GLOBAL  
Default Value: OFF  
whether use inner xa transaction

**`SPIDER_TRANS_ROLLBACK`**  
Scope: SESSION  
Default Value: OFF  
If off, when statement execute failed in transaction, subsequent statement can execute continue; If on, when failed, transaction will rollback. For XA scenarios, recommend set to ON.

**`SPIDER_GENERAL_LOG`**  
Scope: GLOBAL  
Default Value: OFF  
Log query to TenDB node in general log.

**`SPIDER_IGNORE_CREATE_LIKE`**  
Scope: GLOBAL  
Default Value: ON  
If on, CREATE TABLE LIKE will ignore sharding rules on table structure.

<a id="SPIDER_IGNORE_AUTOCOMMIT"></a>
**`SPIDER_IGNORE_AUTOCOMMIT`**  
Scope: GLOBAL   
Default Value: OFF  
If on, when TenDB node had set autocommit to 0, after current transaction commit, TenDB node's autocommit will be set to 1，the autocommit(0) only affect current transaction, even it's the same connection.

<a id="quick-mode"></a>
**`SPIDER_QUICK_MODE`**  

Scope: SESSION|GLOBAL  
Default Value：1  
Sets the backend query buffering to cache on the TenDB node or in the local buffer  
- -1 Uses the table parameter.  
- 0 Local buffering, it acquires records collectively with store_result.  
- 1 TenDB buffering, it acquires records one by one. Interrupts don't wait and recovery on context switch back.  
- 2 TenDB buffering, it acquires records one by one. Interrupts wait to the end of the acquisition.  
- 3 Local buffering, uses a temporary table on disk when the result-set is greater than the value of the spider_quick_page_size system variable.  

**`SPIDER_IDLE_CONN_RECYCLE_INTERVAL`**  
Scope: GLOBAL  
Default Value: 3600  
The interval time for TSpider to recycle idle connection.

**`SPIDER_GET_STS_OR_CRD`**  
Scope: GLOBAL  
Default Value: OFF  
If off, spider not collect statistics from TenDB node.   


**`DDL_EXECUTE_BY_CTL`**  
Scope: SESSION|GLOBAL  
Default Value: OFF  
If on, DDL statement on TSpider will directly forward to Tdbctl execute.


**`TDBCTL_WRAPPER_NAME`**  
Scope: GLOBAL  
Default Value: TDBCTL  
Dynamic: 0  
Tdbctl node's wrapper_name in mysql.servers.

**`TDBCTL_SKIP_DDL_CONVERT_DB`**  
Scope: GLOBAL  
Default Value: performance_schema,information_schema,mysql,test,db_infobase  
Dynamic: 0  
Default Value`performance_schema,information_schema,mysql,test,db_infobase`，Table under these schema's ddl will not forward to Tdbctl node。

**`SPIDER_PARALLEL_LIMIT`**  
Scope: GLOBA  
Default Value: OFF  
If on, statement with `limit` will execute in parallel.


**`SPIDER_PARALLEL_GROUP_ORDER`**  
Scope: GLOBAL  
Default Value: ON  
If on, statement include `group by, order by` will execute in parallel.


 **`SPIDER_CONN_RECYCLE_MODE`**  
Scope: SESSION|GLOBAL  
Default Value: 1  
Connection recycle mode.
- 0 Disconnect.
- 1 Recycle by all sessions.
- 2 Recycle in the same session.


**`SPIDER_FORCE_COMMIT`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
Behavior when error occurs on XA PREPARE, XA COMMIT, and XA ROLLBACK statements.
- 0 Returns the error.
- 1 Returns the error when the xid doesn't exist, otherwise it continues processing the XA transaction.
- 2 Continues processing the XA transaction, disregarding all errors.


**`SPIDER_INTERNAL_OFFSET`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
Skip records when acquired from the TenDB.
- -1 Uses the table parameter.
- 0 or more : Number of records to skip.  
For example, `set SPIDER_INTERNAL_OFFSET = 2`, when TSpider execute 
`select c from t`，statement `select  c from t limit 2,9223372036854775807` will distribute to TenDB node


**`SPIDER_INTERNAL_LIMIT`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
Limits the number of records when acquired from a TenDB node.
- -1 The table parameter is adopted.
- 0 or greater: Records limit.
For example, `set SPIDER_INTERNAL_LIMIT=2`, when TSpider execute `select c from t`, statement ` select c from t limit 2;` will distribute to TenDB node.



**`SPIDER_SPLIT_READ`**  
Scope: SESSION|GLOBAL  
Default Value: -1  
Number of records in chunk to retry the result when a range query is sent to TenDB node.
- -1 Uses the table parameter.
- 0 or more: Number of records.  
For example，if value=-1, when TSpider execute `select * from t1`，statement `select * from t1` will distribute to TenDB node;
if SPIDER_SPLIT_READ=3， the blow statement will distribute:
select `c` from `spider_test_0`.`t1` limit 3 ;  
select `c` from `spider_test_0`.`t1` limit 3,3;   
select `c` from `spider_test_0`.`t1` limit n,3;  


**`SPIDER_SEMI_SPLIT_READ`**  
Scope: SESSION|GLOBAL  
Default Value: 1  
Sets the limit value for the spider_semi_split_read system variable.
- -1 Uses the table parameter.
- 0 or more: The limit value


**`SPIDER_SEMI_SPLIT_READ_LIMIT`**  
Scope: SESSION|GLOBAL  
Default Value: -1  
Sets the limit value for the spider_semi_split_read system variable.
- -1 Uses the table parameter.  
- 0 or more: The limit value.  
For example:   
spider_semi_split_read=1  
spider_semi_split_read_limit=-1  
when TSpider execute `select id from test_incr limit 4`, each TenDB node will execute `select id from test_incr limit 4`.
If `spider_semi_split_read_limit=2`，each TenDB node will execute `select id from test_incr limit 2;select id from test_incr limit 2,2`.



**`SPIDER_SEMI_TRX_ISOLATION`**  
Scope: SESSION|GLOBAL  
Default Value: -1  
Set consistency during range SQL execution when spider_sync_trx_isolation is 1
- -1 OFF
- 0 READ UNCOMMITTED
- 1 READ COMMITTED
- 2 REPEATABLE READ
- 3 SERIALIZABLE



**`SPIDER_SELUPD_LOCK_MODE`**  
Scope: SESSION|GLOBAL    
Default Value: 1  
Local lock mode on INSERT SELECT.
- -1 Uses the table parameter.
- 0 Takes no locks.
- 1 Takes shared locks.
- 2 Takes exclusive locks


**`SPIDER_SYNC_AUTOCOMMIT`**  
Scope: SESSION|GLOBAL  
Default Value: ON  
Whether to push down local auto-commits to TenDB node.
- OFF Pushes down local auto-commits.
- ON Doesn't push down local auto-commits



**`SPIDER_SYNC_TIME_ZONE`**  
Scope: SESSION|GLOBAL  
Default Value: ON  
Whether to push the local time zone down to TenDB node.
- OFF Doesn't synchronize time zones.
- ON Synchronize time zones




**`SPIDER_INTERNAL_SQL_LOG_OFF`**  
Scope: SESSION|GLOBAL  
Default Value: 1  
Whether to log SQL statements sent to the TenDB node in the General Query Log. Explicitly setting this system variable to either ON or OFF causes the Spider node to send a SET sql_log_off statement to each of the data nodes using the SUPER privilege.
- OFF Logs SQL statements to the TenDB node.
- ON Doesn't log SQL statements to the TenDB node. 


**`SPIDER_BULK_SIZE`**  
Scope: SESSION|GLOBAL  
Default Value: 16000  
Size in bytes of the buffer when multiple grouping multiple INSERT statements in a chunk, (that is, bulk inserts).
- -1 The table parameter is adopted.
- 0 or greater: Size of the buffer.


**`SPIDER_BULK_UPDATE_SIZE`**  
Scope: SESSION|GLOBAL  
Default Value: 16000  
Bulk update and delete mode. Note: If you use a non-default value for the spider_bgs_mode or `spider_split_read` system variables, Spider sets this variable to 2.
- -1 Uses the table parameter.
- 0 Sends UPDATE and DELETE statements one by one.
- 1 Collects multiple UPDATE and DELETE statements, then sends the collected statements one by one.
- 2 Collects multiple UPDATE and DELETE statements and sends them together.



**`SPIDER_CONNECT_TIMEOUT`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
Timeout in seconds to declare TenDB node unresponsive when opening a connection. Change for high-latency networks.
- -1 The table parameter is adopted.
- 0 Less than 1.
- 1 and greater: Number of seconds


**`SPIDER_DIRECT_DUP_INSERT`**  
Scope: SESSION|GLOBAL  
Default Value: 1  
Manages duplicate key check for REPLACE, INSERT IGNORE and LOAD DATA LOCAL INFILE to TenDB node. This can save on network roundtrips if the key always maps to a single partition. For bulk operations, records are checked for duplicate key errors one by one on the TenDB node, unless you set it to avoid duplicate checks on local servers (1).
- -1 Uses the table parameter.
- 0 Performs duplicate checks on the local server.
- 1 Avoids duplicate checks on the local server.


**`SPIDER_REMOTE_ACCESS_CHARSET`**  
Scope: SESSION|GLOBAL  
Default Value: NULL  
Forces session character set when connecting to the backend server. This can improve connection time performance  


**`SPIDER_REMOTE_AUTOCOMMIT`**  
Scope: GLOBAL  
Default Value: -1  
Sets the auto-commit mode when connecting to TenDB node. This can improve connection time performance.
- -1 Doesn't change the auto-commit mode.
- 0 Sets the auto-commit mode to 0.
- 1 Sets the auto-commit mode to 1.



**`SPIDER_REMOTE_DEFAULT_DATABASE`**  
Scope: GLOBAL  
Default Value: NULL   
Sets the local default database when connecting to backend servers. This can improve connection time performance.
- 0 OFF
- 1 ON


**`SPIDER_CONNECT_RETRY_INTERVAL`**  
Scope: SESSION|GLOBAL  
Default Value: 1000  
Interval in microseconds for connection failure due to too many connection processes


**`SPIDER_QUICK_MODE_ONLY_SELECT`**  
Scope: GLOBAL  
Default Value: ON  
If on, for complex statement(such as: delete/insert into select.. through limit x, y to batch fetch records) will use `spider_quick_mode` with value 0.


**`SPIDER_IGNORE_XA_LOG`**  
Scope: SESSION|GLOBAL  
Default Value: ON  
If on, not log XA log. This can improve performance.


**`SPIDER_GROUP_BY_HANDLER`**  
Scope: GLOBAL  
Default Value: OFF  
If off, TSpider node disable `direct join`;


**`SPIDER_RONE_SHARD_SWITCH`**  
Scope: GLOBAL  
Default Value: ON  
If on, random access a TenDB node. For example, statement `select spider_rone_shard from tb limit 1` will fetch result from anyone TenDB node random.


**`SPIDER_QUERY_ONE_SHARD`**  
Scope: GLOBAL  
Default Value: OFF  
If on, statement `update/delete/select` must use `shard_key` as WHERE condition.For two table join, at least one table had specified `shard_key`.


**`SPIDER_TRANSACTION_ONE_SHARD`**  
Scope: GLOBAL  
Default Value: OFF  
If on, multi query in the same transaction must routing to the same TenDB node.


>if specify table to `config_table`，will skip `spider_query_one_shard`'s restrict，but can't skip `spider_transaction_one_shard`.
config_table usage:  
```sql
    CREATE TABLE `t6` (  
        `id` int(11) NOT NULL,  
         PRIMARY KEY (`id`)  
    ) ENGINE=InnoDB  COMMENT='shard_key "id", config_table "true"'  
```
>Note: spider_query_one_shard、spider_transaction_one_shard only effect under non-super privilge.



**`SPIDER_DIRECT_LIMIT_IN_GROUP`**  
Scope: GLOBAL  
Default Value: FALSE  
If false, statement with `group by + limit` not distribute `limit` to TenDB.  


**`SPIDER_MODIFY_STATUS_INTERVAL`**  
Scope: GLOBAL  
Default Value: 28800  
The interval to update `spider_table_status`.



**`SPIDER_SLOW_LOG`**  
Scope: GLOBAL  
Default Value: OFF  
If on, TSpider node will record all statement which distribute to TenDB.



**`SPIDER_IGNORE_SINGLE_SELECT_INDEX`**  
Scope: GLOBAL  
Default Value: ON  
If on, SELECT involve single table not use index.



**`SPIDER_IGNORE_SINGLE_UPDATE_INDEX`**  
Scope: GLOBAL  
Default Value: ON  
If on, UPDATE involve single table not use index.


**`SPIDER_DELETE_ALL_ROWS_TYPE`**  
Scope: SESSION|GLOBAL  
Default Value: 1  
If on, DELETE will return all effect rows number.



**`SPIDER_CONNECT_ERROR_INTERVAL`**  
Scope: GLOBAL  
Default Value: 1
Interval in microseconds for return connection error.


**`SPIDER_VERSION`**
Scope: GLOBAL  
Default Value: 1  
TSpider engine version.


**`SPIDER_INTERNAL_XA_ID_TYPE`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
XID type


**`SPIDER_CONNECT_RETRY_COUNT`**  
Scope: SESSION|GLOBAL  
Default Value: 20  
Retry times for TSpider to connect TenDB.


**`SPIDER_CONNECT_MUTEX`**  
Scope: GLOBAL  
Default Value: OFF  
If on, TSpider will use lock when establish connection.



**`SPIDER_READ_ONLY_MODE`**  
Scope: SESSION|GLOBAL  
Default Value: 0  
If on, TSpider engine related to table read-only. 



**`SPIDER_LOG_RESULT_ERRORS`**  
Scope: GLOBAL  
Default Value: 1  
If on, TSpider log execute errors.


**`SPIDER_LOG_RESULT_ERROR_WITH_SQL`**  
Scope: GLOBAL  
Default Value: 3  
spider result log level. For example, if value 3, only log TSpider and TenDB's query when error happend. 


**`SPIDER_CONN_WAIT_TIMEOUT`**  
Scope: GLOBAL  
Default Value: 20  
Wait in microseconds for get connection from connection pool.



**`SPIDER_REMOTE_TIME_ZONE`**  
Scope: GLOBAL  
Default Value: NULL  
The time_zone set on TenDB when establish connection. 



**`SPIDER_REMOTE_SQL_LOG_OFF`**  
Scope: GLOBAL  
Default Value: 0  
If 0, TenDB's general_log not log statement distributed by TSpider, otherwise log all queryes.



**`SPIDER_REMOTE_TRX_ISOLATION`**  
Scope: GLOBAL  
Default Value: -1  
The TRX_ISOLATION set on TenDB when establish connection.
- -1 OFF
- 0 READ UNCOMMITTED
- 1 READ COMMITTED
- 2 REPEATABLE READ
- 3 SERIALIZABLE




**`SPIDER_QUICK_PAGE_SIZE`**  
Scope: SESSION|GLOBAL  
Default Value: 1000  
The number of each batch to fetch from result set.


<a id="SPIDER_WITH_BEGIN_COMMIT"></a>

**`SPIDER_WITH_BEGIN_COMMIT`**  
Scope: SESSION|GLOBAL  
Default Value: OFF  
If off, implicit transaction not distribute BEGIN/COMMIT to TenDB.



**`SPIDER_CLIENT_FOUND_ROWS`**  
Scope: SESSION|GLOBAL  
Default Value: OFF  
If off, mysql_affercted_rows not return matched row number.



**`SPIDER_LOCK_EXCHANGE`**   
Scope: SESSION|GLOBAL  
Default Value: OFF    
If off, not convert `select lock` to `lock tables`.