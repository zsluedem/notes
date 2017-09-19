#consul搭设集群
##集群说明
3台机子：

1. 192.168.99.100
2. 192.168.99.101
3. 192.168.99.102


##consul server配置
`/etc/consul.d/server/config.json`

```json
{
"bind_addr": "192.168.99.102",
"datacenter": "dc1",
"data_dir": "/data/var/consul",
"encrypt": "CtqVBYJzjz8VyoO20yDHqw==",
"log_level": "INFO",
"enable_syslog": true,
"enable_debug": true,
"node_name": "ConsulServer3",
"server": true,
"bootstrap_expect": 3,
"leave_on_terminate": false,
"skip_leave_on_interrupt": true,
"rejoin_after_leave": true,
"retry_join": [
  "192.168.99.100:8301",
  "192.168.99.101:8301",
  "192.168.99.102:8301"
  ]
}
```

##consul server启动命令
`consul agent -config-dir /etc/consul.d/server/`


##consul client 配置
`/etc/consul.d/client/config.json`

```json
{
 "bind_addr": "192.168.1.21",
 "datacenter": "dc1",
 "data_dir": "/var/consul",
 "encrypt": "CtqVBYJzjz8VyoO20yDHqw==",
 "log_level": "INFO",
 "enable_syslog": true,
 "enable_debug": true,
 "node_name": "ConsulClient",
 "server": false,
   "service": {"name": "Apache", "tags": ["HTTP"], "port": 80,
   "check": {"script": "curl localhost >/dev/null 2>&1", "interval": "10s"}},
 "rejoin_after_leave": true,
 "retry_join": [
  "192.168.99.100:8301",
  "192.168.99.101:8301",
  "192.168.99.102:8301"
  ]
 }
```