<!---
{
  "id": "21351032-2a40-4101-8c29-513fdaf37365",
  "teaches": "Running Commands in a Background Docker-Container",
  "depends_on": ["24b25804-5bb5-443f-a78a-1bd485bebed8"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-05-14",
  "keywords": ["docker", "background container", "exec", "bash", "volume mount", "startup script"]
}
--->

# Running Commands in a Background Docker-Container

> In this exercise you will learn how to manage and execute commands inside a Docker container running in the background. Furthermore we will explore how to use Docker volumes and Dockerfile instructions to control container behavior.

## Introduction

Docker provides a lightweight and consistent environment for running applications. Often, containers are run in detached mode, meaning they run in the background without tying up your terminal. This makes Docker especially powerful for long-running services or background processes. However, this setup requires some understanding of how to interact with containers after they’ve started. This exercise will build on your prior knowledge of Docker by exploring how to interact with background containers using the `exec` command, how to prepare containers with pre-configured commands via Dockerfiles, and how to mount host scripts into containers for execution.

You will learn how to:

* Start a Docker container in detached mode.
* Use `docker exec` to run interactive commands like `bash` inside a container.
* Mount an external script into a running container and execute it.

These skills are fundamental for scripting, debugging, and building automated environments in containerized systems.

### Further Readings and Other Sources

* [Docker Official Documentation: docker exec](https://docs.docker.com/engine/reference/commandline/exec/)
* [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
* [YouTube: Docker Volumes Explained](https://www.youtube.com/watch?v=pTFZFxd4hOI)
* [Introduction to Docker - O’Reilly Book](https://learning.oreilly.com/library/view/docker-up-running/9781491917572/)

## Tasks

### Task 1: Run a Container in the Background

1. Start with a basic image like `ubuntu`:

   ```bash
   docker run -dit --name mycontainer ubuntu
   ```

   The flags used here are:

   * `-d`: detached mode, runs the container in the background
   * `-i`: keeps STDIN open
   * `-t`: allocates a pseudo-TTY
   * `--name mycontainer`: assigns a specific name to the container

   This command will create and start a new container named `mycontainer` based on the `ubuntu` image. It will remain running in the background, waiting for further interaction.

2. Use `docker ps` to verify the container is running:

   ```bash
   docker ps
   ```

   This command lists all running containers. You should see `mycontainer` listed with the status "Up".

   You now have a full Ubuntu system running within that container, ready to accept commands or run processes.

### Task 2: Use `exec` to Interact with the Container

1. Attach to the container with an interactive terminal:

   ```bash
   docker exec -it mycontainer bash
   ```

   This command runs a new Bash session inside the already running container. The `-it` combination provides an interactive terminal so you can input commands directly.

2. Run some commands inside:

   ```bash
   echo "Hello from inside the container!"
   ls /
   ```

   These commands will demonstrate the environment inside the container. The first command prints a message to the terminal. The second lists the contents of the root directory, showing the filesystem typical of an Ubuntu system.

3. Type `exit` to leave the container shell. This will close the Bash session but the container itself will continue running in the background.

### Task 3: Mount an External Script

1. On your host machine, create a simple shell script:

   ```bash
   echo -e "#!/bin/bash\necho Hello from the volume-mounted script" > hostscript.sh
   chmod +x hostscript.sh
   ```

   This script prints a message to the terminal. Make sure it is executable so that the container can run it.

2. Start a container and mount the script into it using the `-v` option:

   ```bash
   docker run --rm -v $(pwd)/hostscript.sh:/mnt/hostscript.sh -it ubuntu bash
   ```

   * `--rm`: automatically remove the container when it exits.
   * `-v $(pwd)/hostscript.sh:/mnt/hostscript.sh`: mounts the host script to `/mnt/hostscript.sh` in the container.

3. Inside the container, run the mounted script:

   ```bash
   /mnt/hostscript.sh
   ```

   You should see the message "Hello from the volume-mounted script" printed.

4. You can modify the host script and rerun the container to see changes without rebuilding an image. This method is ideal for development workflows or testing scripts in an isolated environment.

5. Type `exit` to leave the container. Since we used `--rm`, the container will be removed automatically.

### Task 4: Cleanup

1. Stop and remove the container if still running:

   ```bash
   docker stop mycontainer
   docker rm mycontainer
   ```

## Questions

1. What is the purpose of running a container in detached mode?
2. How does `docker exec -it` differ from `docker run -it`?
3. Why might mounting scripts from the host be preferable to embedding them in the image?
4. What are the benefits and risks of mounting host volumes into containers?
5. How can you inspect the output of a command run in a background container?

## Advice

Understanding how to interact with Docker containers post-deployment is essential for real-world usage. Whether for debugging, adding quick fixes, or deploying configurations, these methods offer flexibility and control. Don’t hesitate to experiment with different commands and mounts. Try editing your host-mounted scripts and rerunning containers to see how quickly changes propagate. Also, check the logs using `docker logs` to track output from background containers. If you feel unsure about image building or mounting, revisit the foundational sheet on [hello world in Docker](https://github.com/STEMgraph/24b25804-5bb5-443f-a78a-1bd485bebed8) to strengthen your core understanding before proceeding further.
