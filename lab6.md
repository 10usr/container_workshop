[back](container_workshop.md)
## Lab 6: Building Images With A Containerfile

<br />

### Objectives

We will build a container image automatically, with a Containerfile.

At the end of this lab, you will be able to:
-   Write a Containerfile.
-   Build an image from a Containerfile.

** Containerfile overview
-   A Containerfile is a build recipe for a container image.
-   It contains a series of instructions telling podman how an image is constructed.
-   The podman build command builds an image from a Containerfile.

<br />
<br />

### Step 1: Writing our first Containerfile

Our Containerfile must be in a **new, empty directory**.

1.  Create a directory to hold our Containerfile. 
```
$ mkdir myimage
```
2.  Create a Containerfile inside this directory.
```
 $ cd myimage
 $ vim Containerfile
```
Of course, you can use any other editor of your choice, if you are using a Windows device.

Type this into our Containerfile...
```
FROM ubuntu
RUN apt-get update
RUN apt-get install figlet
```

-   FROM indicates the base image for our build.
-   Each RUN line will be executed by podman during the build.
-   Our RUN commands **must be non-interactive.**
    (No input can be provided to podman during the build.)
-   In many cases, we will add the -y flag to apt-get.

<br />
<br />

### Step2: Build it!

Save our file, then execute:
```
 $ podman build -t figlet:v1 .
```
-  `-t` indicates the tag to apply to the image.
-   `.` indicates the location of the *build context*.
-   `:v1` tag it with a version number.
 (We will talk more about the build context later; but to keep things simple: this is the directory where our Containerfile is located.)

<br />

What happens when we build the image?\
The output of podman build looks like this:
```
 $ podman build -t figlet:v1 .
STEP 1/3: FROM ubuntu
STEP 2/3: RUN apt-get update
Get:1 http://archive.ubuntu.com/ubuntu jammy InRelease [270 kB]
Get:2 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
...
STEP 3/3: RUN apt-get install figlet
Reading package lists...
Building dependency tree...
Reading state information...
The following NEW packages will be installed:
  figlet
...
COMMIT figlet:v1
--> ae9e91f3e0b
Successfully tagged localhost/figlet:v1
ae9e91f3e0b68052232655ff7a6e4247dac73783bb43c53b1db2bd4ef4161451
```
-   The build context is the `.` directory given to `podman build`.

**Executing each step**

-   A container (840cb3533193) is created from the base image.
-   The RUN command is executed in this container.
-   The container is committed into an image (7257c37726a1).
-   The build container (840cb3533193) is removed.
-   The output of this step will be the base image for the next one.

**The caching system**

If you run the same build again, it will be instantaneous.

Why?
-   After each build step, podman takes a snapshot of the resulting image.
-   Before executing a step, podman checks if it has already built the same sequence.
-   podman uses the exact strings defined in your Containerfile, so:
-   `RUN apt-get install figlet cowsay` is different from

    * `RUN apt-get install cowsay figlet`
    * `RUN apt-get update` is not re-executed when the mirrors are updated

You can force a rebuild with `podman build --no-cache` ....

<br />
<br />

### Step 3: Running the image

The resulting image is not different from the one produced manually.
```
 $ podman run -ti figlet:v1
 root@91f3c974c9a1:/# figlet hello
     _          _ _
    | |__   ___| | | ___
    |  _ \ / _ \ | |/ _ \
    | | | |  __/ | | (_) |
    |_| |_|\___|_|_|\___/
```
-   Sweet is the taste of success!


Using image and viewing history.\
The history command lists all the layers composing an image.\
For each layer, it shows its creation time, size, and creation command.

When an image was built with a Containerfile, each layer corresponds to a line of the Containerfile.
```
$ podman history figlet:v1

<OUTPUT>
```

<br />
<br />

### Step 4: Introducing JSON syntax

Most Containerfile arguments can be passed in two forms:

-   plain string:\
    `RUN apt-get install figlet`
-   JSON list:\
    `RUN ["apt-get", "install", "figlet"]`

Let's change our Containerfile as follows!

```
 FROM ubuntu
 RUN apt-get update
 RUN ["apt-get", "install", "figlet"]
```

Then build the new Containerfile.
```
 $ podman build -t figlet:v2 .
 <output>
```

<br />

**JSON syntax vs string syntax**

Compare the new history:
```
$ podman history figlet:v2
ID            CREATED         CREATED BY                                     SIZE        COMMENT
9fbc597a5d70  22 seconds ago  /bin/sh -c ["apt-get", "install", "figlet"]    1.66 MB     FROM 497e92820a8d
497e92820a8d  28 minutes ago  /bin/sh -c apt-get update                      37.3 MB     FROM docker.io/library/ubuntu:latest
2dc39ba059dc  2 weeks ago     /bin/sh -c #(nop)  CMD ["bash"]                0 B         
<missing>     2 weeks ago     /bin/sh -c #(nop) ADD file:a7268f82a862198...  80.4 MB
```
-   JSON syntax specifies an *exact* command to execute.
-   String syntax specifies a command to be wrapped within `/bin/sh -c "..."`.

[back](container_workshop.md)