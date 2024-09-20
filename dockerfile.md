# Dockerfile Cheatsheet

### Basic Commands

| **Command**           | **Description**                                                                                |
|-----------------------|------------------------------------------------------------------------------------------------|
| `FROM`                | Specifies the base image. Example: `FROM ubuntu:20.04`.                                         |
| `RUN`                 | Executes a command in a new layer on top of the current image. Example: `RUN apt-get update`.   |
| `CMD`                 | Provides defaults for the container when it starts. Example: `CMD ["echo", "Hello World"]`.     |
| `ENTRYPOINT`          | Configures the container to run as an executable. Example: `ENTRYPOINT ["python", "app.py"]`.   |
| `COPY`                | Copies files from the host machine to the container. Example: `COPY ./src /app`.                |
| `ADD`                 | Adds files to the container. Also supports URLs and tar file extraction. Example: `ADD file.tar.gz /app`. |
| `WORKDIR`             | Sets the working directory inside the container. Example: `WORKDIR /app`.                       |
| `EXPOSE`              | Informs Docker that the container will listen on a specified port. Example: `EXPOSE 8080`.      |
| `ENV`                 | Sets environment variables. Example: `ENV API_KEY=abcdef12345`.                                 |
| `ARG`                 | Defines build-time variables. Example: `ARG VERSION=1.0`.                                       |
| `VOLUME`              | Defines a mount point for a volume. Example: `VOLUME /data`.                                    |
| `USER`                | Sets the user for subsequent `RUN`, `CMD`, and `ENTRYPOINT` commands. Example: `USER nobody`.   |

### Common Examples

#### Basic Ubuntu Image

```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y curl
CMD ["echo", "Hello from Docker"]
```

----

### Using COPY and WORKDIR

```
FROM node:14
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD ["npm", "start"]
```

### Using ENTRYPOINT and CMD

```
FROM python:3.9
WORKDIR /usr/src/app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
ENTRYPOINT ["python"]
CMD ["app.py"]
```

---

### Difference between CMD and ENTRYPOINT:
- CMD: Defines default command but can be overridden at runtime.
- ENTRYPOINT: Always executed and not overridden unless --entrypoint flag is used.

---

### Multi-Stage Build

```
# First stage (build)
FROM golang:1.16 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp
# Second stage (run)
FROM alpine:3.14
COPY --from=builder /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

---

### Setting Environment Variables and Arguments

```
FROM node:14
ARG NODE_ENV=production
ENV NODE_ENV=$NODE_ENV
WORKDIR /app
COPY . .
RUN npm install
CMD ["npm", "start"]
```
---

### Using VOLUME

```
FROM mysql:5.7
ENV MYSQL_ROOT_PASSWORD=my-secret-pw
VOLUME /var/lib/mysql
EXPOSE 3306
```
The VOLUME directive creates a mount point with the specified path (/var/lib/mysql) and marks it as holding externally mounted data. This ensures that data stored in this directory persists beyond the lifecycle of the container.

---

### Dockerfile Best Practices
- Minimize Layers: Combine multiple RUN commands into one to reduce layers.

```
RUN apt-get update && apt-get install -y \
curl \
git \
&& apt-get clean
```

---

* Use .dockerignore: Exclude unnecessary files from being copied into the container by using a .dockerignore file (like .git, node_modules, etc.).

* Leverage Multi-Stage Builds: Helps in creating small final images by discarding intermediate artifacts.

* Pin Image Versions: Specify versions or tags for base images to ensure reproducibility.

* Reduce Image Size: Use smaller base images (like alpine) when possible.


---


### Docker Ignore Example
Create a .dockerignore file to exclude files/folders from the build context:

```
node_modules
.git
README.md
Dockerfile
```

---

## Common Directives Summary

| **Instruction** | **Description** |
|-----------------|-----------------|
| `FROM` | Specifies the base image. Example: `FROM ubuntu:20.04`. <br> - **Purpose**: Defines the starting point for your Docker image. It can be an official image from Docker Hub or a custom image. <br> - **Syntax**: `FROM <image>[:<tag>]` <br> - **Example**: `FROM python:3.9-slim` uses the slim variant of Python 3.9. <br> - **Note**: Must be the first non-comment instruction in the Dockerfile. |
| `RUN` | Executes a command in a new layer on top of the current image. Example: `RUN apt-get update`. <br> - **Purpose**: Install packages, create files/directories, or perform any setup needed. <br> - **Syntax**: `RUN <command>` or `RUN ["executable", "param1", "param2"]` <br> - **Best Practice**: Combine multiple commands using `&&` to reduce image layers. <br> - **Example**: `RUN apt-get update && apt-get install -y curl git` |
| `CMD` | Provides defaults for the container when it starts. Example: `CMD ["echo", "Hello World"]`. <br> - **Purpose**: Specifies the default command to run when a container starts. <br> - **Syntax**: `CMD <command>` or `CMD ["executable", "param1", "param2"]` <br> - **Behavior**: Can be overridden by specifying a command at runtime. <br> - **Use Case**: Setting a default application to run. |
| `ENTRYPOINT` | Configures the container to run as an executable. Example: `ENTRYPOINT ["python", "app.py"]`. <br> - **Purpose**: Defines the executable that always runs when the container starts. <br> - **Syntax**: `ENTRYPOINT ["executable", "param1", "param2"]` <br> - **Behavior**: Cannot be overridden by runtime commands unless `--entrypoint` is used. <br> - **Use Case**: Ensuring a specific application always runs. |
| `COPY` | Copies files from the host machine to the container. Example: `COPY ./src /app`. <br> - **Purpose**: Transfer files/directories from your local machine to the Docker image. <br> - **Syntax**: `COPY <source> <destination>` <br> - **Features**: Does not support remote URLs. <br> - **Use Case**: Adding application code, configuration files, etc. |
| `ADD` | Adds files to the container. Also supports URLs and tar file extraction. Example: `ADD file.tar.gz /app`. <br> - **Purpose**: Similar to `COPY` but with additional functionalities. <br> - **Syntax**: `ADD <source> <destination>` <br> - **Features**: Can handle remote URLs and automatically extract compressed files. <br> - **Caution**: Use `COPY` when you don't need these extra features to avoid unexpected behavior. |
| `WORKDIR` | Sets the working directory inside the container. Example: `WORKDIR /app`. <br> - **Purpose**: Defines the default directory for subsequent instructions (`RUN`, `CMD`, etc.). <br> - **Syntax**: `WORKDIR <path>` <br> - **Behavior**: Creates the directory if it doesn't exist. <br> - **Use Case**: Organizing application files and setting context for commands. |
| `EXPOSE` | Informs Docker that the container will listen on a specified port. Example: `EXPOSE 8080`. <br> - **Purpose**: Documents the ports the container listens on at runtime. <br> - **Syntax**: `EXPOSE <port> [<port>/<protocol>]` <br> - **Note**: Does not publish the port; use `-p` or `-P` flags with `docker run` to publish. <br> - **Use Case**: Indicating application ports for reference and tooling. |
| `ENV` | Sets environment variables. Example: `ENV API_KEY=abcdef12345`. <br> - **Purpose**: Define environment variables accessible during build and runtime. <br> - **Syntax**: `ENV <key>=<value>` or `ENV <key1>=<value1> <key2>=<value2> ...` <br> - **Use Case**: Configuring application settings, secrets (with caution), and build parameters. |
| `ARG` | Defines build-time variables. Example: `ARG VERSION=1.0`. <br> - **Purpose**: Pass variables during the image build process. <br> - **Syntax**: `ARG <name>[=<default value>]` <br> - **Scope**: Only available during the build; not accessible at runtime. <br> - **Use Case**: Parameterizing builds, such as specifying versions or feature flags. |
| `VOLUME` | Defines a mount point for a volume. Example: `VOLUME /data`. <br> - **Purpose**: Create a mount point with a specified name and mark it as holding externally mounted data. <br> - **Syntax**: `VOLUME ["/data"]` or `VOLUME /data` <br> - **Behavior**: Docker manages the volume's lifecycle, allowing data persistence. <br> - **Use Case**: Storing databases, logs, or user-generated content. |
| `USER` | Sets the user for subsequent `RUN`, `CMD`, and `ENTRYPOINT` commands. Example: `USER nobody`. <br> - **Purpose**: Specify the user (and optionally group) to run commands as. <br> - **Syntax**: `USER <username>` |
| `ONBUILD` | Adds a trigger instruction to be executed during the next build phase. <br> - **Purpose**: Define instructions that will be executed when the image is used as a base for another build. <br> - **Syntax**: `ONBUILD <instruction>` <br> - **Use Case**: Creating reusable base images that enforce certain build steps in child images. |
| `STOPSIGNAL` | Defines the system call signal to stop the container. <br> - **Purpose**: Specify the signal to use when stopping the container. <br> - **Syntax**: `STOPSIGNAL <signal>` <br> - **Default**: `SIGTERM` <br> - **Use Case**: Gracefully stopping applications that require a specific shutdown signal. |
| `HEALTHCHECK` | Defines a command to test the container’s health. <br> - **Purpose**: Monitor the health of the running container. <br> - **Syntax**: `HEALTHCHECK [OPTIONS] CMD <command>` <br> - **Options**: `--interval`, `--timeout`, `--retries`, etc. <br> - **Use Case**: Ensuring applications are running correctly and enabling Docker to manage unhealthy containers. |
| `LABEL` | Adds metadata to an image (e.g., version, maintainer, etc.). <br> - **Purpose**: Attach key-value metadata to the image. <br> - **Syntax**: `LABEL <key>=<value> [<key>=<value> ...]` <br> - **Use Case**: Providing information about the image, such as version, description, maintainer contact, licensing, etc. <br> - **Best Practice**: Use consistent labeling for automation and documentation. |

**



