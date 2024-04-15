## AttemptFailureDetectionRegistration
前文我们说到了 GetReplicationAnalysis 函数，它会从元数据中扫出宕机的实例信息(ReplicationAnalysis 类型)；在后面的恢复逻辑中 AttemptFailureDetectionRegistration 会把这个宕机侦测信息写回到 `topology_failure_detection` 表。

之所以叫 attempt , 是因为不一定保证能插入的，它使用的是 insert ignore into .

```sql
insert ignore into topology_failure_detection(
        hostname,
        port,
        in_active_period,
        end_active_period_unixtime,
        processing_node_hostname,
        processcing_node_token,
        analysis,
        cluster_name,
        cluster_alias,
        count_affected_slaves,
        slave_hosts,
        is_actionable,
        start_active_period)
values(
        'git.sqlpy.com',
        4408,
        1,
        0,
        'git.sqlpy.com',
        '7f35a735aaaa1624e40c405f7d920650458606f5f57353ec342bf6fde916656e',
        'DeadMaster',
        'git.sqlpy.com:4408',
        'git',
        2,
        'git.sqlpy.com:4407,git.sqlpy.com:4406',
        1,
        now());
```

---