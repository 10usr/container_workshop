[back](container_workshop.md)
## Lab 9: Operations with images

<br />

You are already familiar with one command, `podman images`. You can also remove images, tag and untag them.

<br />
<br />

### **Step 1:** Build a new version

First let build the new figlet v3 from Containerfile, so that we have more versions.
```
 $ podman build -t figlet:v3 .
```

<br />
<br />

### **Step 2:** Removing images and containers

Let's start with removing the image figlet verion 1 (figlet:v1) that takes too much disk space:

```
$ podman rmi figlet:v1
Error response from daemon: conflict: unable to remove repository reference "figlet:v1" (must force) - container 3b8931ffdacd is using its referenced image bbc0e5eb5777

```

podman complains that there are containers using this image. How is this possible? We thought that all our containers are gone.
Actually, podman keeps track of all containers, even those that have stopped:

```
$ podman ps -a
(OUTPUT)
```

We can now delete the container:

```
$ podman rm 292d1e8d5103
292d1e8d5103
```

and the image:

```
$ podman rmi figlet:v1

output

```

<br />
<br />

### **Step 2:** Tagging images

`podman tag` helps us to tag images.

We have a lot of versions of `figlet` built, but latest still points to the old `v1`.

```
$ podman images | grep figlet
figlet                                             v2                  bbc0e5eb5777        2 days ago          170 MB
figlet                                             v3                  bbc0e5eb5777        2 days ago          170 MB
figlet                                             latest              b7fa8e20196b        2 days ago          170 MB
figlet                                             v1                  b7fa8e20196b        2 days ago          170 MB
```

Let's change that by re-tagging `latest` to `v3`:

```
$ podman tag figlet:v3 figlet:latest
$ podman images | grep figlet

```

Both `v3` and `latest` point to the same image ID `d0ec3cfed6f7`.

<br />
<br />

### **Step 3:** Publishing images

Images are distributed with a special service - `docker registry`.
Let us spin up a local registry:

```
$ podman run -p 5000:5000 --name registry -d registry:2
```

`podman push` is used to publish images to registries.

To instruct where we want to publish, we need to append registry address to repository name:

```
$ podman tag figlet:v3 127.0.0.1:5000/figlet:v3
$ podman push 127.0.0.1:5000/figlet:v3 --tls-verify=false
```

`podman push` pushed the image to our "remote" registry.

<br />
<br />

### **Step 4:** Stop all containers and images

Cleanup every figlet container and image.\
Stop every figlet container
```
$ podman stop $(podman ps | grep figlet | awk '{print$3}')
```

Delete every figlet containers.\
This must be run first because images are attached to containers
```
$ podman rm $(podman ps -a | grep figlet | awk '{print$3}')
```

Delete every figlet image
```
$ podman rmi $(podman images | grep figlet | awk '{print$3}')
```

We can skip the `podman stop` and `podman rm` commands and delete every figlet image including all the atteched containers, by:
```
$ podman rmi -f $(podman images | grep figlet | awk '{print$3}')
```

See that the figlet images are removed.
```
$ podman images | grep figlet | awk '{print$3}'
```


<br />
<br />

### **Step 5:** PULL the image from registry

We can now download the image using the `podman pull` command:

```
$ podman pull 127.0.0.1:5000/figlet
v3: Pulling from figlet
Digest: sha256:c472a7ec8ab2b0db8d0839043b24dbda75ca6fa8816cfb6a58e7aaf3714a1423
Status: Image is up to date for 127.0.0.1:5000/figlet
```

and see that it is pulled.

```
$ podman images
```

Repeat step 4 if you want a clean envoirement. 

<br />

## End of the Container labs :-)

[back](container_workshop.md)