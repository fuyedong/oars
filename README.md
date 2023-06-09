# oars

[中文版](https://github.com/xwsoul/oars/blob/main/README_zh.md)

## Introduction

`oars` is a wrapper for Docker container commands that can encapsulate different Docker parameters based on the directory, meeting the need to simplify common command execution within containers. Written in Bash, `oars` allows users to specify various parameters of the Docker container through command line options, such as container name, working directory, interactive mode, and more.

## Command Options

* `-n <container_name>`: Specifies the name of the Docker container. This is a required parameter.
* `-s <shell_environment>`: Specifies the shell environment to be executed within the container, with a default value of /bin/sh -c.
* `-w <work_directory>`: Specifies the working directory within the container, with a default value of /.
* `-m <interactive_mode>`: Specifies the interactive mode of the container, with a default value of -it.
* `-c <container_command>`: Specifies the command to be executed within the container. If this parameter is specified, the script will execute the given command within the container.
* `-r <running_command>`: Executes the given command with the prefix set by OPT_CONTAINER_RUNNING_PREFIX. If the prefix is not set, the script will report an error and exit.
* `-l <line_numbers>`: Show the log with the specified number of lines

## Configuration Files

The script attempts to load configuration files from the following paths:

1. /etc/oars.conf
2. /etc/oars
3. $HOME/.oars.conf
4. $HOME/.oars
5. $(pwd)/.oars.conf
6. $(pwd)/.oars

Settings in the configuration files will override the default settings in the script. Moreover, configurations loaded later will override those loaded earlier. This means that configuration files in the current directory have the highest priority for settings.

### Configuration Variables

* `OPT_DOCKER_EXEC`: The path to the Docker executable, defaulting to `/usr/bin/docker`. If Docker is installed in another location, modify this variable.
* `OPT_CONTAINER_NAME`: The container name, defaulting to empty. When using the script, the container name must be specified using the `-n` option.
* `OPT_CONTAINER_SHELL_ENV`: The shell environment to be executed within the container, defaulting to `/bin/sh -c`.
* `OPT_CONTAINER_WORK_DIR`: The working directory within the container, defaulting to `/`.
* `OPT_CONTAINER_INTERACTIVE_MODE`: The interactive mode of the container, defaulting to `-it`.
* `OPT_CONTAINER_COMMAND`: The command to be executed within the container, defaulting to empty. When using the script, the command can be specified using the `-c` option.
* `OPT_CONTAINER_RUNNING_PREFIX`: The prefix for running the container command, defaulting to empty. When using the script, the command can be specified using the `-r` option, and it will be run together with this prefix.
* `OPT_LOG_LINES`: The number of lines printed each time the log is shown (Not recommended to configure).

## Examples

### Example 1

```shell
oars -n my_container -w /app -c "python manage.py runserver"
```

The above command will execute the `python manage.py runserver` command within the `my_container` container, with the working directory set to `/app`.

### Example 2

Create a configuration file in the current directory:

```shell
touch .oars
```

Write the following configuration:

```shell
OPT_CONTAINER_NAME="php"
OPT_CONTAINER_WORK_DIR="/data/web/example-app"
OPT_CONTAINER_RUNNING_PREFIX="php artisan"
```

When executing the following `oars` command:

```shell
oars -r 'test'
```

It is equivalent to executing:

```docker
/usr/bin/docker exec -it -w /data/web/example-app php \
    /bin/sh -c php artisan test
```

This greatly simplifies and reduces the parameters needed for executing commands, thus improving the invocation efficiency.

## Restart container

```shell
oars restart
```

Also supports the parameter `-n`

```shell
oars restart -n <container_name>
```

## Show container logs

```shell
oars logs
```

Also supports the parameter `-n`

```shell
oars logs -n <container_name>
```

The parameter `-l` show log with specified number of lines

```shell
oars logs -l <n>
```