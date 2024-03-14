##### **编写个shell脚本将当前目录下大于10K的文件转移到/tmp目录下**

```shell
for fileName in `ls -l |awk '$5>10240 {print $9}'`
do
mv $fileName /tmp
done
ls -la /tmp
echo 'move done'
```

##### 脚本计算1到100
#!/bin/bash sum=0 for ((i=1; i<=100; i++)) do sum=$((sum + i)) done echo "1到100的和是：$sum"
```shell
#!bin/bash
sum = 0
for((i=1;i<=100;i++))
do
sum=$((sum+i))
done
echo "结果为:${}"
```