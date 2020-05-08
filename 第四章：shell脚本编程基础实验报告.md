# 实验报告
## 任务一：用bash编写一个图片批处理脚本
### 修改之后可以正确执行的代码如下：
```
#!/bin/bash
echo "this script is to rename picture"

for names in /lustre1/hw/yingjia/number/1/*
do
    echo "$names"
    news=$i
    echo "$news"
    mv $names /lustre1/hw/yingjia/number/1/$news.png
    let i=i+1
done
```
### for循环实现了图片的批量命名。 
### bash脚本文件
```
#!/bin/bash
for files in `ls`
do
    # 截取文件名的前两个字符
    fname=${files:0:2}
    # 截取文件的后四个字符
    bname=${files:0-4}
    # 拼接成文件名
    filename=$fname$bname
    # 更改文件名
    mv $files $filename
done123456789101112
```
### 注意在执行该脚本文件的时候使用下面的命令来执行:
```
bash 脚本文件名
```
### 或者
```
sudo chmod 777 脚本文件名
./脚本文件名
```
### 还可以自己指定文件的名字(不需要从文件中截取)Bad substitution如下例子:
```
#!/bin/bash
cd 上册
name=0
for files in `ls`
do
    # 指定后缀名
    hname=".mp4"
    # 指定文件名(这里采用加1的方式)
    name=$(echo "$name + 1"|bc)
    # 拼接成完整文件名
    filename=$name$hname 
    # 修改文件名
    mv $files $filename
done
```