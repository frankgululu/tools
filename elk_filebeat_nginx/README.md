# docker-compose 形式启动ElasticSearch 集群
## 调整虚拟memory vm.max_map_count
```bash
  grep vm.max_map_count /etc/sysctl.conf
  vm.max_map_count=262144
  
  sysctl -w vm.max_map_count=262144 #临时设置
```
## 调整文件打开数的大小
```bash
vim /etc/security/limits.conf
#elasticsearch - nofile 65536
* soft nofile 65535
* hard nofile 65535
# Once finished, run the following command to reload units
systemctl daemon-reload
```
## 启动集群
```bash
docker-compose -f es-cluster.yaml up -d
```
## 配置ES集群的设置，这里有三种形式
- 通过环境变量的形式
```bash
environment:
   - cluster.name=docker-cluster
   - bootstrap.memory_lock=true
   - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
   - "discovery.zen.ping.unicast.hosts=elasticsearch"
```
- 通过外部挂载的形式
```bash
volumes:
  - ./elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro
```
- 自定义image
```bash
FROM docker.elastic.co/elasticsearch/elasticsearch:6.0.1
COPY --chown=elasticsearch:elasticsearch elasticsearch.yml /usr/share/elasticsearch/config/

docker build --tag=elsticsearch-custom .
```
