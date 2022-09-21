# Container Workshop

This container workshop will teach you the basics of creating containers. For this workshop we'l be using podman, podman is a simple management tool for pods, containers and images.

Podman is a single-process architecture, where Docker is a client-server application.
Docker needs to have a daemon running on every host that needs to run containers, where Podman doesn't. This is why podman is preferred.

With containarizations we use a lot of pre-build images, so we don't have to build the basic images. We use these basic images to build our own images and include our modifications.

Here we see examples of private and public registries:
- **Docker Hub Registry**
  - collection of public images
- **Quay.io** 
  - collection of public/private images
- **Red Hat registry**
  - collection of private images

**The workshop contains 9 lab's, who will guide us through the basics of containers.**

* [Lab 1: Our First Containers](#lab-1-our-first-containers)
* [Lab 2: Background Containers](#lab-2-background-containers)
* [Lab 3: Restarting and Attaching to Containers](#lab-3-restarting-and-attaching-to-containers)
* [Lab 4: Understanding Container Images](#lab-4-understanding-container-images)
* [Lab 5: Building Images Interactively](#lab-5-building-images-interactively)
* [Lab 6: Building Podman Images](#lab-6-building-images-with-a-containerfile)
* [Lab 7: CMD and ENTRYPOINT](#lab-7-cmd-and-entrypoint)
* [Lab 8: Copying files during the build](#lab-8-copying-files-during-the-build)
* [Lab 9: Operations with images](#lab-9-operations-with-images)

<br />
<br />

## [Lab 1: Our First Containers](lab1.md)

### Objectives

At the end of this lab, you will have:
- Seen Podman in action.
- Started your first containers.

<br />

## [Lab 2: Background Containers](lab2.md)

### Objectives

Our first containers were *interactive*.\
We will now see how to:
-   Run a non-interactive container.
-   Run a container in the background.
-   List running containers.
-   Check the logs of a container.
-   Stop a container.
-   List stopped containers.

<br />

## [Lab 3: Restarting and Attaching to Containers](lab3.md)

### Objectives

We have started containers in the foreground, and in the background. In this lab, we will see how to:
-   Put a container in the background.
-   Attach to a background container to bring it to the foreground.
-   Restart a stopped container.

<br />

## [Lab 4: Understanding Container Images](lab4.md)

### Objectives

In this lab, we will explain:
-   What is an image.
-   What is a layer.
-   The various image namespaces.
-   How to search and download images.
-   Image tags and when to use them.

<br />

## [Lab 5: Building Images Interactively](lab5.md)

### Objectives

In this lab, we will create our first container image.\
It will be a basic distribution image, but we will pre-install the package figlet.\
We will:
-   Create a container from a base image.
-   Install software manually in the container, and turn it into a new image.
-   Learn about new commands: podman commit, podman tag, and podman diff.

<br />

## [Lab 6: Building Images With A Containerfile](lab6.md)

### Objectives

We will build a container image automatically, with a containerfile.\
At the end of this lab, you will be able to:
-   Write a containerfile.
-   Build an image from a containerfile.

<br />

## [Lab 7: CMD and ENTRYPOINT](lab7.md)

### Objectives

In this lab, we will learn about two important containerfile commands: `CMD` and `ENTRYPOINT`.\
Those commands allow us to set the default command to run in a container.

<br />

## [Lab 8: Copying files during the build](lab8.md)

### Objectives

So far, we have installed things in our container images by downloading packages. We can also copy files from the *build context* to the container that we are building.

<br />

## [Lab 9: Operations with images](lab9.md)

You are already familiar with one command, `podman images`. You can also remove images, tag and untag them.
