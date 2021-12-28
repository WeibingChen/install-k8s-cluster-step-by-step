# 安装docker环境

## 01-安装docker引擎

### 卸载旧版本

```shell
 $ yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

> It’s OK if `yum` reports that none of these packages are installed.

### 设置安装镜像源

> 这里使用aliyun镜像源

```shell
 $ yum install -y yum-utils
 $ yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

> The following command enables the **nightly** repository. `sudo yum-config-manager --enable docker-ce-nightly`
> 
> To enable the **test** channel, run the following command: `sudo yum-config-manager --enable docker-ce-test`

```shell
$ yum-config-manager --enable docker-ce-nightly && yum-config-manager --enable docker-ce-test
```

### 安装docker-ce

安装默认版本，当前版本 20.10.12

```shell
$ yum install -y docker-ce docker-ce-cli containerd.io
```

安装指定版本

```shell
$ yum list docker-ce --showduplicates | sort -r
$ yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

测试安装结果

```shell
$ docker version
Client: Docker Engine - Community
 Version:           20.10.12
 API version:       1.41
 Go version:        go1.16.12
 Git commit:        e91ed57
 Built:             Mon Dec 13 11:45:41 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

# 启动docker并设置开机自启
$ systemctl enable docker && systemctl start docker
```

修改配置文件daemon.json并重启

```shell
$ cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF
```

启动docker容器

```shell
$ docker run hello-world
```

## 02-安装docker-compose

```shell
$ curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
$ docker-compose version
docker-compose version 1.29.2, build 5becea4c
docker-py version: 5.0.0
CPython version: 3.7.10
OpenSSL version: OpenSSL 1.1.0l  10 Sep 2019
```
