# Sapien尝试记录(2023.4)

## 1. 安装

### 方式一：Pip(PyPI) or Conda

配置环境

```
pip install sapien
```

### 方式二：Build from source[](https://sapien.ucsd.edu/docs/2.2/tutorial/basic/installation.html#build-from-source)

You may build SAPIEN from source to access latest features under development in the [dev](https://github.com/haosulab/SAPIEN/tree/dev) branch, and/or contribute to the project.

#### Clone SAPIEN[](https://sapien.ucsd.edu/docs/2.2/tutorial/basic/installation.html#clone-sapien)

```
git clone --recursive https://github.com/haosulab/SAPIEN.git
```

#### Build in Docker（暂弃）[](https://sapien.ucsd.edu/docs/2.2/tutorial/basic/installation.html#build-in-docker)

While it is possible to build SAPIEN natively on Linux. We strongly recommend building using [Docker](https://docs.docker.com/get-started/overview/).

```
cd SAPIEN
./docker_build_wheels.sh
```

安装docker：https://docs.docker.com/engine/install/ubuntu/#set-up-the-repository



## 2. 关于docker的作用  linux空间查看

Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.

y taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

- 现在的问题是在etc/apt/sources.list.d里docker.list出问题，但是并没有影响docker的正常安装
  -  Malformed entry 1 in list file /etc/apt/sources.list.d/docker.list (Suite)
    E: The list of sources could not be read.

（non-Gnome Desktop environments是什么？）(curl　gnupg是什么)

- 现在是磁盘满了



空间查看：

```shell
#查看当前文件夹下的所有文件大小（包含子文件夹）
du -h
#查看指定文件夹下的所有文件大小（包含子文件夹）
du -h [目录/文件]
#返回当前文件夹的总M数
du -sm
#返回指定文件夹/文件总M数
du -sm [文件夹/文件]
```

在 Linux 上查找可用磁盘空间的最简单的方法是[使用 df 命令](https://linuxhandbook.com/df-command/)。`df` 命令从字面意思上代表着磁盘可用空间disk free，很明显，它将向你显示在 Linux 系统上的可用磁盘空间。

```
df -h
```

使用 `-h` 选项，它将以人类可读的格式（MB 和 GB）来显示磁盘空间。
