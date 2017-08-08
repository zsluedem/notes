参考网站：[sciki-learn feature-extraction](http://scikit-learn.org/stable/modules/feature_extraction.html)

##tf-idf基本概念
----------------------
tf-idf主要是用于提取文本关键词，在文本中，有些词语如：我，你，他这种词会在大量文本出现，而对文本的分类起不大作用的词语。这时候使用tf-idf提取文本的关键词就可以把这些通用次筛选掉了。

直接上公式了，一个词的tf-idf用下式计算：
![这里写图片描述](http://img.blog.csdn.net/20161206152028639)
其中tf是词在一个文本出现的次数,idf指一个词在所有文本出现的值，用下式表示。
其中idf在scikit-learn用下式计算：
![这里写图片描述](http://img.blog.csdn.net/20161206152420580)
在scikit-learn中最后会用Euclidean norm来把值保留在0-1之间。

```python
>>>counts = [[3, 0, 1],
           [2, 0, 0],
           [3, 0, 0],
           [4, 0, 0],
           [3, 2, 0],
           [3, 0, 2]]
>>>tfidf = transformer.fit_transform(counts)
>>>tfidf.toarray()                        
array([[ 0.81940995,  0.        ,  0.57320793],
       [ 1.        ,  0.        ,  0.        ],
       [ 1.        ,  0.        ,  0.        ],
       [ 1.        ,  0.        ,  0.        ],
       [ 0.47330339,  0.88089948,  0.        ],
       [ 0.58149261,  0.        ,  0.81355169]])
```
上面是一个tf-idf计算过程
![这里写图片描述](http://img.blog.csdn.net/20161206153500302)
![这里写图片描述](http://img.blog.csdn.net/20161206153523130)
![这里写图片描述](http://img.blog.csdn.net/20161206153601100)
![这里写图片描述](http://img.blog.csdn.net/20161206153618663)
这是第一个词的tf-idf
剩下两个词语用下列计算得
![这里写图片描述](http://img.blog.csdn.net/20161206153728960)
![这里写图片描述](http://img.blog.csdn.net/20161206153739679)
最后归一化
![这里写图片描述](http://img.blog.csdn.net/20161206153934914)
这样就能计算出所有文档中所有词语的tf-idf