# sparse matrix(稀疏矩阵)
------------------
下文大致参考[sparse_matrix-维基百科](https://en.wikipedia.org/wiki/Sparse_matrix)

最近在接触sklearn 进行自然语言处理的时候，发现做类似tf-idf等等这样的算法时候，很多时候自然语言得出来的矩阵里面，是含有很多个0这样的元素的。这时候如果用一个大矩阵去处理数据，很容易造成内存不足（我处理100多万份短文本，分词后转化为矩阵明显是不足的），经过我研究，sklearn是默认使用一种sparse matrix来降低内存的使用量。

sparse matrix大多使用在一个含有很多0的矩阵中，在这样的矩阵中转化为sparse matrix是十分节省内存的。sparse matrix只记录不含0的矩阵元素。

-----------------------------
###sparse matrix 定义
-----------------------
一个m*n的矩阵M，可以直接用3个一维矩阵矩阵表示（A, IA, JA）,现在用NNA标记为M矩阵中不是0的数量

1.一维矩阵A长度等于NNA,矩阵A保存着所有M矩阵中非0元素，顺序按照M矩阵从左到右，从上到下顺序。
2.一维矩阵IA
- IA[0]=0
- IA[i] = IA[i-1]+在i-1行上所有非0元素的个数
3.一维矩阵JA,对应A矩阵中每个元素所对应行的列号

例如：
![例子](https://wikimedia.org/api/rest_v1/media/math/render/svg/0fce3708488472b34a85f11f54d8df3eeab1aabc)
是一个4x4的带有4个非0的矩阵
这个矩阵能用下列3个一维矩阵表示
> A  = [ 5 8 3 6 ]
> IA = [ 0 0 2 3 4 ]
> JA = [ 0 1 2 1 ]