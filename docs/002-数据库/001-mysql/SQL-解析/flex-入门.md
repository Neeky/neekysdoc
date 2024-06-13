## 背景
假设要写一下类似于 wc 这样一个用于统计单词和行数的小工具。

---

## 定义规则
定义好词法规则，我们在这里只统计“单词”，“行”， “字符”，保存到文件 `wc.l` 中。
```flex
%{
    int chars = 0;
    int words = 0;
    int lines = 0;
%}

%%
[a-zA-Z]+   {words++; chars += strlen(yytext);}
\n          {chars++; lines++;}
.           {chars++;}
%%

int main(int argc, char ** argv) 
{
    yylex();
    printf("lines = %d    words = %d    chars = %d \n", lines, words, chars);
}
```

---

## 根据规则生成 c/c++ 语言程序
```bash
/usr/local/flex/bin/flex wc.l 

# 它会生成   lex.yy.c 文件
ll lex.yy.c 
-rw-r--r-- 1 root root 44367 Apr 21 23:57 lex.yy.c

```

---

## 编译 c/c++ 程序生成可执行文件
```bash
gcc -o my-wc -L /usr/local/flex/lib/ -lfl lex.yy.c 

# 它会生成 my-wc 这个可执行文件
ll my-wc
-rwxr-xr-x 1 root root 33120 Apr 21 23:59 my-wc
```

---

## 检查
1、建一个用于测试的数据文件
```bash
cat /tmp/data.txt 
hello
world
json
```
比较两个版本的 wc 程序的运行结果
```bash
cat /tmp/data.txt | ./my-wc 
lines = 3    words = 3    chars = 17 


cat /tmp/data.txt | wc
      3       3      17
```

---