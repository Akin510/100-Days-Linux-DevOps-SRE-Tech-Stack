# Containers — Part 2: Implementation, Images, Podman, and Registries

> **Platform:** Rocky Linux 9 / Red Hat Enterprise Linux 9  
> **Topic:** Container fundamentals and management tools  
> **Focus:** How containers work, where images come from, and which tools manage them

## 🎯 Learning Objectives

After completing this lesson, students should be able to:

- Identify the Linux technologies used to implement containers.
- Explain the portability and reusability of containerized applications.
- Distinguish a **container image** from a **running container**.
- Describe the roles of **Podman**, **Skopeo**, and **Buildah**.
- Explain what a **container registry** is and how images are pushed and pulled.
- Recognize Red Hat container registries.

## 1. Exploring the Implementation of Containers

Linux containers rely on several kernel and security technologies working together:

| Technology | Main purpose | Beginner explanation |
|---|---|---|
| **Control groups (cgroups)** | Resource management | Control or account for how much CPU, memory, and other resources a container uses. |
| **Namespaces** | Process and resource isolation | Give containerized processes isolated views of resources such as process IDs, networking, and mounts. |
| **SELinux** | Mandatory access control | Enforces security policy boundaries for processes and files. |
| **seccomp** (secure computing mode) | System-call filtering | Restricts which system calls a containerized process can make. |

### Conceptual flow

```text
Containerized application
          │
          ├── cgroups ─── Resource controls
          ├── namespaces ─ Isolation
          ├── SELinux ─── Security policy
          └── seccomp ─── System-call filtering
          │
          ▼
    Shared Linux kernel
```

**Teaching point:** A Linux container does not need a separate full guest operating system. Containers share the host's Linux kernel while using isolation and security mechanisms.

## 2. Planning for Containers

Containers provide a convenient way to package, reuse, and move applications between environments.

For example, an application can be prepared in **development**, tested in **testing**, and deployed in **production** using the same image.

```text
Development ───► Testing ───► Production
         Same container image
```

**Key benefits discussed in this lesson:**

- **Portability:** Move an application image between compatible environments.
- **Reusability:** Start multiple containers from the same image.
- **Consistency:** Package the application and its required dependencies together.

> Portability does not eliminate all environment differences: networking, storage, architecture, permissions, and configuration may still need attention.

## 3. Running Containers from Container Images

A **container image** is a packaged template used to create containers. It can include:

- The application itself
- Required system libraries
- Programming-language runtimes
- Programming-language libraries
- Configuration settings
- Static data files

A **container** is a running or created instance of an image.

```text
Container Registry
       │
       │ Pull image
       ▼
Local Container Image
       │
       │ podman run
       ▼
Running Container
       │
       ▼
Application and dependencies
```

### Example: Apache HTTP Server

If you want to run Apache in a container, the workflow is:

1. Search for an appropriate HTTP server image.
2. Pull the image from a container registry.
3. Run a container from the image.
4. Verify that the HTTP server works.

Container images are generally treated as **immutable artifacts**: rather than modifying a published image in place, a new image is built when changes are needed. Image formats and runtimes commonly follow **Open Container Initiative (OCI)** specifications.

## 4. Managing Containers with Podman, Skopeo, and Buildah

Red Hat Enterprise Linux provides container tools that also work in Rocky Linux environments.

| Tool | Primary role | Example task |
|---|---|---|
| **Podman** | Manage container images and containers | Pull an image, run a container, inspect or stop it |
| **Skopeo** | Work with images and registries without running containers | Inspect or copy an image between registries |
| **Buildah** | Build container images | Create a new image from application content |

This module focuses on **Podman** and **Skopeo**. Building images with **Buildah** is beyond the scope of this introductory lesson.

### Example commands for a later lab

```bash
# Check the installed Podman version
podman --version

# Search configured registries for images
podman search httpd

# List locally available images
podman images

# List running containers
podman ps

# List all containers, including stopped ones
podman ps -a

# Check the installed Skopeo version
skopeo --version
```

> These commands are introductory examples. Searching may depend on configured registries, connectivity, and authentication.

## 5. Selecting Container Images and Registries

A **container registry** is a service that stores and distributes container images.

- A **developer** or image publisher **pushes** an image to a registry.
- An **end user** or administrator **pulls** the image to a host.
- A container engine such as Podman creates a container from the downloaded image.

```text
Developer / Publisher
         │
         │ PUSH
         ▼
   Container Registry
         │
         │ PULL
         ▼
 Rocky Linux 9 Host
         │
         │ RUN (Podman)
         ▼
 Running Application
```

### Red Hat registries mentioned in the lecture

| Registry | Purpose |
|---|---|
| `registry.redhat.io` | Red Hat product and certified container images; access to some content requires Red Hat authentication or entitlements. |
| `registry.connect.redhat.com` | Red Hat partner / third-party container images. |

**Rocky Linux 9 note:** You can also use suitable publicly available OCI-compatible images from other registries. Access rules vary by registry and image.

## 6. Quick Revision

| Term | Remember |
|---|---|
| **cgroups** | Manage resources |
| **Namespaces** | Isolate processes and other resources |
| **SELinux** | Enforce security policy |
| **seccomp** | Filter system calls |
| **Container image** | Packaged application template |
| **Container** | Instance created from an image |
| **Podman** | Run and manage containers |
| **Skopeo** | Inspect and copy images |
| **Buildah** | Build new images |
| **Registry** | Store and distribute images |
| **Push** | Upload an image |
| **Pull** | Download an image |

## 🧪 Knowledge Check

1. What is the difference between a container image and a container?
2. Which Linux technology manages container resource usage?
3. Which technology provides isolated views of processes and networking?
4. What security roles do SELinux and seccomp play?
5. Which tool would you use to run an existing image: Podman, Skopeo, or Buildah?
6. What is the difference between pushing and pulling an image?
7. Why is using the same container image in development and production helpful?

## ⭐ Golden Rule

**Registry stores the image → Podman pulls the image → Podman runs the container.**

```text
REGISTRY → IMAGE → CONTAINER → APPLICATION
```

**Next lesson:** Hands-on container labs using Podman and Skopeo on Rocky Linux 9.
