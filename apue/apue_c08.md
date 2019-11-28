
## 第八章 进程控制

### 学习记录

fork 函数也没有那么神秘, 可以直接调用, 本来就是嘛, 那么 aws 管的那一套. 

### 总结

### 习题与问题
1. 在图8-3程序中, 如果用exit调用代替_exit 调用, 那么可能会使标准输出关闭, 使printf 返回-1. 修改该程序以验证在你所使用的系统上是否会产生此种结果. 如果并非如此, 你怎样处理才能得到类似的结果呢? 

2. 回忆图7-6中典型的存储空间布局. 由于对应于每个函数调用的栈帧通常存储在栈中, 并且由于调用vfork后, 子进程运行在父进程的地址空间中,如果不是在main函数中而是在另一个函数中调用vfork, 此后子进程又从该函数返回, 将会发生什么? 请编写一段测试程序对此进行验证, 并且画图说明发生了什么? 

3. 重写8-6中的程序, 把wait 换成waiteid. 不调用pr_exit, 而从siginfo 结构中确定等价的信息. 

4. 当用$./a.out执行图8-13中的程序一次时, 其输出是正确的. 但是若将该程序按下列方式执行多次, 则其输出不正确. 
```shell
$ ./a.out; a.out; ./a.out
output from parent
ooutput from parent
ouotuptut from child
put from parent
output from child
utput form child 
```
原因是什么? 怎样才能更正此类错误? 如果使子进程首先输出, 还会发生此问题吗?

5. 在图8-20所示的程序中, 调用execl, 指定pathname为解释器文件. 如果将其改为调用execlp, 指定testinterp 的filename, 并且如果目录/home/sar/bin 是路径前缀, 则运行该程序时, argv[2] 的打印输出是什么?

6. 编写一段程序创建一个僵死进程, 然后调用system执行ps(1)命令以验证该进程是僵死进程.

7. 8.10 节中提及POSIX.1 要求在exec时关闭打开目录流. 按下列方法对此进行验证: 对根目录调用opendir, 查看在你系统上实现的DIR结构, 然后打印执行时关闭标志. 接着打开同一目录读并执行时关闭标志. 