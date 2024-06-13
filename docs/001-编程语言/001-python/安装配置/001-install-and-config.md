# 安装Python环境
相比官方提供的安装包来讲，自己编译安装 Python 环境，自定义程度上会更高一些。一来我想安装在哪里就安装在哪里； 二就是我会开启一些编译连接时的代码选项，这样可以让 Python 运行时更快一些。


---

## 下载源代码
```bash
cd /tmp/
wget https://www.python.org/ftp/python/3.12.0/Python-3.12.0.tar.xz

```

---

## 安装依赖
```bash
yum -y install tar net-tools gcc zlib-devel openssl-devel libffi-devel xz-devel
```

---


## 编译安装
我在编译时打开 `--enable-optimizations` 这样虽然在编译安装的时候耗时久一点，但是 Python 在经过优化之后会运行的更快一些。
```bash

tar -xvf Python-3.12.0.tar.xz 
cd Python-3.12.0

./configure --prefix=/usr/local/python-3.12.0 --enable-optimizations && make && make install

cd /usr/local/
ln -s python-3.12.0 python

```

---

## 导出环境变量
导出环境变量
```bash
echo 'export PATH=/usr/local/python/bin/:$PATH' >> /etc/profile
source /etc/profile
```

---

## 配置软件包的源
为了让下载其它软件包更快一些，这里直接使用阿里云的软件源。
```bash
pip config set global.index-url http://mirrors.aliyun.com/pypi/simple/
```

---