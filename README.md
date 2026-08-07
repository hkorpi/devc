# Development Tools Container

Run your development tools inside a dedicated container and use them as if they were native command-line tools in your local environment.

## Benefits

* **Security** – Node.js, Python, and other tools are isolated inside the container. They can access only the files and resources that you explicitly mount.
* **Networking** – CLI tools can communicate over the same Docker network as your development services defined with Docker Compose.

## Installation

Clone the repository to any location on your machine:
```sh
git clone https://github.com/hkorpi/devc.git
```
Throughout this documentation, the directory where the repository is cloned is referred to as `<devc-path>`.


## Usage

Define your development environment using the following files:

* **`dev.dockerfile`** – A Dockerfile that builds an image containing your development tools and specifies how they should be run.
* **`dev.options`** – Additional `docker run` options for your local environment, such as mounting your home directory, Maven repository, or other resources.

Add the defined commands to your shell environment:

```sh
eval $(<devc-path>/use <name>)
```

This builds a docker image: `<name>-dev` and adds commands defined in the dockerfile to your shell environment. 
The commands are sourced as posix compliant functions. 
The command function executes (`docker run`) the original command inside the container 
using runtime options interpreted from `dev.dockerfile` and directly from `dev.options`.

### Dockerfile

The development container is defined using a standard Dockerfile, as described in the [Dockerfile reference](https://docs.docker.com/reference/dockerfile/).

In addition to the standard Dockerfile instructions, `devc` recognizes the following instructions and labels to configure the runtime environment:

* **Commands**
  ```dockerfile
  LABEL dev.cmds=<cmd1>,<cmd2>,...
  ```
  A comma-separated list of commands which are exported to your shell environment from the container. A command must be executable inside the container.

* **Network**
  ```dockerfile
  LABEL dev.network=<network>
  ```
  Connects the container to the specified Docker network.

* **User**
  ```dockerfile
  USER <user>
  ```
  Specifies the user that commands will run as inside the container.

* **Workspace**
  ```dockerfile
  LABEL dev.workspace=<workspace>
  ```
  Specifies the workspace directory relative to the location of `dev.dockerfile`. This directory is mounted into the container at:
  ```
  /home/<user>/workspace
  ```
  The container working directory is your current directory inside this workspace. 
  Default workspace is '.' so default workspace directory is same where the dev.dockerfile is located.

* **Ports**

  ```dockerfile
  LABEL dev.ports=<port1>,<port2>,...
  ```
  A comma-separated list of container ports to expose on `localhost`. Each listed port is published from the container to the same port on the host.


## Example

The following example makes the `node` command available in your shell:

```sh
cat > dev.dockerfile <<'EOF'
FROM node
USER node
LABEL dev.network="none"
LABEL dev.cmds="node"
EOF

eval $(./use test)
node --version
```

In this example, the `node` command has no network access and can access only the current working directory.
