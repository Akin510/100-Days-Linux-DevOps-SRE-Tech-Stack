# Containers — Part 3: Podman/Docker — Basic Container Lab

> **Platform:** Rocky Linux 9 (also discusses the original lecture's RHEL/CentOS examples)  
> **Focus:** Install Podman, find container images, pull images, run an Ubuntu container, and prepare an Apache HTTP image for the next lab.

## 🎯 Learning Objectives

After this lab, students should be able to:

- Explain the difference between a **container image** and a **running container**.
- Install and verify Podman.
- Identify and log in to a container registry when authentication is required.
- Search for and pull images from a registry.
- List local images and running containers.
- Start a container in detached mode with a pseudo-terminal.
- Enter a running container interactively and inspect its operating-system userspace.
- Pull an Apache HTTP Server image in preparation for the next exercise.

## 1. Install Podman

The original lecture uses `yum` and mentions the `container-tools` module. On **Rocky Linux 9**, use `dnf` to install Podman directly:

```bash
sudo dnf install -y podman
```

The lecture's original installation commands were:

```bash
# Original RHEL/CentOS lecture commands
# yum install podman
# yum module install container-tools
```

> **Rocky Linux 9 note:** The module command from older releases is not required for this lab. `podman` can be installed directly from the available Rocky Linux repositories.

Verify the installation:

```bash
podman --version
podman --help
```

If Podman displays its version and help information, the command-line tool is available.

## 2. Understand Container Registries

A **container registry** stores container images. You **pull** an image from a registry to your machine, then use it to create and run containers.

```text
Container Registry (remote)
        │
        │ podman pull
        ▼
Local Container Image
        │
        │ podman run
        ▼
Running Container
```

For some Red Hat images, authentication may be required:

```bash
podman login registry.redhat.io
```

Enter valid registry credentials when prompted. Public images on Docker Hub can often be pulled without logging in, subject to registry policies and rate limits.

## 3. Search for an Ubuntu Image

The lecture begins by searching for Ubuntu:

```bash
podman search ubuntu
```

Review the repository name, description, and other registry-provided information. **Stars/popularity alone do not prove an image is safe or official**. Prefer trusted publishers and verify the complete image reference.

For this lab, use the official Ubuntu image reference:

```text
docker.io/library/ubuntu:latest
```

> The `latest` tag is convenient for practice, but it is not a guarantee of a particular Ubuntu version. Pin a version or image digest for reproducible production deployments.

## 4. Check Existing Local Images

Before downloading anything, inspect the images already stored locally:

```bash
podman images
```

On a fresh lab VM, the list may be empty.

## 5. Pull the Ubuntu Image

```bash
podman pull docker.io/library/ubuntu:latest
```

Verify the download:

```bash
podman images
```

The output identifies the repository, tag, image ID, creation time, and size. **Pulling an image does not start a container.**

## 6. Check Running Containers

```bash
podman ps
```

If no containers are running, the output contains no container entries. To see **all** containers, including stopped ones:

```bash
podman ps -a
```

## 7. Start an Ubuntu Container

The lecture uses `-dt` to run a Bash shell in the background with a pseudo-terminal:

```bash
podman run -dt --name ubuntu-lab docker.io/library/ubuntu:latest /bin/bash
```

| Option | Meaning |
|---|---|
| `run` | Create and start a new container from an image |
| `-d` | Detached: run in the background |
| `-t` | Allocate a pseudo-terminal (TTY) |
| `--name ubuntu-lab` | Give the container an easy-to-remember name |
| `/bin/bash` | Run Bash as the container's main process |

Podman returns a container ID. Verify the container is running:

```bash
podman ps
```

> `podman ps` shows running containers, not a list of Podman daemon processes. Podman is daemonless.

## 8. Enter the Running Ubuntu Container

Use `podman exec` with interactive input and a pseudo-terminal:

```bash
podman exec -it ubuntu-lab /bin/bash
```

| Option | Meaning |
|---|---|
| `exec` | Run a command inside an existing running container |
| `-i` | Keep standard input open |
| `-t` | Allocate a pseudo-terminal |
| `/bin/bash` | Start an interactive Bash session |

Inside the container, inspect the Ubuntu userspace:

```bash
cat /etc/os-release
cat /etc/issue
hostname
uname -a
```

**Important distinction:** `/etc/os-release` and `/etc/issue` describe the container image's distribution. `uname -a` reports the **host's shared Linux kernel**, not a separate Ubuntu kernel.

To leave the interactive shell without stopping the container:

```bash
exit
```

Back on the Rocky Linux host, confirm the container is still running:

```bash
podman ps
```

## 9. Search for an Apache HTTP Server Image

The next demonstration searches for an image containing Apache HTTP Server:

```bash
podman search httpd
```

The original lecture discusses a CentOS-based HTTPD image. For a straightforward Rocky Linux 9 lab, use the official Apache HTTP Server image on Docker Hub:

```text
docker.io/library/httpd:2.4
```

> This is a container image for the Apache HTTP Server application, not a complete CentOS or Rocky Linux virtual machine.

## 10. Pull the Apache HTTP Server Image

```bash
podman pull docker.io/library/httpd:2.4
```

Verify that both Ubuntu and HTTPD images are present:

```bash
podman images
```

Now compare images with running containers:

```bash
podman ps
```

**Expected concept:** Both images are stored locally, but only the Ubuntu container has been started. The Apache image is ready for the next lab.

## 11. Complete Lab Flow

```text
Rocky Linux 9 Host
        │
        ▼
Install Podman
        │
        ▼
Search Ubuntu Image
        │
        ▼
Pull Ubuntu Image
        │
        ▼
List Local Images
        │
        ▼
Run Ubuntu Container (-dt)
        │
        ▼
View Running Containers (podman ps)
        │
        ▼
Enter Container (podman exec -it)
        │
        ▼
Inspect Ubuntu Userspace
        │
        ▼
Exit to Host
        │
        ▼
Search Apache HTTPD Image
        │
        ▼
Pull HTTPD Image
        │
        ▼
Next Lab: Run and Configure Apache Container
```

## 12. Command Reference

| Command | Purpose |
|---|---|
| `dnf install -y podman` | Install Podman on Rocky Linux 9 |
| `podman --help` | Display Podman command help |
| `podman login registry.redhat.io` | Authenticate to Red Hat's registry |
| `podman search ubuntu` | Search for Ubuntu images |
| `podman images` | List locally stored images |
| `podman pull docker.io/library/ubuntu:latest` | Download the Ubuntu image |
| `podman ps` | List running containers |
| `podman ps -a` | List running and stopped containers |
| `podman run -dt --name ubuntu-lab docker.io/library/ubuntu:latest /bin/bash` | Start Ubuntu in the background |
| `podman exec -it ubuntu-lab /bin/bash` | Open a shell in the running container |
| `cat /etc/os-release` | Identify the container's Linux distribution |
| `podman search httpd` | Search for Apache HTTP Server images |
| `podman pull docker.io/library/httpd:2.4` | Download an Apache HTTP Server image |

## 🔧 Troubleshooting

| Problem | What to Check |
|---|---|
| `podman: command not found` | Install Podman with `dnf` and check `podman --version` |
| Image pull fails | Verify network connectivity, image name, registry access, and authentication requirements |
| `podman ps` is empty | Confirm that a container was started; inspect `podman ps -a` |
| Container exited | Inspect `podman ps -a` and `podman logs ubuntu-lab` |
| Cannot enter container | Verify that `ubuntu-lab` is running and that `/bin/bash` exists |
| `uname -a` shows Rocky's kernel | This is expected: Linux containers share the host kernel |

## 🧪 Knowledge Check

1. What is the difference between a container image and a container?
2. Which command lists locally downloaded images?
3. Does `podman pull` automatically start a container?
4. What do `-d`, `-i`, and `-t` mean?
5. How do you enter an existing running container?
6. Why does `uname -a` show the host kernel inside an Ubuntu container?
7. What is the purpose of a container registry?
8. After pulling the HTTPD image, is the Apache container running automatically?

## 📌 Quick Revision

- **Image:** A packaged blueprint used to create containers.
- **Container:** A running or stopped instance created from an image.
- **Registry:** A remote service for storing and distributing images.
- **`podman pull`:** Download an image.
- **`podman images`:** List local images.
- **`podman run`:** Create and start a container.
- **`podman ps`:** List running containers.
- **`podman exec -it`:** Run an interactive command inside a running container.

## ⭐ Golden Rule

**Search → Pull → Run → Verify → Enter**

Downloading an image is **not** the same as running a container. The next lesson will start the Apache HTTP Server container and explore additional runtime options.
