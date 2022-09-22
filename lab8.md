[back](container_workshop.md)
## Lab 8: Copying files during the build

<br />

### Objectives

So far, we have installed things in our container images by downloading packages. We can also copy files from the *build context* to the container that we are building.

Remember: the *build context* is the directory containing the Containerfile. In this chapter, we will learn a new Containerfile keyword: 

`COPY`

<br />
<br />

### Step 1: Build some C code

We want to build a container that compiles a basic "Hello world" program in C. Here is the program, hello.c:

```
 int main () 
 { puts("Hello, world!"); return 0;
 }
```
Let's create a new directory, and put the file hello.c in there.

<br />
<br />

### Step 2: Write the Containerfile.

On Debian and Ubuntu, the package build-essential will get us a compiler.

When installing it, don't forget to specify the `-y` flag, otherwise the build will fail (since the build cannot be interactive).

Then we will use `COPY` to place the source file into the container.
```
 FROM ubuntu
 RUN apt-get update
 RUN apt-get install -y build-essential
 COPY hello.c /
 RUN make hello
 CMD /hello
```

<br />
<br />

### Step 3: Create this Containerfile.

 Testing our C program
-   Create hello.c and Containerfile in the same direcotry.
-   Run `podman build -t hello .` in this directory.        
-   Run `podman run hello `, you should see Hello, world!.

Success!

<br />
<br />

### Step 4: `COPY` and the build cache
-   Run the build again.
-   Now, modify hello.c and run the build again.
-   podman can cache steps involving COPY.
-   Those steps will not be executed again if the files haven't been changed.

<br />

 **Details:**
-   You can `COPY` whole directories recursively.
-   Older Containerfiles also have the `ADD` instruction. It is similar but can automatically extract archives.
-   If we really wanted to compile C code in a compiler, we would:
    -   Place it in a different directory, with the `WORKDIR` instruction.
    -   Even better, use the gcc official image.

[back](container_workshop.md)