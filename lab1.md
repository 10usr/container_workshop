[back](container_workshop.md)
## Lab 1: Our First Containers

<br />

### Objectives

At the end of this lab, you will have:

- Seen Podman in action.
- Started your first containers.

### Podman architecture vs Docker architecture
Podman is a single-process architecture, where Docker is a client-server application.
Docker needs to have a daemon running on every host that needs to run containers, where Podman doesn't. This is why podman is preferred.

### Registries
With containarizations we use a lot of pre-build images, so we don't have to build the basic images. We use these basic images to build our own images and include our modifications. Here we can see examples of private and public registries:
 
- **Docker Hub Registry**
  - collection of public images
- **Quay.io** 
  - collection of public/private images
- **Red Hat registry**
  - collection of private images

<br />
<br />

### **Step 1:** Hello World

To run you'r first container with podman, just run the following command:
```
$ podman run busybox echo hello world
hello world
```
That was our first container!

- We used one of the smallest, simplest images available: busybox.
- busybox is typically used in embedded systems (phones, routers...)
- We ran a single process and echo'ed hello world.

<br />
<br />

### **Step 2:** A more useful container

Let's run a more exciting container:
```
$ podman run -it ubuntu
```
- This is a brand new container.
- It runs a bare-bones, no-frills ubuntu system.
- `-it` is shorthand for `-i -t`.
- `-i` tells podman to connect us to the container's stdin.
- `-t` tells podman that we want a pseudo-terminal.

Do something in our container.\
Try to run figlet in our container.
```
root@04c0bb0a6c07:/# figlet hello
bash: figlet: command not found
```
Alright, we need to install it.

**An observation**

Let's check how many packages are installed here.
```
root@04c0bb0a6c07:/# dpkg -l | wc -l 
189
```
- `dpkg -l` lists the packages installed in our container
- `wc -l` counts them
- If you have a Debian or Ubuntu machine, you can run the same command and compare the results.


We want figlet, so let's install it:
```
root@04c0bb0a6c07:/# apt-get update
...

Fetched 1514 kB in 14s (103 kB/s) 
Reading package lists... Done

root@04c0bb0a6c07:/# apt-get install figlet
Reading package lists... Done

...
```
One minute later, figlet is installed!
```
 # figlet hello
     _          _ _
    | |__   ___| | | ___
    |  _ \ / _ \ | |/ _ \
    | | | |  __/ | | (_) |
    |_| |_|\___|_|_|\___/
```

Exiting our container\
Just exit the shell, like you would usually do
(e.g. with ^D or exit)
```
 root@04c0bb0a6c07:/# exit
```
-   Our container is now in a *stopped* state.
-   It still exists on disk, but all compute resources have been freed up.

What if we start a new container, and try to run figlet again?
```
$ podman run -it ubuntu 
root@b13c164401fb:/# figlet 
bash: figlet: command not found
```
-   We started a *brand new container*.
-   The basic Ubuntu image was used, and figlet is not here.
-   We will see in the next chapters how to bake a custom image with figlet.

[back](container_workshop.md)
