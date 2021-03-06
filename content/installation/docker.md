---
title: Running Humio as a Docker container
weight: 100
menuTitle: Docker
pageImage: /integrations/docker.svg
categories: ["Integration", "Platform"]
---

Humio is distributed as a Docker image. This means that you can start an
instance without a complicated installation procedure.

{{% notice tip %}}
Looking for how to get logs from Docker into Humio? Try the [Docker logging integration]({{< ref "docker-integration.md" >}}) instead.
{{% /notice %}}

**Step 1**

Create an empty file on the host machine to store the Humio configuration.
For example, `humio.conf`.

You can use this file to pass on JVM arguments to the Humio Java process.

**Step 2**

Enter the following settings into the configuration file:

```shell
HUMIO_JVM_ARGS=-Xss2M -XX:MaxDirectMemorySize=4G
```

<!--
{{% notice note %}}
These settings are for a machine with 8GB of RAM or more.
{{% /notice %}}
-->

**Step 3**

Create an empty directory on the host machine to store data for Humio:

```shell
mkdir humio-data
```

**Step 4**

Pull the latest Humio image:

```shell
docker pull humio/humio
```

**Step 5**

Run the Humio Docker image as a container:

```shell
docker run -v $HOST_DATA_DIR:/data --net=host --name=humio --ulimit="nofile=8192:8192" --env-file=$PATH_TO_CONFIG_FILE humio/humio
```

Replace `$HOST_DATA_DIR` with the path to the humio-data directory you created
on the host machine, and `$PATH_TO_CONFIG_FILE` with the path of the
configuration file you created.

**Step 6**

Humio is now running. Navigate to `http://localhost:8080` to view the Humio web interface.

{{% notice info %}}
In the above example, we started the Humio container with full access to the
network of the host machine (`--net=host`). In a production environment, you
should restrict this access by using a firewall, or adjusting the Docker network
configuration. Another possibility is to forward explicit ports.
That is possible like this: `-p 8080:8080`. But then you need to forward all
the ports you configure Humio to use. By default Humio is only using port 8080.
{{% /notice %}}


{{% notice note %}}
On a Mac there can be issues with using the host network (`--net=host`). In
that case use `-p 8080:8080` to forward port 8080 on the host network to the Docker container.  
Another concern is to allow enough memory to the virtual machine running Docker
on Mac. Open the Docker app and go to preferences and specify 4GB.
{{% /notice %}}

Updating Humio is described in the [upgrade section]({{< relref "upgrading.md" >}})

## Running Humio as a system service

The Docker container can be started as a service using Docker's [restart policies](https://docs.docker.com/engine/reference/run/#restart-policies-restart).  
An example is adding `--detach --restart=always` to the above docker run:

```shell
docker run ... --detach --restart=always
```
