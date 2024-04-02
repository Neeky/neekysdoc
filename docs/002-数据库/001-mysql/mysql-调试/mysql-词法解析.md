# MySQL 词法解析流程

从功能上看是 lex_one_token 负责从输入流中一个一个的读取字符，整体来看就一个状态机。函数的堆栈如下

![](../images/mysql-insert.svg?x=1109.2&y=661&s=token)

---

## 完整的堆栈信息

```bpftrace 
#!/usr/bin/env bpftrace

BEGIN 
{
    print("词法解析 lex_one_token 的堆栈信息 \n")
}

uprobe:/usr/local/mysql/bin/mysqld:_ZL13lex_one_tokenP13Lexer_yystypeP3THD
{
    printf("%s\n", ustack(perf)); 
    print("exit \n");
}

END
{
    print("exit \n")
}
```

执行效果如下

```
bpftrace trace.bt 
Attaching 3 probes...
词法解析 lex_one_token 的堆栈信息 


	335b64d lex_one_token(Lexer_yystype*, THD*)+0 (/usr/local/mysql-8.0.33/bin/mysqld)
	35e9354 MYSQLparse(THD*, Parse_tree_root**)+933 (/usr/local/mysql-8.0.33/bin/mysqld)
	32c5cff THD::sql_parser()+39 (/usr/local/mysql-8.0.33/bin/mysqld)
	33b6a0a parse_sql(THD*, Parser_state*, Object_creation_ctx*)+667 (/usr/local/mysql-8.0.33/bin/mysqld)
	33b1e25 dispatch_sql_command(THD*, Parser_state*)+418 (/usr/local/mysql-8.0.33/bin/mysqld)
	33a8191 dispatch_command(THD*, COM_DATA const*, enum_server_command)+5868 (/usr/local/mysql-8.0.33/bin/mysqld)
	33a60de do_command(THD*)+1469 (/usr/local/mysql-8.0.33/bin/mysqld)
	35c5f85 handle_connection+479 (/usr/local/mysql-8.0.33/bin/mysqld)
	5222f5d pfs_spawn_thread+336 (/usr/local/mysql-8.0.33/bin/mysqld)
	7f694c89fd92 start_thread+722 (/usr/lib64/libc.so.6)
```

## 词法解析的流程

从堆栈中我们可以看到是 dispatch_sql_command 函数间接调用的 lex_one_token 来完成词法解析。但是要解析的 sql 语句是 dispatch_command 函数调用 THD::set_query 函数设置到 THD 对象上的(设置到了 m_query_string 上)

详细的堆栈看这里 

![](../images/mysql-insert.svg?x=66.6&y=741&s=token)

THD::set_query 函数的完整实现

```c++
void THD::set_query(LEX_CSTRING query_arg) {
  assert(this == current_thd);
  mysql_mutex_lock(&LOCK_thd_query);
  m_query_string = query_arg;
  mysql_mutex_unlock(&LOCK_thd_query);
}

// THD 类定义的关键部分
class THD : public MDL_context_owner,
            public Query_arena,
            public Open_tables_state 
{

	// 这个对象后面会用来，保存 sql 语句
    LEX_CSTRING m_query_string;
}
```

lex_one_token 函数的原型如下，

```c++
static int lex_one_token(Lexer_yystype *yylval, THD *thd) {
	// 太长了大致来讲就一个状态机
}
```

---

## 取出 SQL 语句
既然 lex_one_token 是用于 sql 解析的，我打算用 gdb 在它身上加个断点，打印 m_query_string 看是不是我发走的 查询。

1：发起一条 sql 语句
```sql
mysql> select user, host from mysql.user;
```

2：打断点，并打印 sql 语句 
```bash
(gdb) b lex_one_token
Breakpoint 1 at 0x335b669: file /data/repos/mysql-8.0.33/sql/sql_lex.cc, line 1370.
(gdb) n
Single stepping until exit from function poll,
which has no line number information.
[Switching to Thread 0x7f69041f0640 (LWP 4102459)]

Thread 40 "connection" hit Breakpoint 1, lex_one_token (yylval=0x7f69041eccf0, thd=0x7f69200fe780) at /data/repos/mysql-8.0.33/sql/sql_lex.cc:1370
1370	  uchar c = 0;

(gdb) p thd->m_query_string
$1 = {str = 0x7f692008cdf0 "select user, host from mysql.user", length = 33}
```

和预期的一样我们可以把 sql 语句打印出来。

---

