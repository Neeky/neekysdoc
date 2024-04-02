## 空文件
```bash
=== RotateEvent ===
Date: 1970-01-01 08:00:00
Log position: 0
Event size: 40
Position: 4
Next log name: binlog.000001

=== FormatDescriptionEvent ===
Date: 2023-12-29 23:31:34
Log position: 126
Event size: 122
Version: 4
Server version: 8.0.33-debug
Checksum algorithm: 1

=== PreviousGTIDsEvent ===
Date: 2023-12-29 23:31:34
Log position: 157
Event size: 31
Previous GTID Event: 
```

假设表结构如下

```sql
CREATE TABLE t (
  id bigint NOT NULL AUTO_INCREMENT,
  a bigint NOT NULL DEFAULT '0',
  b bigint NOT NULL DEFAULT '0',
  update_at datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  create_at datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (id),
  KEY idx_a_update_at (a,update_at)
) ENGINE=InnoDB
```

---

## insert
```sql
insert into t(a,b) values(0,0);
```
```bash
=== AnonymousGTIDEvent ===
Date: 2023-12-29 23:55:35
Log position: 236
Event size: 79
Commit flag: 0
GTID_NEXT: 00000000-0000-0000-0000-000000000000:0
LAST_COMMITTED: 0
SEQUENCE_NUMBER: 1
Immediate commmit timestamp: 1703854535945727 (2023-12-29T23:55:35.945727+08:00)
Orignal commmit timestamp: 1703854535945727 (2023-12-29T23:55:35.945727+08:00)
Transaction length: 320
Immediate server version: 80033
Orignal server version: 80033

=== QueryEvent ===
Date: 2023-12-29 23:55:35
Log position: 321
Event size: 85
Slave proxy ID: 9398
Execution time: 0
Error code: 0
Schema: tempdb
Query: BEGIN

=== TableMapEvent ===
Date: 2023-12-29 23:55:35
Log position: 376
Event size: 55
TableID: 288
TableID size: 6
Flags: 1
Schema: tempdb
Table: t
Column count: 5
Column type: 
00000000  08 08 08 12 12                                    |.....|
NULL bitmap: 
00000000  00                                                |.|
Signedness bitmap: 
00000000  00                                                |.|
Default charset: []
Column charset: []
Set str value: []
Enum str value: []
Column name: []
Geometry type: []
Primary key: []
Primary key prefix: []
Enum/set default charset: []
Enum/set column charset: []
UnsignedMap: map[int]bool{0:false, 1:false, 2:false}
CollationMap: map[int]uint64(nil)
EnumSetCollationMap: map[int]uint64(nil)
EnumStrValueMap: map[int][]string(nil)
SetStrValueMap: map[int][]string(nil)
GeometryTypeMap: map[int]uint64(nil)
Columns: 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=18   null=no 
  <n/a>  type=18   null=no 

=== WriteRowsEventV2 ===
Date: 2023-12-29 23:55:35
Log position: 446
Event size: 70
TableID: 288
Flags: 1
Column count: 5
Values:
--
0:1000136
1:0
2:0
3:"2023-12-29 23:55:35"
4:"2023-12-29 23:55:35"

=== XIDEvent ===
Date: 2023-12-29 23:55:35
Log position: 477
Event size: 31
XID: 2198252
```

---

## update

```sql
update t set a = 1 , b = 1 where id = 1000136;
```

```bash
=== AnonymousGTIDEvent ===
Date: 2023-12-29 23:30:45
Log position: 556
Event size: 79
Commit flag: 0
GTID_NEXT: 00000000-0000-0000-0000-000000000000:0
LAST_COMMITTED: 1
SEQUENCE_NUMBER: 2
Immediate commmit timestamp: 1703854965644535 (2023-12-29T23:30:45.644535+08:00)
Orignal commmit timestamp: 1703854965644535 (2023-12-29T23:30:45.644535+08:00)
Transaction length: 357
Immediate server version: 80033
Orignal server version: 80033

=== QueryEvent ===
Date: 2023-12-29 23:30:45
Log position: 642
Event size: 86
Slave proxy ID: 9398
Execution time: 0
Error code: 0
Schema: tempdb
Query: BEGIN

=== TableMapEvent ===
Date: 2023-12-29 23:30:45
Log position: 697
Event size: 55
TableID: 288
TableID size: 6
Flags: 1
Schema: tempdb
Table: t
Column count: 5
Column type: 
00000000  08 08 08 12 12                                    |.....|
NULL bitmap: 
00000000  00                                                |.|
Signedness bitmap: 
00000000  00                                                |.|
Default charset: []
Column charset: []
Set str value: []
Enum str value: []
Column name: []
Geometry type: []
Primary key: []
Primary key prefix: []
Enum/set default charset: []
Enum/set column charset: []
UnsignedMap: map[int]bool{0:false, 1:false, 2:false}
CollationMap: map[int]uint64(nil)
EnumSetCollationMap: map[int]uint64(nil)
EnumStrValueMap: map[int][]string(nil)
SetStrValueMap: map[int][]string(nil)
GeometryTypeMap: map[int]uint64(nil)
Columns: 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=18   null=no 
  <n/a>  type=18   null=no 

=== UpdateRowsEventV2 ===
Date: 2023-12-29 23:30:45
Log position: 803
Event size: 106
TableID: 288
Flags: 1
Column count: 5
Values:
--
0:1000136
1:0
2:0
3:"2023-12-29 20:55:35"
4:"2023-12-29 20:55:35"
--
0:1000136
1:1
2:1
3:"2023-12-29 20:55:35"
4:"2023-12-29 20:55:35"

=== XIDEvent ===
Date: 2023-12-29 23:30:45
Log position: 834
Event size: 31
XID: 2198257
```

---


## delete

```sql
delete from t where id = 1000136;
```

```bash
=== AnonymousGTIDEvent ===
Date: 2023-12-29 23:34:12
Log position: 913
Event size: 79
Commit flag: 0
GTID_NEXT: 00000000-0000-0000-0000-000000000000:0
LAST_COMMITTED: 2
SEQUENCE_NUMBER: 3
Immediate commmit timestamp: 1703855652381052 (2023-12-29T23:34:12.381052+08:00)
Orignal commmit timestamp: 1703855652381052 (2023-12-29T23:34:12.381052+08:00)
Transaction length: 312
Immediate server version: 80033
Orignal server version: 80033

=== QueryEvent ===
Date: 2023-12-29 23:34:12
Log position: 990
Event size: 77
Slave proxy ID: 9398
Execution time: 0
Error code: 0
Schema: tempdb
Query: BEGIN

=== TableMapEvent ===
Date: 2023-12-29 23:34:12
Log position: 1045
Event size: 55
TableID: 288
TableID size: 6
Flags: 1
Schema: tempdb
Table: t
Column count: 5
Column type: 
00000000  08 08 08 12 12                                    |.....|
NULL bitmap: 
00000000  00                                                |.|
Signedness bitmap: 
00000000  00                                                |.|
Default charset: []
Column charset: []
Set str value: []
Enum str value: []
Column name: []
Geometry type: []
Primary key: []
Primary key prefix: []
Enum/set default charset: []
Enum/set column charset: []
UnsignedMap: map[int]bool{0:false, 1:false, 2:false}
CollationMap: map[int]uint64(nil)
EnumSetCollationMap: map[int]uint64(nil)
EnumStrValueMap: map[int][]string(nil)
SetStrValueMap: map[int][]string(nil)
GeometryTypeMap: map[int]uint64(nil)
Columns: 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=8    unsigned=no   null=no 
  <n/a>  type=18   null=no 
  <n/a>  type=18   null=no 

=== DeleteRowsEventV2 ===
Date: 2023-12-29 23:34:12
Log position: 1115
Event size: 70
TableID: 288
Flags: 1
Column count: 5
Values:
--
0:1000136
1:1
2:1
3:"2023-12-29 20:55:35"
4:"2023-12-29 20:55:35"

=== XIDEvent ===
Date: 2023-12-29 23:34:12
Log position: 1146
Event size: 31
XID: 2198258
```

----