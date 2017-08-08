#pyspark map,reduce接收参数

查看spark官方档，发现map, reduce的方法都指定只能代如固定一个参数（map）或者两个参数（reduce）

现在工作发现，想要多把代入参数到map,和reduce的方法里面.

查了下，发现以下方法。

先定义一个函数：

```
def func(a, extra_para):
    k =  a + extra_para
    return k
```

然后map 的时候：

```
rdd = rd.map(lambda row:func(1,2))
```

这样就能代入另外的参数到map, reduce中

参考url:
http://stackoverflow.com/questions/34087578/how-to-pass-additional-parameters-to-user-defined-methods-in-pyspark-for-filter