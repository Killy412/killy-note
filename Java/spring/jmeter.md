# jmeter压测

我这里执行的命令为：

```
jmeter -n -t testplan/RedisLock.jmx -l testplan/result/result.txt -e -o testplan/webreport
```

说明：

- testplan/RedisLock.jmx 为测试计划文件路径
- testplan/result/result.txt    为测试结果文件路径
- testplan/webreport   为web报告保存路径。