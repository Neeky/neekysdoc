# mysql-dml-trace

```
#!/usr/bin/env bpftrace 

BEGIN {
    printf("mysql dispatch_command trace thead-os-id : %d \n", $1);
    printf("%-12s    %-21s    %-36s    sql\n", "thread", "timestamp", "fun")
}



// dispatch_command
uprobe:/usr/local/mysql/bin/mysqld:_Z16dispatch_commandP3THDPK8COM_DATA19enum_server_command /tid == $1/{
    if (arg2 == 3) {
        @times[tid] = nsecs;
        @sql[tid] = str(*arg1);
        @sql[tid] = str(*arg1);
        printf("tid: %d    time: %-12s    %-36s    sql: %-63s \n", tid, strftime("%H:%M:%S.%f", @times[tid]), "dispatch_command", str(*arg1));
    }   
}

uretprobe:/usr/local/mysql/bin/mysqld:_Z16dispatch_commandP3THDPK8COM_DATA19enum_server_command /@times[tid]/{
    $delta = nsecs - @times[tid];
    printf("tid: %d    time: %-12s    %-36s    sql: %-63s    duration: %d(us) \n\n", tid, strftime("%H:%M:%S.%f", nsecs), "dispatch_command", @sql[tid], $delta/1000);

    delete(@times[tid]);
    delete(@sql[tid]);

}


// Sql_cmd_insert_values::execute_inner
uprobe:/usr/local/mysql/bin/mysqld:_ZN21Sql_cmd_insert_values13execute_innerEP3THD /@times[tid]/{
    @inserts[tid] = nsecs;
    printf("tid: %d    time: %-12s    %-36s    sql: %-63s\n", tid, strftime("%H:%M:%S.%f", nsecs), "Sql_cmd_insert_values::execute_inner", "******");
}

uretprobe:/usr/local/mysql/bin/mysqld:_ZN21Sql_cmd_insert_values13execute_innerEP3THD /@times[tid]/{
    $inserts_delta = nsecs - @inserts[tid];
    printf("tid: %d    time: %-12s    %-36s    sql: %-63s    duration: %d(us) \n", tid, strftime("%H:%M:%S.%f", nsecs),  "Sql_cmd_insert_values::execute_inner", "******", $inserts_delta/1000);
}
```

运行效果
```
bpftrace /tmp/trace-sql.bt  4102459

WARNING: Addrspace is not set
Attaching 5 probes...
mysql dispatch_command trace thead-os-id : 4102459 
thread          timestamp                fun                                     sql
tid: 4102459    time: 22:26:20.256932    dispatch_command                        sql: select * from performance_schema.threads where processlist_id = 
tid: 4102459    time: 22:26:20.257704    dispatch_command                        sql: select * from performance_schema.threads where processlist_id =    duration: 771(us) 

tid: 4102459    time: 22:26:22.965839    dispatch_command                        sql: insert into t(a,b) values(3,3)                                  
tid: 4102459    time: 22:26:22.966332    Sql_cmd_insert_values::execute_inner    sql: ******                                                         
tid: 4102459    time: 22:26:22.967256    Sql_cmd_insert_values::execute_inner    sql: ******                                                             duration: 923(us) 
tid: 4102459    time: 22:26:22.968282    dispatch_command                        sql: insert into t(a,b) values(3,3)                                     duration: 2442(us) 
```