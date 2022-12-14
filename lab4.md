[back](container_workshop.md)
## Lab 4: Understanding Container Images

<br />

### Objectives

In this lab, we will explain:
-   What is an image.
-   What is a layer.
-   The various image namespaces.
-   How to search and download images.
-   Image tags and when to use them.

<br />

#### What is an image?

-   An image is a collection of files + some meta data.
    (Technically: those files form the root filesystem of a container.)
-   Images are made of *layers*, conceptually stacked on top of each other.
-   Each layer can add, change, and remove files.
-   Images can share layers to optimize disk usage, transfer times, and memory use.
-   A layer example could be:
    -   layer: CentOS
    -   layer: JRE
    -   layer: Tomcat
    -   layer: Dependencies
    -   layer: Application JAR
    -   layer: Configuration

<br />

#### Differences between containers and images

-   An image is a read-only filesystem.
-   A container is an encapsulated set of processes running in a read-write copy of that filesystem.
-   To optimize container boot time, *copy-on-write* is used instead of regular copy.
-   `podman run` starts a container from a given image.

Let's give a couple of metaphors to illustrate those concepts.

<br />

**Image as stencils**

Images are like templates or stencils that you can create containers from.

Object-oriented programming
-   Images are conceptually similar to *classes*.
-   Layers are conceptually similar to *inheritance*.
-   Containers are conceptually similar to *instances*.

<br />

**Wait a minute...**

If an image is read-only, how do we change it?
-   We don't.
-   We create a new container from that image.
-   Then we make changes to that container.
-   When we are satisfied with those changes, we transform them into a new layer.
-   A new image is created by stacking the new layer on top of the old image.

<br />

**A chicken-and-egg problem**

-   The only way to create an image is by "freezing" a container.
-   The only way to create a container is by instanciating an image.

<br />

**Creating the first images** ???

        There is a special empty image called scratch.
        -   It allows to *build from scratch*.

        The podman import command import a tarball and save it as a filesystem image.
        -   The imported tarball becomes a standalone image.
        -   That new image has a single layer.

        Note: you will probably never have to do this yourself.

<br />

**Creating other images**

`podman commit`
-   Saves all the changes made to a container into a new layer.
-   Creates a new image (effectively a copy of the container).

`podman build`
-   Performs a repeatable build sequence.
-   This is the preferred method!

We will explain both methods in a moment.

<br />

**Images namespaces**

There are three namespaces:

-   Official images e.g. ubuntu, busybox ...
-   User (and organizations) images e.g. hcscompany/clock
-   Self-hosted images

 e.g. registry.example.com:5000/my-private/image

Let's explain each of them.

<br />

**Root namespace**

The root namespace is for official images. They are put there by .???, but they are generally authored and maintained by third parties.

Those images include:
-   Small, "swiss-army-knife" images like busybox.
-   Distro images to be used as bases for your builds, like ubuntu, fedora...
-   Ready-to-use components and services, like redis, postgresql... User namespace

The user namespace holds images for Docker Hub users and organizations.\
For example:

`hcscompany/clock`

The Docker Hub user is:

`hcscompany`

The image name is:

`clock`

<br />

**Self-Hosted namespace**

This namespace holds images which are not hosted on Docker Hub, but on third party registries. They contain the hostname (or IP address), and optionally the port, of the registry server.

For example:

`localhost:5000/wordpress`

-   localhost:5000 is the host and port of the registry
-   wordpress is the name of the image

How do you store and manage images?\
Images can be stored:

-   On your local host.
-   In a container registry.

You can use podman to download (pull) or upload (push) images.

To be more accurate: you can use podman to tell a server to push and pull images to and from a registry.

<br />
<br />

### **Step 1:** Showing current images

Let's look at what images are on our host now.
```
$ podman images
REPOSITORY                                         TAG                 IMAGE ID            CREATED             SIZE
mkerker/duckhunt                                   latest              5df5972ae7b5        2 months ago        771 MB
node                                               latest              efe7b69d7b71        2 months ago        660 MB
httpd                                              latest              6587355a8c4f        2 months ago        176 MB
busybox                                            latest              7968321274dc        3 months ago        1.11 MB
ubuntu                                             latest              104bec311bcd        3 months ago        129 MB
hcscompany/clock                                   latest              12068b93616f        2 years ago         2.43 MB
```

<br />
<br />

### **Step2:** Searching for images

We cannot list *all* images on a remote registry, but we can search for a specific keyword:
```
$ podman search zookeeper
NAME                          DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
jplock/zookeeper              Builds a docker image for Zookeeper versio...   159                  [OK]
zookeeper                     Apache ZooKeeper is an open-source server ...   132                  [OK]
mesoscloud/zookeeper          ZooKeeper                                       70                   [OK]
digitalwonderland/zookeeper   Latest Zookeeper - clusterable                  12                   [OK]
springxd/zookeeper            A Docker image that can run a ZooKeeper se...   6                    [OK]
elevy/zookeeper               ZooKeeper configured to execute an ensembl...   6                    [OK]
debezium/zookeeper            Zookeeper image required when running the ...   3                    [OK]
```
-   "Stars" indicate the popularity of the image.
-   "Official" images are those in the root namespace.
-   "Automated" images are built automatically by the Docker Hub.
    (This means that their build recipe is always available.)

<br />
<br />

### **Step 3:** Downloading images

There are two ways to download images.

-   Explicitly, with `podman pull`.
-   Implicitly, when executing `podman run` and the image is not found locally.

Lets pulling an image:
```
$ podman pull debian:jessie
jessie: Pulling from library/debian
6d827a3ef358: Pull complete
Digest: sha256:72f784399fd2719b4cb4e16ef8e369a39dc67f53d978cd3e2e7bf4e502c7b793
Status: Downloaded newer image for debian:jessie
```
-   As seen previously, images are made up of layers.
-   podman has downloaded all the necessary layers.
-   In this example, :jessie indicates which exact version of Debian we would like. It is a *version tag*.

<br />

### Image and tags

-   Images can have tags.
-   Tags define image versions or variants.
-   `podman pull ubuntu` will refer to `ubuntu:latest`.
-   The `:latest` tag is generally updated often.

**When to (not) use tags**

Don't specify tags:
-   When doing rapid testing and prototyping.
-   When experimenting.
-   When you want the latest version.

Do specify tags:
-   When recording a procedure into a script.
-   When going to production.
-   To ensure that the same version will be used everywhere.
-   To ensure repeatability later.

In the next lab we tag an image. 

<br />

### Section summary

We've learned how to:
-   Understand images and layers.
-   Understand container image namespacing.
-   Search and download images.

[back](container_workshop.md)