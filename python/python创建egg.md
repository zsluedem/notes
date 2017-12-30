# python创建egg

最近需要使用Spark 运算数据，我是个Pythoner，自然用的是pyspark啦！

其中遇到一个问题，就是自己写的一些package，在spark中的map,reduce中并不能引用这个包里的函数。查文档说是要把Python 的package打包成egg，再提交上去才能正确引入。

上网查询了下python 打包，现在都是用setuptools，直接在package下穿件一个setup.py,
以下是代码：
```
from setuptools import setup  
  
setup(  
    name='project',  
    version='1.0',  
    description='test',  
    packages=[  
        'utils'  
    ],  
    py_modules=[  
        'settings'  
    ]  
)  
```


如果是个文件包，就指定再packages下，是个.py结尾的是指定再py_modules,就这么简单地设定。

然后Python setup.py bdist_egg就会创建一个egg出来了。

根据setuptools文档，python还可以根据c，c++语言，指定库，很容易生成用c底层的python包，这功能以后还有待研究。还可以根据不同平台，创建不一样的rpm,deb包。看起来打包真的十分容易。



参考网址：
1.http://stackoverflow.com/questions/2026395/how-to-create-python-egg-file

2.http://mrtopf.de/blog/en/a-small-introduction-to-python-eggs/