[back](container_workshop.md)
## Lab 6: Building Images With A Dockerfile

### Objectives

We will build a container image automatically, with a Dockerfile.

At the end of this lab, you will be able to:

-   Write a Dockerfile.

-   Build an image from a Dockerfile.

** Dockerfile overview

-   A Dockerfile is a build recipe for a Docker image.

-   It contains a series of instructions telling Docker how an image is constructed.

-   The docker build command builds an image from a Dockerfile.

### Step 1: Writing our first Dockerfile

Our Dockerfile must be in a **new, empty directory**.

1.  Create a directory to hold our Dockerfile. 
```
$ mkdir myimage
```
2.  Create a Dockerfile inside this directory.
```
 $ cd myimage
 $ vim Dockerfile
```
Of course, you can use any other editor of your choice, if you are using a Windows device.

Type this into our Dockerfile...
```
FROM ubuntu
RUN apt-get update
RUN apt-get install figlet
```

-   FROM indicates the base image for our build.

-   Each RUN line will be executed by Docker during the build.

-   Our RUN commands **must be non-interactive.**
    (No input can be provided to Docker during the build.)

-   In many cases, we will add the -y flag to apt-get.

### Step2: Build it!

Save our file, then execute:
```
 $ docker build -t figlet:v1 .
```
-  `-t` indicates the tag to apply to the image.

-   `.` indicates the location of the *build context*.

-   `:v1` tag it with a version number.

 (We will talk more about the build context later; but to keep things simple: this is the directory where our Dockerfile is located.)

What happens when we build the image?

The output of docker build looks like this:
```
 $ docker build -t figlet:v1 .
 Sending build context to Docker daemon 2.048 kB Sending build context to Docker daemon
 Step 0 : FROM ubuntu
 ---> e54ca5efa2e9
 Step 1 : RUN apt-get update
 ---> Running in 840cb3533193
 ---> 7257c37726a1
 Removing intermediate container 840cb3533193 Step 2 : RUN apt-get install figlet
 ---> Running in 2b44df762a2f
 ---> f9e8f1642759
 Removing intermediate container 2b44df762a2f Successfully built f9e8f1642759
```
-   The output of the RUN commands has been omitted.

-   Let's explain what this output means.

```
Sending build context to Docker daemon 2.048 kB
```
-   The build context is the `.` directory given to `docker build`.

-   It is sent (as an archive) by the Docker client to the Docker daemon.

-   This allows to use a remote machine to build using local files.

-   Be careful (or patient) if that directory is big and your link is slow.

**Executing each step**

```
Step 1 : RUN apt-get update

---> Running in 840cb3533193 (...output of the RUN command...)

 ---> 7257c37726a1

Removing intermediate container 840cb3533193
```

-   A container (840cb3533193) is created from the base image.

-   The RUN command is executed in this container.

-   The container is committed into an image (7257c37726a1).

-   The build container (840cb3533193) is removed.

-   The output of this step will be the base image for the next one.

**The caching system**

If you run the same build again, it will be instantaneous.

Why?

-   After each build step, Docker takes a snapshot of the resulting image.

-   Before executing a step, Docker checks if it has already built the same sequence.

-   Docker uses the exact strings defined in your Dockerfile, so:

-   `RUN apt-get install figlet cowsay` is different from

    * `RUN apt-get install cowsay figlet`

    * `RUN apt-get update` is not re-executed when the mirrors are updated

You can force a rebuild with `docker build --no-cache` ....

### Step 3: Running the image

The resulting image is not different from the one produced manually.
```
 $ docker run -ti figlet:v1
 root@91f3c974c9a1:/# figlet hello
     _          _ _
    | |__   ___| | | ___
    |  _ \ / _ \ | |/ _ \
    | | | |  __/ | | (_) |
    |_| |_|\___|_|_|\___/
```
-   Sweet is the taste of success!


 Using image and viewing history

The history command lists all the layers composing an image.

For each layer, it shows its creation time, size, and creation command.

When an image was built with a Dockerfile, each layer corresponds to a line of the Dockerfile.
```
$ docker history figlet:v1

<OUTPUT>
```
### Step 4: Introducing JSON syntax

Most Dockerfile arguments can be passed in two forms:

-   plain string:

    `RUN apt-get install figlet`

-   JSON list:

    `RUN ["apt-get", "install", "figlet"]`

Let's change our Dockerfile as follows!

```
 FROM ubuntu

 RUN apt-get update

 RUN ["apt-get", "install", "figlet"]
```

Then build the new Dockerfile.
```
 $ docker build -t figlet:v2 .
 <output>
```

**JSON syntax vs string syntax**

Compare the new history:
```
$ docker history figlet:v2
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
b7fa8e20196b        2 days ago          /bin/sh -c apt-get install figlet               1.02 MB
f4e91ba1a062        2 days ago          /bin/sh -c apt-get update                       40.2 MB
104bec311bcd        3 months ago        /bin/sh -c #(nop)  CMD ["/bin/bash"]            0 B
<missing>           3 months ago        /bin/sh -c mkdir -p /run/systemd && echo '...   7 B
<missing>           3 months ago        /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   1.9 kB
<missing>           3 months ago        /bin/sh -c rm -rf /var/lib/apt/lists/*          0 B
<missing>           3 months ago        /bin/sh -c set -xe   && echo '#!/bin/sh' >...   745 B
<missing>           3 months ago        /bin/sh -c #(nop) ADD file:7529d28035b43a2...   129 MB

```
-   JSON syntax specifies an *exact* command to execute.

-   String syntax specifies a command to be wrapped within `/bin/sh -c "..."`.

[back](container_workshop.md)