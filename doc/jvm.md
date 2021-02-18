### jinfo 查看jvm 参数
    jinfo -flags 进程id

### jstack 查看栈信息
    jstack -l 2454 >> post_loan_back.txt

### jstat jvm运行状态信息
    jstat -gcutil 进程id 输出间隔时间

### jmap 生产堆快照
    jmap -histo 1953 | head -20

#### cup 高
    top -Hp pid 
    查看该进程下所有线程

