多进程可以有效利用服务器多核CPU的计算资源，加速运行效率，在python中，通过内置模块multiprocessing来进行多进程编程。
子进程通过Process类来设置，示例如下
```
from  multiprocessing import Process
import subprocess
import shlex

def cal_seqs(fq):
    print('calculate fastq sequences')
    cnt = 0
    with open(fq) as f:
        for line in f:
            cnt += 1
    print('fastq sequences : {}'.format(cnt / 4))

def fastqc(fq):
    cmd = 'fastqc -t 10 {}'.format(fq)
    cmd_args = shlex.split(cmd)
    p = subprocess.Popen(cmd_args, stdout = subprocess.PIPE)
    p.stdout.read()

if __name__ == '__main__':
    fq = 'test.fq'
    p = Process(target = fastqc, args = (fq, ))
    p.start()
    cal_seqs(fq)
    print('Finish calculate fastq sequences')
```

上述代码**启动了一个子进程**来运行fastqc, 主进程则执行计算fastq文件的序列数，运行上述代码，你会看到类似如下的输出
```
calculate fastq sequences
fastq sequences : 100000
Finish calculate fastq sequences
Started analysis of test.fq
Approx 5% complete for test.fq
Approx 10% complete for test.fq
Approx 15% complete for test.fq
Approx 20% complete for test.fq
```
主进程中的行数计算先执行完毕，子进程后执行完毕， 说明子进程是非阻塞的。在主进程中启动了子进程后，主进程不管子进程是否运行结束，接着往下执行计算行数的结果，这就是非阻塞。如果需要等子进程执行完毕后，主进程再直接执行，也就是阻塞式的运行，需要join函数来进行阻塞，上述代码修改如下
```
from  multiprocessing import Process
import subprocess
import shlex

def cal_seqs(fq):
    print('calculate fastq sequences')
    cnt = 0
    with open(fq) as f:
        for line in f:
            cnt += 1
    print('fastq sequences : {}'.format(cnt / 4))

def fastqc(fq):
    cmd = 'fastqc -t 10 {}'.format(fq)
    cmd_args = shlex.split(cmd)
    p = subprocess.Popen(cmd_args, stdout = subprocess.PIPE)
    p.stdout.read()

if __name__ == '__main__':
    fq = 'test.fq'
    p = Process(target = fastqc, args = (fq, ))
    p.start()
    p.join()
    cal_seqs(fq)
    print('Finish calculate fastq sequences')
```
再次运行，可以看到如下输出
```
Started analysis of test.fq
Approx 5% complete for test.fq
Approx 10% complete for test.fq
...
Approx 95% complete for test.fq
Approx 100% complete for test.fq
calculate fastq sequences
fastq sequences : 100000
Finish calculate fastq sequences
```
join的作用就是阻塞子程序，等待子进程执行完毕之后，主进程才可以接着往下执行。如果只是需要执行某些程序，而且下文中也不依赖其结果，可以选择非阻塞式的运行，如果下文需要依赖其结果，就需要阻塞式的运行了，应该根据实际情况，灵活进行选择。

对于多个样本的重复处理，可以用多进程达到并行的目的，代码示例如下
```
process_list = []
samples = ['control1', 'control2', 'control3', 'case1', 'case2', 'case3']
for sample in sample:
    fq = '{}.fq'.format(sample)
    p = Process(target = fastqc, args = (sample, ))
    p.start()
    process_list.append(p)

# 根据需要选择是否阻塞
for p in process_list:
    p.join()
上述代码有一个问题，会一次性将所有的样本都并行，在实际开发中，我们需要结合计算资源来控制最大的并行数，此时可以使用进程池Pool类，可以方便的指定最大进程数，示例如下

p = Pool(3)
process_list = []
samples = ['control1', 'control2', 'control3', 'case1', 'case2', 'case3']
for sample in sample:
    fq = '{}.fq'.format(sample)
    p.append_async(target = fastqc, args = (sample, ))

p.close()
p.join()
```
上述代码还可以用map方法进行改写，更加简便，改写之后的完整代码如下
```
from multiprocessing import Pool
import subprocess
import shlex

def fastqc(fq):
    cmd = 'fastqc -t 10 {}'.format(fq)
    cmd_args = shlex.split(cmd)
    p = subprocess.Popen(cmd_args, stdout = subprocess.PIPE)
    p.stdout.read()

if __name__ == '__main__':
    samples = ['control1.fq', 'control2.fq', 'control3.fq', 'case1.fq', 'case2.fq', 'case3.fq']
    with Pool(3) as p:
        samples = ['control1.fq', 'control2.fq', 'control3.fq', 'case1.fq', 'case2.fq', 'case3.fq']
        p.map(fastqc, samples)
    print('Finish')
```
以上就是python多进程的基本用法，除此以外，python还支持进程间通信以及共享变量，更高级的用法请查看官方的API文档。