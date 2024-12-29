# ElastiCache

- It is an in-memory database for application which need high-end performance
- It is orders of magnitude faster than a classic DB.
- ElastiCache provides 2 different engines: Managed Redis and MemcacheD as a service
- Can be used to store session date, making stateful applications stateless
- Using `ElastiCache requires application code changes!`

## Redis vs MemcacheD

- Both offer sub-millisecond access to data
- MemcacheD supports simple data structures (string), while Redis can support more advanced type of data: lists, sets, sorted sets, hashes, bit arrays, etc.
- Redis supports replication of data across multiple AZs
- MemcacheD supports multiple nodes with manual sharding, but it does not supports `"true" replication across AZs for scalability reasons`
- Redis supports backups and restores
- MemcacheD does not support persistance and backups.
- MemcacheD is multi-threaded by design.
- Redis supports transactions (multiple operations at once)
## ElastiCache: Redis vs Memcached
### Redis:
- Multi AZ with auto-failover
- Read replicas can be used to scale reads and have high availability
- Data Durability can be enabled using AOF persistance
- Backups can be restored
### Memcached:
- Multi-node for partition of data (sharding)
- No persistance
- No backup and restore
- Multi-threaded architecture