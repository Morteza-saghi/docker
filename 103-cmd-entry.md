# CMD Vs ENTRYPOINT

In a Dockerfile, both CMD and ENTRYPOINT define what command gets executed when a container starts, but they serve different purposes

### 1.CMD 
Acts as a default command that runs when the container starts.
Can be overridden when running the container.


Example:

```
CMD ["nginx", "-g", "daemon off;"]
```

Can be overridden:

```
docker run myimage echo "Hello"
```


### 2. ENTRYPOINT
Defines the main command that always executes.
Cannot be overridden using docker run <image> <override> unless --entrypoint is explicitly specified.

- Typically used when the container is meant to run as an application.

Example:

```
ENTRYPOINT ["python", "app.py"]
```
This ensures that python app.py always runs, even if arguments are passed.



### Combining CMD and ENTRYPOINT


ENTRYPOINT sets the main command, while CMD provides default arguments.

```
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Running docker run myimage executes:

```
python app.py
```

Running docker run myimage -m http.server executes:

```
python -m http.server
```


### When to Use What?
Use CMD for default behaviors that can be overridden.
Use ENTRYPOINT when your container should always run a specific command.
Use both together when you want a fixed command but allow arguments to be modified.
