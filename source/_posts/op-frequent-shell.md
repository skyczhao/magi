title: 运维常用脚本
date: 2016-12-23 09:54:45
desc:
tags:
---

命令就在那，看自己组合使用。所以简单有简单的用法，复杂有复杂的用法。

awk
-----
> 如果非要选出个神器我觉得就它了吧

```
# 分割输出
jps -m | grep Scheduler | awk '{print $1}'

# 分割csv
## 条件判断
cat field.txt | awk '{print "IF(ti.`"$1"` IS NULL, t.`"$1"`, ti.`"$1"`) AS `"$1"`,"}' 
## 指定分隔符
head t_weixin_account.csv | awk -F',' '{print $5"|"$6"|"$8"|"$10}'
```

<!--more-->

xargs
-----
```
find . -name *kafka*.jar | xargs -I {} ls -l {}
```

grep
-----
```
# 文件内容搜索
grep -rni join .
```

iconv
-----
```
cat Workbook1.csv | iconv -f gbk -t utf-8
```

for
-----
```
# 一般用法
for i in {1..5}
do
   echo "Welcome $i times"
done

# 指定间隔
for i in {0..10..2}
do 
     echo "Welcome $i times"
done

# 结合seq
for i in $(seq 1 2 20)
do
   echo "Welcome $i times"
done
```