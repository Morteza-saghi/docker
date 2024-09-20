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


Directive	Purpose
FROM	Defines the base image.
RUN	Executes commands to build the image.
CMD	Provides the default command for running a container.
ENTRYPOINT	Defines the command that will run as the container's main process.
COPY	Copies files from the host to the container.
ADD	Adds files, supports tar extraction and URLs.
WORKDIR	Sets the working directory for future instructions.
EXPOSE	Specifies port numbers that the container will listen on at runtime.
ENV	Sets environment variables.
ARG	Defines build-time environment variables.
VOLUME	Creates a mount point and marks it as externally mounted.
USER	Specifies the user to run subsequent commands as.
ONBUILD	Adds a trigger instruction to be executed during the next build phase.
STOPSIGNAL	Defines the system call signal to stop the container.
HEALTHCHECK	Defines a command to test the container’s health.
LABEL	Adds metadata to an image (e.g., version, maintainer, etc.).






