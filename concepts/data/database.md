# Database Management — Best Practices

**Scope**: RDBMS design, indexing, query optimization, transaction management, and operational database practices.

**Purpose**: Use this when designing databases, optimizing queries, managing transactions, and operating relational databases. For data pipeline patterns, see [Data Engineering](engineering.md). For data quality and integrity, see [Data Operations](operations.md).

## Table of Contents

- [1. Database Design](#1-database-design)
- [2. Indexing Strategies](#2-indexing-strategies)
- [3. Query Optimization](#3-query-optimization)
- [4. Transaction Management](#4-transaction-management)
- [5. Connection & Resource Management](#5-connection--resource-management)
- [6. Backup & Recovery](#6-backup--recovery)
- [7. Performance Tuning](#7-performance-tuning)
- [8. Partitioning & Sharding](#8-partitioning--sharding)
- [9. Replication & High Availability](#9-replication--high-availability)
- [10. Database Maintenance](#10-database-maintenance)

---

## 1. Database Design

### Normalization

- **First Normal Form (1NF)**: Eliminate repeating groups; atomic values
- **Second Normal Form (2NF)**: 1NF + remove partial dependencies
- **Third Normal Form (3NF)**: 2NF + remove transitive dependencies
- **Boyce-Codd Normal Form (BCNF)**: 3NF + every determinant is a candidate key
- **Trade-offs**: Higher normalization reduces redundancy but increases joins

### Denormalization

- **Purpose**: Improve query performance by reducing joins
- **When to denormalize**: Frequently joined tables, read-heavy workloads, small dimension tables
- **Common patterns**: Duplicate columns, pre-computed aggregates, flattened hierarchies
- **Maintenance cost**: Keep denormalized data in sync with source

### Schema Design Principles

- **Primary keys**: Single column preferred; composite keys when necessary
- **Foreign keys**: Enforce referential integrity; index foreign keys
- **Constraints**: NOT NULL, UNIQUE, CHECK constraints for data integrity
- **Data types**: Choose appropriate types (avoid over-sized types)
- **Naming conventions**: Consistent naming (tables, columns, indexes)

### Table Design

- **Column order**: Frequently queried columns first; fixed-length before variable-length
- **NULL handling**: Minimize NULLs; use default values where appropriate
- **Temporal columns**: Created_at, updated_at, deleted_at for auditability
- **Soft deletes**: Use deleted_at flag instead of hard deletes when needed

---

## 2. Indexing Strategies

### Index Types

- **B-tree indexes**: Default for most queries; supports range queries, sorting
- **Hash indexes**: Equality lookups only; no range queries
- **Composite indexes**: Multiple columns; order matters (leftmost prefix rule)
- **Covering indexes**: Include all columns needed by query (avoid table lookups)
- **Filtered indexes**: Index subset of rows (WHERE clause in index definition)
- **Unique indexes**: Enforce uniqueness; automatically created for PRIMARY KEY

### Index Design

- **When to index**: Foreign keys, frequently filtered columns, join columns, ORDER BY columns
- **When not to index**: Low-cardinality columns, rarely queried tables, frequently updated columns
- **Index maintenance**: Monitor index usage; drop unused indexes
- **Index bloat**: Rebuild/reorganize indexes periodically

### Index Selection

- **Query patterns**: Index columns used in WHERE, JOIN, ORDER BY clauses
- **Composite index order**: Most selective columns first; match query patterns
- **Covering index**: Include columns in SELECT to avoid table lookups
- **Partial indexes**: Index filtered subset (e.g., WHERE status = 'active')

---

## 3. Query Optimization

### Execution Plans

- **Analyze plans**: Use EXPLAIN/EXPLAIN ANALYZE to understand query execution
- **Key metrics**: Cost, rows scanned, index usage, join algorithms
- **Red flags**: Full table scans, missing indexes, expensive sorts, nested loops on large tables

### Query Patterns

- **Avoid SELECT ***: Select only needed columns
- **Use LIMIT**: Limit result sets when possible
- **Avoid functions in WHERE**: Functions on indexed columns prevent index usage
- **Use EXISTS vs IN**: EXISTS often more efficient for subqueries
- **Avoid correlated subqueries**: Use JOINs when possible

### Join Optimization

- **Join order**: Smaller tables first; most selective joins first
- **Join algorithms**: Hash join (large tables), nested loop (small tables), merge join (sorted)
- **Index on join columns**: Index foreign keys and join columns
- **Avoid cartesian products**: Always specify join conditions

### Aggregation & Sorting

- **Pre-aggregate**: Use materialized views or summary tables for common aggregations
- **Index for ORDER BY**: Index columns used in ORDER BY to avoid sorting
- **Window functions**: Use window functions instead of correlated subqueries when possible
- **DISTINCT**: Use only when necessary; consider GROUP BY alternatives

---

## 4. Transaction Management

### ACID Properties

- **Atomicity**: All or nothing; rollback on failure
- **Consistency**: Database remains in valid state
- **Isolation**: Concurrent transactions don't interfere
- **Durability**: Committed changes persist

### Isolation Levels

- **Read Uncommitted**: Lowest isolation; dirty reads possible
- **Read Committed**: Default in most databases; no dirty reads
- **Repeatable Read**: Consistent reads within transaction
- **Serializable**: Highest isolation; prevents phantom reads
- **Trade-off**: Higher isolation = better consistency, lower concurrency

### Locking

- **Row-level locks**: Fine-grained; better concurrency
- **Table-level locks**: Coarse-grained; lower concurrency
- **Deadlocks**: Detect and resolve; use consistent lock ordering
- **Lock timeouts**: Set timeouts to prevent indefinite waits

### Transaction Best Practices

- **Keep transactions short**: Minimize lock duration
- **Avoid long-running transactions**: Break into smaller transactions
- **Use appropriate isolation level**: Don't use higher isolation than needed
- **Handle deadlocks**: Implement retry logic with exponential backoff

---

## 5. Connection & Resource Management

### Connection Pooling

- **Purpose**: Reuse connections; avoid connection overhead
- **Pool sizing**: Size based on concurrent queries; monitor pool utilization
- **Connection timeout**: Set appropriate timeouts
- **Idle connections**: Close idle connections to free resources

### Resource Limits

- **Max connections**: Limit concurrent connections per application/user
- **Query timeout**: Set query timeouts to prevent runaway queries
- **Memory limits**: Limit memory per query/connection
- **CPU limits**: Throttle CPU-intensive queries

### Connection Management

- **Always close connections**: Use try-finally or connection managers
- **Connection string security**: Store in secure vault; rotate credentials
- **Connection health checks**: Verify connections before use
- **Failover handling**: Implement retry logic for connection failures

---

## 6. Backup & Recovery

### Backup Strategies

- **Full backup**: Complete database backup; base for recovery
- **Incremental backup**: Changes since last full backup
- **Differential backup**: Changes since last full backup (cumulative)
- **Transaction log backup**: For point-in-time recovery (SQL Server, PostgreSQL)

### Backup Frequency

- **Full backup**: Daily or weekly depending on data volume
- **Incremental/differential**: More frequent (hourly or every few hours)
- **Transaction log**: Very frequent (every 15-30 minutes) for point-in-time recovery
- **Retention**: Retain backups per compliance requirements

### Recovery Strategies

- **Point-in-time recovery**: Restore to specific timestamp using transaction logs
- **Recovery time objective (RTO)**: Target time to restore service
- **Recovery point objective (RPO)**: Maximum acceptable data loss
- **Test restores**: Regularly test backup restoration procedures

### Backup Best Practices

- **Automated backups**: Schedule automated backups
- **Off-site storage**: Store backups in separate location/region
- **Encryption**: Encrypt backups at rest
- **Backup validation**: Verify backup integrity regularly

---

## 7. Performance Tuning

### Query Tuning

- **Identify slow queries**: Use query logs, slow query logs, monitoring tools
- **Analyze execution plans**: Understand query execution; optimize bottlenecks
- **Add missing indexes**: Index frequently queried columns
- **Rewrite queries**: Simplify complex queries; use appropriate joins

### Index Tuning

- **Monitor index usage**: Track index usage statistics
- **Remove unused indexes**: Drop indexes that aren't used
- **Rebuild fragmented indexes**: Rebuild/reorganize indexes periodically
- **Update statistics**: Keep statistics current for query optimizer

### Resource Tuning

- **Memory allocation**: Allocate sufficient memory for buffer pool, query cache
- **CPU allocation**: Ensure adequate CPU for database workload
- **I/O optimization**: Use SSD storage; optimize I/O patterns
- **Network optimization**: Minimize network latency for distributed databases

### Monitoring

- **Query performance**: Monitor query execution times, slow queries
- **Resource utilization**: CPU, memory, disk I/O, network
- **Connection metrics**: Active connections, connection pool utilization
- **Lock contention**: Monitor lock waits, deadlocks

---

## 8. Partitioning & Sharding

### Table Partitioning

- **Range partitioning**: Partition by date ranges, value ranges
- **Hash partitioning**: Distribute data evenly across partitions
- **List partitioning**: Partition by discrete values (regions, status)
- **Composite partitioning**: Combine multiple partitioning strategies

### Partitioning Benefits

- **Query performance**: Partition pruning (query only relevant partitions)
- **Maintenance**: Manage partitions independently (backup, index rebuild)
- **Data lifecycle**: Drop old partitions easily
- **Parallel processing**: Process partitions in parallel

### Sharding

- **Horizontal sharding**: Split data across multiple databases
- **Shard key selection**: Choose shard key carefully (even distribution, query patterns)
- **Shard routing**: Route queries to correct shard
- **Cross-shard queries**: Minimize cross-shard queries (expensive)

### Sharding Strategies

- **Range-based sharding**: Shard by value ranges
- **Hash-based sharding**: Hash shard key to determine shard
- **Directory-based sharding**: Lookup table maps keys to shards
- **Consistent hashing**: Minimize rebalancing when adding/removing shards

---

## 9. Replication & High Availability

### Replication Types

- **Master-slave (primary-replica)**: One primary, multiple read replicas
- **Master-master (multi-primary)**: Multiple primaries; conflict resolution needed
- **Synchronous replication**: Wait for replica confirmation (strong consistency)
- **Asynchronous replication**: Don't wait for replica (better performance, eventual consistency)

### Read Replicas

- **Purpose**: Scale read workloads; offload read queries from primary
- **Use cases**: Reporting, analytics, read-heavy applications
- **Lag monitoring**: Monitor replication lag; route queries accordingly
- **Failover**: Promote replica to primary on primary failure

### High Availability Patterns

- **Active-passive**: Standby database; failover on primary failure
- **Active-active**: Multiple active databases; load balancing
- **Automatic failover**: Automatic promotion of replica on primary failure
- **Failover testing**: Regularly test failover procedures

### Consistency Trade-offs

- **Strong consistency**: Synchronous replication; higher latency
- **Eventual consistency**: Asynchronous replication; lower latency
- **Read-after-write**: Route reads to primary for immediate consistency
- **Stale reads acceptable**: Route reads to replicas for better performance

### CAP Theorem

- **CAP Theorem**: In distributed systems, can only guarantee 2 of 3: Consistency, Availability, Partition tolerance
- **CP systems**: Strong consistency, partition tolerance (e.g., distributed databases with quorum)
- **AP systems**: High availability, partition tolerance, eventual consistency (e.g., DynamoDB, Cassandra)
- **CA systems**: Consistency and availability (single-node systems, not truly distributed)
- **Trade-offs**: Choose based on use case - strong consistency (CP) vs high availability (AP)
- **Practical implications**: Most distributed databases choose AP (eventual consistency) or CP (strong consistency with availability trade-offs)

---

## 10. Database Maintenance

### Index Maintenance

- **Rebuild indexes**: Rebuild fragmented indexes (recreates index)
- **Reorganize indexes**: Reorganize fragmented indexes (defragments)
- **Update statistics**: Keep statistics current for query optimizer
- **Monitor fragmentation**: Track index fragmentation; rebuild when needed

### Statistics Maintenance

- **Auto-update statistics**: Enable automatic statistics updates
- **Manual statistics update**: Update statistics after bulk loads
- **Statistics sampling**: Balance accuracy vs update time
- **Histogram maintenance**: Keep histograms current for query optimization

### Data Maintenance

- **Vacuum/cleanup**: Remove dead tuples, reclaim space (PostgreSQL)
- **Reindex**: Rebuild all indexes periodically
- **Check integrity**: Run integrity checks (CHECKDB, VACUUM ANALYZE)
- **Archive old data**: Move old data to archive tables/partitions

### Monitoring & Alerts

- **Database size**: Monitor database growth; alert on size thresholds
- **Table bloat**: Monitor table bloat; vacuum/rebuild when needed
- **Index bloat**: Monitor index bloat; rebuild when needed
- **Long-running queries**: Alert on queries exceeding threshold
- **Connection limits**: Alert when approaching connection limits

---

> **Note**: For data pipeline patterns, see [Data Engineering](engineering.md). For data quality and integrity practices, see [Data Operations](operations.md). For data governance, see [Data Governance](governance.md).

