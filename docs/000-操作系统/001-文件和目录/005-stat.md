# stat
一个文件不但有数据，还有属性。如所属用户、组、文件大小、创建时间等等，这些都属于属性。在 c 语言中用 **struct stat** 这个结构来表示他们。
```c
// sys/stat.h

struct stat {
    __nlink_t st_nlink;		        /* Link count.  */
    __mode_t st_mode;		        /* File mode.  */
    __uid_t st_uid;		            /* User ID of the file's owner.	*/
    __gid_t st_gid;		            /* Group ID of the file's group.*/
    __off_t st_size;			    /* Size of file, in bytes.  */
    struct timespec st_atim;		/* Time of last access.  */
    struct timespec st_mtim;		/* Time of last modification.  */
    struct timespec st_ctim;		/* Time of last status change.  */

    // 还有其它内容，后面比较长就不写了
}

```

另外 **sys/stat.h** 中还声明了一个叫 stat 的函数
```c++
// sys/stat.h

int stat (const char *__restrict __file,struct stat *__restrict __buf)

// 成功返回 0 ，报错的时候返回 -1
```

我平时用的比较多的就是 st_size 这个字面了。



---

## 准备环境
**/tmp/test.log** 只包含一行数据，`hello\n` 共 6 个字节。
```python
In [1]: f = open("/tmp/test.log")

In [2]: lines = [_ for _ in f]

In [3]: lines
Out[3]: ['hello\n']
```
用 C 语言把 /tmp/test.log 文件的大小读出来
```c++
#include <fcntl.h>
#include <stdio.h>
#include <unistd.h>
#include <iostream>
#include <sys/stat.h>

using namespace std;

int main(int argc, char **argv)
{
    //
    struct stat buf;
    int err = stat("/tmp/test.log", &buf);
    if (err == -1)
    {
        perror("stat");
        return 1;
    }
    cout << "file size of /tmp/test.log size = " << buf.st_size << " byte(s) " << endl;

    return 0;
}
```
运行效果
```bash
/tmp/cpps/build/cpps
file size of /tmp/test.log size = 6 byte(s)
```
---


