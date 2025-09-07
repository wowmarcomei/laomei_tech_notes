# WSL Docker安装Nacos指南



参考[Nacos-docker](https://github.com/nacos-group/nacos-docker/tree/master)

## 1. 快速部署Nacos v2.x

```ba
docker run --name nacos-standalone-derby-v2.5.1 \
    -e MODE=standalone \
    -p 8848:8848 \
    -p 9848:9848 \
    -d nacos/nacos-server:v2.2.3
```

采用的使单机derby数据库模式部署。



## 2. Standalone Mysql

下载Nacos-Docker仓库

```bash
git clone https://github.com/nacos-group/nacos-docker.git
```

需要通过 example/.env 中的以下配置来更改 Compose 文件中 Nacos 镜像版本。
```bash
NACOS_VERSION=v2.2.3
```

通过docker-compose启动Nacos单机Server

```bash
cd example
./mysql-init.sh && docker-compose -f standalone-mysql.yaml up
```



## 3. docker单节点部署集群模式

```bash
cd example
docker-compose -f example/cluster-hostname.yaml up 
```



## 几个示例

- 服务注册示例

```bash
curl -X POST 'http://127.0.0.1:8848/nacos/v3/client/ns/instance?serviceName=quickstart.test.service&ip=127.0.0.1&port=8080
```



- 服务发现示例

```bash
curl -X GET 'http://127.0.0.1:8848/nacos/v3/client/ns/instance/list?serviceName=quickstart.test.service'
```



- 推送配置示例

```bash
curl -X POST 'http://127.0.0.1:8848/nacos/v3/auth/user/login' -d 'username=nacos' -d 'password=${your_password}'
curl -X POST 'http://127.0.0.1:8848/nacos/v3/admin/cs/config?dataId=quickstart.test.config&groupName=test&content=HelloWorld' -H "accessToken:${your_access_token}"
```



- 获取配置示例

```bash
curl -X GET 'http://127.0.0.1:8848/nacos/v3/client/cs/config?dataId=quickstart.test.config&groupName=test'
```



- 访问控制台

```bash
浏览器访问：http://127.0.0.1:8080/index.html
```