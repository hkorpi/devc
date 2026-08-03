# Development Tools Container

Run your development tools inside a dedicated container and use them as if they were native command-line tools in your local environment.

## Benefits

* **Security** – Node.js, Python, and other tools are isolated inside the container. They can access only the files and resources that you explicitly mount.
* **Networking** – CLI tools can communicate over the same Docker network as your development services defined with Docker Compose.

## Usage

Define your development environment using the following files:

* **`dev.dockerfile`** – A Dockerfile that builds an image containing your development tools and specifies how they should be run.
* **`dev.options`** – Additional `docker run` options for your local environment, such as mounting your home directory, Maven repository, or other resources.

Add the defined commands to your shell environment:

```sh
source init <name>
```

## Example

The following example makes the `node` command available in your shell:

```sh
cat > dev.dockerfile <<'EOF'
FROM node
USER node
LABEL dev.network="none"
LABEL dev.cmds="node"
EOF

source init test
node --version
```

In this example, the `node` command has no network access and can access only the current working directory.
