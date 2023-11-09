# 追踪 redis

默认情况下编译的 redis 优化级别到了 O3 ，这个级别是不会保留符号表的， 对于学习和复现问题不太方便。好在编译一个 Debug 版本的 redis 也不会花太多的成本。

---

## 编译-debug-版本
在 make 的时候传入相应的 flag 就能编译出 Debug 版本的 redis 了
```bash
tar -xvf redis-7.0.11.tar.gz
cd redis-7.0.11

# 注意 make 后面的参数
make CFLAGS="-g -O0"

mkdir -p /usr/local/redis-7.0.11-debug/bin/
mv src/redis-cli /usr/local/redis-7.0.11-debug/
mv src/redis-server /usr/local/redis-7.0.11-debug/
mv src/redis-benchmark /usr/local/redis-7.0.11-debug/

cd /usr/local/
ln -s redis-7.0.11-debug redis
```

---