[back](container_workshop.md)
## Lab 7: CMD and ENTRYPOINT

<br />

### Objectives

In this lab, we will learn about two important Containerfile commands: `CMD` and `ENTRYPOINT`.\
Those commands allow us to set the default command to run in a container.

<br />
<br />

### Step 1: Defining a default command

When people run our container, we want to greet them with a nice hello message, and using a custom font.\
For that, we will execute: `figlet -f script hello`
-   `-f` script tells figlet to use a fancy font.
-   hello is the message that we want it to display.

 Adding CMD to our Containerfile

Our new Containerfile will look like this:
```
 FROM ubuntu
 RUN apt-get update
 RUN \["apt-get", "install", "figlet"\]
 CMD figlet -f script hello
```

-   `CMD` defines a default command to run when none is given.
-   It can appear at any point in the file.
-   Each `CMD` will replace and override the previous one.
-   As a result, while you can have multiple `CMD` lines, it is useless.

<br />
<br />

### Step 2: Build and test our image

Let's build it:

```
 $ podman build -t figlet .
 ...
 Successfully built 042dff3b4a8d
```

And run it: 

```
$ podman run figlet 
```

If we want to get a shell into our container (instead of running figlet), we just have to specify a different program to run:
```
 $ podman run -it figlet bash 
 root@7ac86a641116:/
```
-   We specified bash.
-   It replaced the value of `CMD`.

<br />
<br />

### Step 3: Using ENTRYPOINT

We want to be able to specify a different message on the command line, while retaining figlet and some default parameters.\
In other words, we would like to be able to do this:
```
 $ podman run figlet salut
```

We will use the `ENTRYPOINT` verb in Containerfile.\
Adding` ENTRYPOINT` to our Containerfile

Our new Containerfile will look like this:
```
 FROM ubuntu
 RUN apt-get update
 RUN \["apt-get", "install", "figlet"\]
 ENTRYPOINT \["figlet", "-f", "script"\]
```
-   `ENTRYPOINT` defines a base command (and its parameters) for the container.
-   The command line arguments are appended to those parameters.
-   Like `CMD`, `ENTRYPOINT` can appear anywhere, and replaces the previous value.

Why did we use JSON syntax for our `ENTRYPOINT`?

 Implications of JSON vs string syntax
-   When `CMD` or `ENTRYPOINT` use string syntax, they get wrapped in sh -c.
-   To avoid this wrapping, you must use JSON syntax.

What if we used `ENTRYPOINT` with string syntax?

```
 $ podman run figlet salut
```

This would run the following command in the figlet image:
```
 sh -c "figlet -f script" salut
```
Build and test our image

Let's build it:
```
 $ podman build -t figlet .
 ...
 Successfully built 36f588918d73
```
And run it:
```
 $ podman run figlet salut
```

Great success!

<br />
<br />

### Step 4: Using CMD and ENTRYPOINT together

What if we want to define a default message for our container? Then we will use `ENTRYPOINT` and `CMD` together.
-   `ENTRYPOINT` will define the base command for our container.
-   `CMD` will define the default parameter(s) for this command.
-   They *both* have to use JSON syntax.

Our new Containerfile will look like this:
```
 FROM ubuntu
 RUN apt-get update
 RUN \["apt-get", "install", "figlet"\]
 ENTRYPOINT \["figlet", "-f", "script"\]
 CMD \["hello world"\]
```
-   `ENTRYPOINT` defines a base command (and its parameters) for the container.
-   If we don't specify extra command-line arguments when starting the container, the value of `CMD` is appended.
-   Otherwise, our extra command-line arguments are used instead of `CMD`.

<br />
<br />

### Step 5: Build and test our image

Let's build it:
```
 $ podman build -t figlet .
 ...
 Successfully built 6e0b6a048a07
```
And run it:

```
$ podman run figlet
```

```
 $ podman run figlet hola mundo
```

What if we want to run a shell in our container?

We cannot just do podman run figlet bash because that would just tell figlet to display the word "bash."

We use the --entrypoint parameter:
```
 $ podman run -it --entrypoint bash figlet 
 root@6027e44e2955
```

[back](container_workshop.md)