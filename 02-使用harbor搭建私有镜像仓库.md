# 使用harbor搭建私有镜像仓库.md

对镜像统一管理

## 1 创建证书

```shell
# 创建证书目录
$ mkdir -p /harbor/cert && cd /harbor/cert
# 创建私钥，需要输入密码
$ openssl genrsa -des3 -out server.key 2048
# 使用私钥创建证书请求文件
$ openssl req -new -key server.key -out server.csr
Enter pass phrase for server.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:Beijing
Locality Name (eg, city) [Default City]:Beijing
Organization Name (eg, company) [Default Company Ltd]:xxx
Organizational Unit Name (eg, section) []:Ops
Common Name (eg, your name or your server's hostname) []:harbor.xxx.com
Email Address []:xxx@xxx.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
# 退掉私钥密码（
$ cp server.key server.key.org &&  openssl rsa -in server.key.org -out server.key

$ 生成证书
$ openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
 Signature ok
 subject=/C=CN/ST=BJ/L=Beijing/O=Ops/OU=Ops/CN=harbor.xxx.com/emailAddress=xxxx@xxxx.com
 Getting Private key
```



## 2. 下载软件

[harbor-offline-installer-v2.4.1.tgz](https://github.com/goharbor/harbor/releases/download/v2.4.1/harbor-offline-installer-v2.4.1.tgz)

```shell
$ cd <to_path_harbor-offline-installr-v2.4.1.tgz>
$ tar zxvf harbor-offline-installer-v2.4.1.tgz -C ./harbor
$ cd harbor
# 其它变量暂时忽略
$ grep -v '^[[:space:]]*#' ./harbor.yml.tmpl | grep -v '^$' > ./harbor.yml
$ vim ./harbor.yml
# 需改如下几处
hostname: harbor.wiseco.com
https:
  certificate: /harbor/cert/server.key
  private_key: /harbor/cert/server.crt
harbor_admin_password: <your_passowrd>
database:
  password: <your_password>
data_volume: /harbor/data
log:
  local:
    location: /harbor/log
```

## 3.安装

```shell
# 需要安装了docker环境及docker-compose
$ cd <path_to_harbor>
$ ./prepare
$ ./install
...
[Step 5]: starting Harbor ...
Creating network "harbor-v241_harbor" with the default driver
Creating harbor-log ... done
Creating registry      ... done
Creating registryctl   ... done
Creating harbor-db     ... done
Creating harbor-portal ... done
Creating redis         ... done
Creating harbor-core   ... done
Creating nginx             ... done
Creating harbor-jobservice ... done
✔ ----Harbor has been installed and started successfully.----
```

## 4.docker客户端的配置

```shell
$ echo "<your harbor ip>    harbor.wiseco.com" >> /etc/hosts
# 编辑/etc/docker/daemon.json，增加如下配置(免证书配置)
,"insecure-registries": ["harbor.wiseco.com"]
,"live-restore": true
```

## 5.在docker客户端增加证书

`TODO：`


