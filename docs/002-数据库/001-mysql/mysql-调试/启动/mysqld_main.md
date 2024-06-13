# mysqld 的启动流程
mysqld 是一个 c++ 程序，入口函数当时是 main，而这个 main 呢，就是直接调用 mysqld_main 函数。

```c++
// sql/main.cc 文件
extern int mysqld_main(int argc, char **argv);

int main(int argc, char **argv) { return mysqld_main(argc, argv); }
```

---

## mysqld_main
```c++
// sql/mysqld_main.cc

static Connection_acceptor<Mysqld_socket_listener> *mysqld_socket_acceptor = nullptr;

static bool network_init(void) {
  mysqld_socket_acceptor = new (std::nothrow)
  if(mysqld_socket_acceptor->init_connection_acceptor()) {
    ...
  }
}

int mysqld_main(int argc, char **argv)
{
  // 计算一下堆栈的生长方向，linux 操作系统的话向下长； 影响的话就是会把 stack_direction 设置成 -1 。
  initialize_stack_direction();

  // 这里是一堆的初始化操作
  // 命令行参数解析、初始化网络和套接字、初始化引擎、初始化查询系统、初始化日志、初始化权限
  // 初始化复制、初始化插件、启动tcp服务器
  ...

  // 进入事件循环
  mysqld_socket_acceptor->connection_event_loop();

  // 执行到这里说明是要退出了
  // join 前面创建的线程
  
  clean_up(true);
  mysqld_exit(signal_hand_thr_exit_code);
}
```

---

## connection_event_loop
connection_event_loop 函数的实例如下

```c++
// sql/sql/conn_handler/connection_acceptor.h

  void connection_event_loop() {
    Connection_handler_manager *mgr =
        Connection_handler_manager::get_instance();
    while (!connection_events_loop_aborted()) {
      Channel_info *channel_info = m_listener->listen_for_connection_event();
      
      // 也就是说，一个新的连接真正开始执行是从 process_new_connection 开始的
      if (channel_info != nullptr) mgr->process_new_connection(channel_info);
    }
  }
```

---

## Channel_info
从上面的代码可以看到在收到连后，新的连接会被包装成一个 Channel_info 对象，下面我们来看一下这个对象里面有些什么属性。
```c++

Channel_info *Mysqld_socket_listener::listen_for_connection_event() {
  // 连接时的一些其它的处理与检查
  // ...

  // 可以看到 Channel_info 就是对 socket 对象的包装
  Channel_info *channel_info = nullptr;
  if (listen_socket->m_socket_type == Socket_type::UNIX_SOCKET)
    channel_info = new (std::nothrow) Channel_info_local_socket(connect_sock);
  else
    channel_info = new (std::nothrow) Channel_info_tcpip_socket(
        connect_sock, (listen_socket->m_socket_interface ==
                       Socket_interface_type::ADMIN_INTERFACE));
  if (channel_info == nullptr) {
    (void)mysql_socket_shutdown(connect_sock, SHUT_RDWR);
    (void)mysql_socket_close(connect_sock);
    connection_errors_internal++;
    return nullptr;
  }

  return channel_info;

}
```

---

## Connection_handler_manager
```c++
void Connection_handler_manager::process_new_connection(
    Channel_info *channel_info) {
  if (connection_events_loop_aborted() ||
      !check_and_incr_conn_count(channel_info->is_admin_connection())) {
    channel_info->send_error_and_close_channel(ER_CON_COUNT_ERROR, 0, true);
    delete channel_info;
    return;
  }

  if (m_connection_handler->add_connection(channel_info)) {
    inc_aborted_connects();
    delete channel_info;
  }
}
```
