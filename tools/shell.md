# shell



## 1. shell script

一个脚本涉及到数组、判断、循环

```shell
#!/bin/bash

# 数组
nums[0]=0
nums[1]=1
nums[2]=3

for i in ${nums[*]}
do
  echo $i
done

# 循环+判断
files=`ls`
for file in ${files[*]}
do
  if [ -f $file ]; then
    echo $file is a file
  else
    echo $file is not a file
  fi
done

# 输入
read -p "please enter your name:" name
echo "my name is $name"


```



## 2. grep

几个常用参数

```
-i, --ignore-case
             Perform case insensitive matching.  By default, grep is case sensitive.

-n, --line-number
             Each output line is preceded by its relative line number in the file, starting at line 1.  The line number counter is reset for each file processed.  This option is ignored if -c, -L, -l, or -q is specified.

-o, --only-matching
             Prints only the matching part of the lines.
             
-v, --invert-match
             Selected lines are those not matching any of the specified patterns.	
```

grep与正则表达式结合使用



## 3. 正则表达式

| RE字符                               | 说明                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| ^word                                | word在行首                                                   |
| word$                                | word在行尾                                                   |
| .                                    | 一定有一个任意字符                                           |
| *                                    | `>=0`个前一个字符                                            |
| [list]                               | 字符集合，如[ab]                                             |
| [n1-n2]                              | 字符集合，如[0-9A-Z]                                         |
| [^list]                              | 字符集合取反                                                 |
| `\{n,m\}`<br />`\{n\}`<br />`\{n,\}` | 连续n到m个前一个字符<br />连续n个前一个字符<br />连续n个以上前一个字符 |
| +                                    | 重复一个或一个以上前一个字符                                 |
| ?                                    | 零个或一个前一个字符                                         |
| \|                                   | 或，如 `a|b`                                                 |
| ()                                   | 群组，如 g(oo\|la)d                                          |
| ()+                                  | 一个或多个群组                                               |



## 3. sed

对数据**行**进行增删改查操作

几个常用参数

```shell
-i extension
   			Edit files in-place, saving backups with the specified extension.  If a zero-length extension is given, no backup will be saved.	It is not recommended to give a zero-length extension when in-place editing files, as you risk corruption or partial content in situations where disk space is exhausted, etc.

-E      Interpret regular expressions as extended (modern) regular expressions rather than basic regular expressions (BRE's).
```



Sed Functions

| 命令 | 说明 | 示例                                                         |
| ---- | ---- | ------------------------------------------------------------ |
| a    | 新增 |                                                              |
| i    | 插入 |                                                              |
| d    | 删除 | sed '/^$/d' 删除空白行<br />sed '/deleteworld/d'             |
| s    | 替换 | sed -i 's/old/new/g' test.txt<br />sed -i 's/^.//g' 删除行首第一个字符<br />sed -i 's/.$//g' 删除行尾最后一个字符<br />sed -i 's/^/START ' 在行首添加字符<br />sed -i 's/$/ END' 在行尾添加字符 |



## 4. awk

```shell
cat test.txt | awk -F "," '{printf "line %s\n", $2}'
```



## 5. vim

| 命令                  | 说明                             |
| --------------------- | -------------------------------- |
| :set nu               | 显示行号                         |
| G                     | 移动到最后一行                   |
| nG                    | 移动到第n行，可配合`:set nu`指令 |
| gg                    | 移动到第一行                     |
| /world                | 向下寻找字符串                   |
| ?world                | 向上寻找字符串                   |
| n/N                   | 下一个/反向下一个                |
| :n1,n2s/word1/word2/g | 字符串替换                       |
| 1,$s/word1/word2/g    | 全文字符串替换                   |

