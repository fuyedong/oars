# oars

## 介绍

`oars` 是一个 Docker 容器命令的包装器, 该命令可以根据目录封装不同 docker 参数, 以满足简化常用命令在容器中执行的需要. `oars` 采用 Bash 编写，它允许用户通过命令行选项指定Docker容器的各种参数，如容器名称、工作目录、交互模式等。

## 命令选项

* `-n <container_name>`: 指定Docker容器的名称。这是一个必需的参数。
* `-s <shell_environment>`: 指定在容器内执行的shell环境，默认为/bin/sh -c。
* `-w <work_directory>`: 指定容器内的工作目录，默认为/。
* `-m <interactive_mode>`: 指定容器的交互模式，默认为-it。
* `-c <container_command>`: 指定在容器内执行的命令。如果指定了此参数，脚本会在容器内执行给定的命令。
* `-r <running_command>`: 使用 `OPT_CONTAINER_RUNNING_PREFIX` 设置的前缀执行给定的命令。如果未设置前缀，则脚本会报错并退出。

## 配置文件

脚本会尝试从以下路径加载配置文件：

1. /etc/oars.conf
2. /etc/oars
3. $HOME/.oars.conf
4. $HOME/.oars
5. $(pwd)/.oars.conf
6. $(pwd)/.oars

配置文件中的设置会覆盖脚本中的默认设置。且后加载的配置会覆盖先加载的配置, 这意味着当前目录下的配置文件拥有最高的设置优先级.

### 配置参数

* `OPT_DOCKER_EXEC`: Docker可执行文件的路径，默认为/usr/bin/docker。如果Docker安装在其他位置，请修改此变量。
* `OPT_CONTAINER_NAME`: 容器名称，默认为空。在使用脚本时，需要使用 `-n` 选项指定容器名称。
* `OPT_CONTAINER_SHELL_ENV`: 容器内执行的 Shell 环境，默认为 `/bin/sh -c`。
* `OPT_CONTAINER_WORK_DIR`: 容器内的工作目录，默认为 `/`。
* `OPT_CONTAINER_INTERACTIVE_MODE`: 容器的交互模式，默认为`-it`。
* `OPT_CONTAINER_COMMAND`: 在容器内执行的命令，默认为空。在使用脚本时，可以使用 `-c` 选项指定要执行的命令。
* `OPT_CONTAINER_RUNNING_PREFIX`: 运行容器命令的前缀，默认为空。在使用脚本时，可以使用 `-r` 选项指定要执行的命令，并将其与此前缀一起运行。

## 使用示例

### 示例1

```shell
oars -n my_container -w /app -c "python manage.py runserver"
```

上述命令将在名为 `my_container` 的容器中，将工作目录设置为 `/app` 并运行 `python manage.py runserver` 命令。

### 示例2

在当前目录下配置文件

```shell
touch .oars
```

写入以下配置内容

```shell
OPT_CONTAINER_NAME="php"
OPT_CONTAINER_WORK_DIR="/data/web/example-app"
OPT_CONTAINER_RUNNING_PREFIX="php artisan"
```

当执行执行以下 `oars` 命令时:

```shell
oars -r 'test'
```

相当于执行了

```docker
/usr/bin/docker exec -it -w /data/web/example-app php \
    /bin/sh -c php artisan test
```

这大大地简化了减少了需要执行命令的参数, 提高了调用效率.