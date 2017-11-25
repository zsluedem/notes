Go 语言中的大小写
====

今天遇到了go 的一个奇怪的坑，我明明写了某个interface的所有method，编译的时候却无法通过，报一下错误。

```
src/github.com/portainer/portainer/cmd/portainer/main.go:166:2: cannot use service (type *mongo.Service) as type portainer.MongoService in return argument:
	*mongo.Service does not implement portainer.MongoService (missing portainer.registry method)
		have mongo.registry(*portainer.Task) error
		want portainer.registry(*portainer.Task) error
```

然后查了很多资料感觉莫名其妙。

最后发现是go语言引用包的机制导致的，go语言中，一个文件内小写开头的method和变量都认为是private额，而大写的才是public，才能正确导出。

总结
-----
go

大写开头的变量和函数为公有的变量
小写开头的变量和函数为私有的变量