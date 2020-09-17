xargs
```
 find /proc | xargs

 find /proc -print0 | xargs -0

 find . -name '*.gz' | xargs rm
 
 find . -name '*.gz' -print0 | xargs -0 gunzip

cat list.txt | xargs command # 适用于向同一个程序传递不同的参数，-P参数可以控制最大进程数

```

parallel
```
-k 保持输出有序
--eta 显示进度
GNU parallel 替换 {} 参数为标准输入中的下一行数据。
输入占位符

```


```
find . -type f -name "*.log" | parallel
```
Just like using -exec with find, parallel substitutes each member in the input list as {}. Here, parallel will gzip every file that find outputs:
```
find . -type f -name "*.log" | parallel gzip {}
```

```
用法1: parallel command1 command2 command3 # 类似&，但可以用-j等参数
用法2: cat file | parallel -j 8 --block 10M --pipe command #把file每隔10M行拆分，传递给—pipe指定的command; -j job的数量，默认为CPU个数
```


---
```
ls *sh |tr " " "\n" |parallel bash {} -j 9 --eta
```



[toc]
## 安装
```
(wget -O - pi.dk/3 || curl pi.dk/3/) | bash
```

## 入门
最简单的方法就是类比xargs。在xargs里面有一个参数 -P，可以利用多核。
举个例子：
```
$ time echo {1..5} |xargs -n 1  sleep

real    0m15.005s
user    0m0.000s
sys     0m0.000s
```
这一条xargs把每个echo的数作为参数传给sleep ，所以一共sleep了 1+2+3+4+5=15秒。
如果使用 -P 参数分给5个核，每个核各sleep 1,2,3,4,5秒，所以执行完之后总共sleep的5秒。
```
$ time echo {1..5} |xargs -n 1 -P 5 sleep

real    0m5.003s
user    0m0.000s
sys     0m0.000s
```
铺垫结束。一般情况下，parallel的第一种模式，就是替换掉 xargs -P.
比如压缩一下所有的html文件。
```
find . -name '*.html' | parallel gzip --best
```

## 
### 传递参数
```
seq 5 | parallel echo pre_placehoder_{}
pre_placehoder_1
pre_placehoder_2
pre_placehoder_3
pre_placehoder_4
pre_placehoder_5
```

### 改写 for loop
```
(for x in `cat list` ; do
    do_something $x
  done) | process_output
  
====>  

 cat list | parallel do_something | process_output

```

### --pipe模式
这时管道前面的不是作为参数，而是标准输入传给后面的命令
例如：
 cat my_large_log   |parallel --pipe grep pattern 

如果不加 --pipe ，相当于 mylog中的每一行都变成 grep pattern line的命令展开了。而加入了--pipe，则和 cat mylog | grep pattern  没有区别，只是分配到各个核上去执行了。

#### Ref_Info
：https://www.jianshu.com/p/c5a2369fa613