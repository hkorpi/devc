Development tools container
===

Put your development tools to an application container and use them like any other command line tool in your environment.

Benefits
- Security - Node/python/etc worms can only access to the resources you explicitly mount to the container
- Network - CLI tools can now work in the same network as your development services defined with docker compose

Usage
---

Define you development tools in using 
- **dev.dockerfile** A dockerfile which defines an image containing tools and specifications how to run them
- **dev.options** Extra docker run options - how to run these tools in your local environment e.g. mount user home and other resources like maven repositories

Add defined commands to your shell environment:
```
source init <name>
```

Example
---

Here is a simple example to get node command to your shell environment.
```
echo 'FROM node
USER node
LABEL dev.network="none"
LABEL dev.cmds="node"' > 'dev.dockerfile'
source init test
node --version
```
Here node command does not have any network access and can only access current directory.