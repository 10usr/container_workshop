[back](container_workshop.md)
## Lab 5: Building Images Interactively

<br />

### Objectives

In this lab, we will create our first container image.

It will be a basic distribution image, but we will pre-install the package figlet.

We will:
-   Create a container from a base image.
-   Install software manually in the container, and turn it into a new image.
-   Learn about new commands: podman commit, podman tag, and podman diff.

<br />

**Building Images Interactively**

As we have seen, the images on the Docker Hub are sometimes very basic. How do we want to construct our own images?

As an example, we will build an image that has figlet.

First, we will do it manually with podman commit. Then, in an upcoming chapter, we will use a containerfile and podman build.

Building from a base.\
Our base will be the ubuntu image.

<br />
<br />

### Step 1: Create a new container and make some changes

Start an Ubuntu container:
```
 $ podman run -it ubuntu
 root@<yourContainerId>:\#
```
Run the command apt-get update to refresh the list of packages available to install.

Then run the command apt-get install figlet to install the program we are interested in.
```
 root@<yourContainerId>:\# apt-get update && apt-get install figlet

 .... OUTPUT OF APT-GET COMMANDS ....
```

<br />
<br />

### Step 2: Inspect the changes

Type exit at the container prompt to leave the interactive session.

Now let's run `podman diff` to see the difference between the base image and our container.
```
 $ podman diff <yourContainerId>
 C /root
 A /root/.bash\_history C /tmp
 C /usr
 C /usr/bin
 A /usr/bin/figlet
 ...
```
 podman tracks filesystem changes

As explained before:
-   An image is read-only.
-   When we make changes, they happen in a copy of the image.
-   podman can show the difference between the image, and its copy.
-   For performance, podman uses copy-on-write systems.

 (i.e. starting a container based on a big image doesn't incur a huge copy.)

<br />
<br />

### Step 3: Commit and run your image

The podman commit command will create a new layer with those changes, and a new image using this new layer.
```
 $ podman commit <yourContainerId> 
 <newImageId>
```
The output of the podman commit command will be the ID for your newly created image.

We can run this image:
```
 $ podman run -it <newImageId>
 root@fcfb62f0bfde:/# figlet hello
     _          _ _
    | |__   ___| | | ___
    |  _ \ / _ \ | |/ _ \
    | | | |  __/ | | (_) |
    |_| |_|\___|_|_|\___/
```

<br />
<br />

### Step 4: Tagging images

Referring to an image by its ID is not convenient. Let's tag it instead. We can use the tag command:
```
 $ podman tag <newImageId> figlet
```
But we can also specify the tag as an extra argument to commit:
```
 $ podman commit <containerId> figlet
```
And then run it using its tag:
```
 $ podman run -it figlet
```

<br />

**What's next?**

Manual process = bad.\
Automated process = good.

In the next lab, we will learn how to automate the build process by writing a containerfile.

[back](container_workshop.md)