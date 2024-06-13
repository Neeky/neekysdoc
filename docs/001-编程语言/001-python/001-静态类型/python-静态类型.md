## 问题
我写的代码主要是偏量化交易，这行啊对数值的准确性要求是非常的高，毕竟交易的都是真金白银。但是计算机有时候“不靠谱”，比如 0.2 * 100 用眼睛看一下都知道结果是 20 ； 用计算机来算结果就不一定了，先算个 19.99xxx 的给大家看下。

```python
#!/usr/bin/env python3


def my_sum(a, b):
    return a + b


def main():
    total = 0
    for i in range(100):
        j = 0.1
        k = 0.1
        total = total + my_sum(j, k)

    print("total = {}".format(total))


if __name__ == "__main__":
    main()
```
运行结果
```bash
python3 main.py
total = 19.99999999999996
```

再算一个 20.00xxx 给大家看一下。
```python
#!/usr/bin/env python3


def my_sum(a, b):
    return a + b


def main():
    total = 0
    for i in range(100):
        j = 0.1
        k = 0.1
        # total = total + my_sum(j, k)
        total = total + j + k

    print("total = {}".format(total))


if __name__ == "__main__":
    main()
```
运行结果

```bash
python3 main.py
total = 20.000000000000014
```

---

## 解决办法

就问题的根本原因来说，就是计算机没有办法精确的表示 0.1 这样的数值，详细的说明可以见 IEEE754 标准。解决问题的办法也非常简单就不用浮点数，改成全部用整数表现。 比如 1.234￥ 我们程序中用整数记成 12340 也就是说程序中的每一个 1 表示的是 1/10000 元。

有了解决方案，问题就解决了一半，剩下的就是要落实了；就其它静态类型的语言来说这个比较好办，只需要把数据类型声明为 bigint 类型就行了，编译时就能检查出问题。 对于 Python 的话我们要加一些类型提示(注解)，然后再用专门的静态分析工具去检查，我们的使用方式与类型提示是否一致。

下面就来实操一下。

---


## 第一步安装类型检查工具

为了做静默类型检查，我们需要安装另一个官方提供的工具 mypy 。
```bash
pip3 install mypy
```

---

## 第二步给代码增加类型提示

给我们的代码加上类型提示 。
```python
#!/usr/bin/env python3


def my_sum(a: int, b: int) -> int:
    return a + b


def main() -> None:
    total = 0
    for i in range(100):
        j = 0.1
        k = 0.1
        total = total + my_sum(j, k)

    print("total = {}".format(total))


if __name__ == "__main__":
    main()

```
对代码进行静态类型检查
```bash
mypy --strict main.py 
main.py:13: error: Argument 1 to "my_sum" has incompatible type "float"; expected "int"  [arg-type]
main.py:13: error: Argument 2 to "my_sum" has incompatible type "float"; expected "int"  [arg-type]
Found 2 errors in 1 file (checked 1 source file)
```
可以看到 mypy 检查到类型不兼容的问题了，下面我们把代码改正确。

---

## 第三步修复类型检查的问题
按 1/10000 的精度把我们的代码改正确。
```python
#!/usr/bin/env python3


def my_sum(a: int, b: int) -> int:
    return a + b


def main() -> None:
    total = 0
    for i in range(100):
        # 如果我们用 1/10000 精度，那么 0.1 就应该写成 1000
        j = 1000
        k = 1000
        total = total + my_sum(j, k)

    # 输出的时候要格式化一下这样对人更加友好
    print("total = {0} ￥".format(total / 10000))


if __name__ == "__main__":
    main()
```

运行类型检查工具。
```bash
mypy --strict main.py
Success: no issues found in 1 source file
```

运行程序。
```bash
python3 main.py 
total = 20.0 ￥
```

---


## 最后

事实上我们在真正的开发上并不会，每次都会去运行程序做检查的，vscode 上有方便的插件可以用；不过这是后话了下次再说。

---

