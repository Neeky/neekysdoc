# open
c/c++ 语言中的 open 函数内核提供的打开文件的系统调用函数，其它语言的 open 变种函数应该都是在这一函数上的封装。它定义在 **fcntl.h** 这个头文件中，原型如下：
```c
int open (const char *__path, int __oflag, ...)
``` 
如果打开文件失败就返回 **-1** , 成功就返回对应的文件描述符。

---

## 参数说明

**__path** : 要打开文件的路径(可以是相对路径，也可以是绝对路径)

**__oflag** : 这个参数大有讲究，高级点的用法都藏在这里，我们看一下它接受哪些值

```
O_RDONLY:
    只读方式打开文件

O_WRONLY:
    只写方式打开文件

O_RDWR:
    读+写的方式打开文件

O_APPEND:
    每次写的时候追加到文件尾

O_CREAT:
    文件不存在的时候就创建

O_EXCL:
    可以用于测试一个文件是否存在，如果不存在，则创建文件；如果同时指定了 O_CREAT 并且文件存在，就报错

O_NONBLOCK:
    设置为非阻塞 IO

O_SYNC:
    每次执行 write 都要等待物理 IO 操作的完成，也就是说数据要写完，并且文件属性也要更新完

O_DSYNC:
    每次执行 write 都要等待物理 IO 操作的完成，但是如果这次写入不影响读取刚写入的数据，就不等待文件属性更新

```

---

## 代码实践
用 open 打开文件，如果文件不存在就创建后再打开，存在就直接打开。
```cpp
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>

using namespace std;

int main(int argc, char **argv)
{
    // 文件不存在就创建后再打开，如果存在就直接打开
    // 如果要创建文件，那么把文件的权限设置为 644
    int fd = -1;
    if ((fd = open("/tmp/test.log", O_RDWR | O_CREAT, 0644)) == -1)
    {
        printf("open file /tmp/test.log fail \n");
        return 1;
    }
    else
    {
        printf("open file /tmp/test.log sucess, file-no: %d \n", fd);
    }

    // 关闭文件
    close(fd);
    return 0;
}
```
运行效果
```
/tmp/cpps/build/cpps
open file /tmp/test.log sucess, file-no: 3 

```
---

## 技巧
由于 0, 1, 2 这三个文件描述符会分别对应到 标准输入，标准输出，标准错误输出；所以当我打开文件是最小能用的自然数，已经是 3 了，所以 open 的返回值会是 3 。也就是说在我们不close(3) 的情况下，再去打开一个新文件，这个时候 open 的返回值就会是 4 。

有时候我们为了把标准错误输出到日志文件，上来就是一个 close(2), 这个时候直接打开日志文件就能起到把标准错误重定向到日志文件的目的。

```c++
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>
#include <iostream>

using namespace std;

int main(int argc, char **argv)
{
    // 文件不存在就创建后再打开，如果存在就直接打开
    // 如果要创建文件，那么把文件的权限设置为 644

    // 先闭关 stderr
    close(2);

    // 打开日志文件，这个时候 open 返回的 fd 应该是 2
    // 而 2 又默认关联到 stderr
    int fd = -1;
    if ((fd = open("/tmp/test.log", O_RDWR | O_CREAT | O_APPEND, 0644)) == -1)
    {
        cerr << "open log file fail ." << endl;
        return 1;
    }
    else
    {
        cerr << "open file /tmp/test.log sucess, file-no: " << fd << endl;
    }

    // 关闭文件
    close(fd);
    return 0;
}
```
运行效果
```bash 
// 可以看到这个并不会输出，原因是 stderr 已经打到 /tmp/test.log 文件里面了
[root@git-sqlpy-com build]# /tmp/cpps/build/cpps
[root@git-sqlpy-com build]#
[root@git-sqlpy-com build]#
[root@git-sqlpy-com build]# cat /tmp/test.log
open file /tmp/test.log sucess, file-no: 2
```

---