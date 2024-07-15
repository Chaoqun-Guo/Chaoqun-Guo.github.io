---
title: "Automating Docker Container Command Execution with a Bash Script"
date: 2020-01-12
permalink: /posts/2020/01/12/shell/
excerpt: "Automating Docker Container Command Execution with a Bash Script"
tags:
  - linux
  - shell
  - docker
---

# Automating Docker Container Command Execution with a Bash Script

Managing Docker containers often involves running specific commands inside them. This can be a tedious task if done repeatedly, but with a simple Bash script, we can automate this process. In this blog post, I'll walk you through a Bash script that allows you to specify a Docker container and a command to run inside it, making Docker management more efficient.

## The Bash Script

Here's the complete Bash script:

```bash
#!/bin/bash

_comp() {
    echo "$1 | $2: ${@:2}."
}

_fo() {
    echo "=> $@."
}

_usage() {
    echo "$0 -h | --help to show this message."
    echo "Usage: $0 [-n <container_name> -c <command>]"
    echo "Options:"
    echo "  -h, --help        Show this help message and exit."
    echo "  -n, --name        Specify the name of the Docker container."
    echo "  -c, --cmd         Specify the command to execute inside the Docker container."
    echo "Example: $0 -n my_container -c ls"
    exit 0
}

function handle_input() {

    if [ $# -eq 0 ]; then
        echo "Please provide the parameters."
        _usage
    fi

    local args=$(getopt -o "hn:c:" -l "help,name:,cmd:" -- "$@")
    eval set -- "${args}"
    while true; do
        case "${1}" in
        -h | --help)
            _usage
            shift
            break
            ;;
        -n | --name)
            _name=${2}
            shift 2
            ;;
        -c | --cmd)
            _cmd=${2}
            shift 2
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "Invalid Option: $@"
            exit 1
            ;;
        esac
    done
}

function run() {
    _execit() {
        _fo "You are executing cmd '${_cmd}' in container '${_name}'"
        docker exec -it ${_name} ${_cmd}
        return 0
    }

    _execit

    if [ $? -eq 0 ]; then
        _fo "Bye!"
    else
        _fo "Something went wrong."
    fi
}

handle_input "$@"

run
```

## How It Works

### 1. Parameter Parsing

The script uses `getopt` for parsing command-line arguments, which allows the user to specify the Docker container name and the command to execute inside the container. It also provides help messages for guidance.

### 2. Functions

- `_comp` and `_fo`: These are simple utility functions for formatted output.
- `_usage`: This function displays usage information and exits the script.
- `handle_input`: This function parses the input arguments and sets the appropriate variables.
- `run` and `_execit`: These functions execute the specified command inside the Docker container using `docker exec`.

### 3. Error Handling

The script includes basic error handling to ensure it exits gracefully and provides meaningful messages if something goes wrong.

## Example Dockerfile

To test this script, you can use a simple Dockerfile to create a container:

```dockerfile
# Use the official Alpine image as the base image
FROM alpine:3.14

# Install basic packages
RUN apk add --no-cache bash

# Set the working directory
WORKDIR /app

# Default command to keep the container running
CMD ["sh"]
```

### Building and Running the Docker Container

1. **Build the Docker image:**

   ```bash
   docker build -t my-alpine .
   ```

2. **Run the Docker container:**

   ```bash
   docker run -d --name my_container my-alpine
   ```

3. **Save and run the Bash script:**

   Save the script as `run_command.sh` and give it execute permissions:

   ```bash
   chmod +x run_command.sh
   ```

   Then, use the script to execute a command inside the container:

   ```bash
   ./run_command.sh -n my_container -c ls
   ```

## Conclusion

This Bash script simplifies the process of running commands inside Docker containers by automating parameter handling and execution. By following the examples and explanations in this blog post, you can adapt and expand this script to suit your specific needs, making your Docker management tasks more efficient and streamlined.

Feel free to customize the script and Dockerfile as needed for your projects. Happy Dockerizing!

```bash
This Markdown blog post includes an introduction, an explanation of the Bash script, a sample Dockerfile, steps to build and run the Docker container, and a conclusion. It should provide a comprehensive guide for readers to understand and use the provided script.
```
