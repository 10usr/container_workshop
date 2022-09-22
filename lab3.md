[back](container_workshop.md)
## Lab 3: Restarting and Attaching to Containers

<br />

### Objectives

We have started containers in the foreground, and in the background. In this lab, we will see how to:
-   Put a container in the background.
-   Attach to a background container to bring it to the foreground.
-   Restart a stopped container.

<br />

### Background and foreground

The distinction between foreground and background containers is arbitrary. From containers point of view, all containers are the same.

All containers run the same way, whether there is a client attached to them or not. It is always possible to detach from a container, and to reattach to a container.

Analogy: attaching to a container is like plugging a keyboard and screen to a physical server.

<br />
<br />

### **Step 1:** Detaching from a container

-   If you have started an *interactive* container (with option `-it`), you can detach from it.
-   The "detach" sequence is ^P^Q.
-   Otherwise you can detach by killing the container.

 (But not by hitting ^C, as this would deliver SIGINT to the container.)

What does `-it` stand for?

-   `-t` means "allocate a terminal."
-   `-i` means "connect stdin to the terminal."

<br />

### Specifying a custom detach sequence

Start a interactive container with command:
```
 $ podman run -it containers.cicd.ontwikkel.local/levitate/clock
```
Detach by hitting ^P^Q.

Check that our container is still running:
```
 $ podman ps -l
```

<br />
<br />

### **Step 2:** Attaching to a container

You can attach to a container:
```
 $ podman attach <containerID>
```
-   The container must be running.
-   There *can* be multiple clients attached to the same container.

<br />

Detaching from non-interactive containers

-   **Warning:** if the container was started without `-it`...
    -   You won't be able to detach with ^P^Q.
    -   If you hit ^C, the signal will be proxied to the container.

<br />
<br />

### **Step 3:** Checking container output

- Use podman attach if you intend to send input to the container.
- If you just want to see the output of a container, use podman logs.
```
$ podman logs --tail 1 --follow <containerID>
```
 
<br />
<br />

### **Step 4:** Restarting a container

When a container has exited, it is in stopped state.

It can then be restarted with the start command.
```
 $ podman start <yourContainerID>
```
The container will be restarted using the same options you launched it with.\
You can re-attach to it if you want to interact with it:
```
 $ podman attach <yourContainerID>
```
Use `podman ps -a` to identify the container ID of a previous clock container, and try those commands.

> Attaching to a REPL
> -   REPL = Read Eval Print Loop
> -   Shells, interpreters, TUI ...
> -   Symptom: you podman attach, and see nothing
> -   The REPL doesn't know that you just attached, and doesn't print anything
> -   Try hitting ^L or Enter
> SIGWINCH
> -   When you podman attach, a couple of SIGWINCH signals are send to the container.
> -   SIGWINCH = WINdow CHange; indicates a change in window size.
> -   This will cause some CLI and TUI programs to redraw the screen.
> -   But not all of them.

[back](container_workshop.md)
