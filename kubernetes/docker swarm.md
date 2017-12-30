# docker swarm
----
##swarm集群搭建
执行`docker  swarm init`

```
$ docker swarm init
Swarm initialized: current node (i8ixhnetqogvnqi69jlbbhlff) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-30wjh2ual0u0w4t25am2ig372wp9ubei9b174gyz6jbita7fnw-2hzuvc0gu02ysqr04yp46rx6a 192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

$ docker swarm join-token manager
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-30wjh2ual0u0w4t25am2ig372wp9ubei9b174gyz6jbita7fnw-awyu6gszrldqe87kmxf20k42c 192.168.99.100:2377

```

然后就把当前主机设置为manager，可以通过`docker swarm join`加入节点。如果想加入为manager，则通过不一样的token加入可成为manager.


##docker registry

为了节省各个节点之间建立image的时间，现在在3个节点中加入一个registry(就是一个image源)。通过`docker run -d -p 5000:5000 --restart=always --name registry registry:2`启动一个镜像源服务。

每次建立一个image后，修改tag为`localhost:5000/my-image`然后`docker push localhost:5000/my-image`就可以把镜像上传到服务器.

这样构建一个image之后就直接把image push到registry中就可以了，那么其他节点也可以很轻松获取到image(通常构建一个image通常需要很长的时间，这样做可以极大的提高效率)。

**注意**：
由于这样直接启用的registry服务并没有加上https证书需求，所以其实直接push image到没有经过设置的docker是会报出安全错误，阻止你上传image的。这时候需要设置docker允许的insecure-registry，把服务的http地址加入到docker的insecure-registry中。

----------