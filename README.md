# Pritunl for Docker

Docker image that should be used to start an Pritunl server.

## Synopsis

This script will create a Docker image with Pritunl installed and with all
of the required initialisation scripts.

The Docker image resulting from this script should be the one used to
instantiate a Pritunl server.

## Getting Started

There are a couple of things needed for the script to work.

### Prerequisites

Docker, either the Community Edition (CE) or Enterprise Edition (EE), needs to
be installed on your local computer.

#### Docker

Docker installation instructions can be found
[here](https://docs.docker.com/install/).

### Usage

In order to create a Docker image using this Dockerfile you need to run the
`docker` command with a few options.

```
docker build --squash --force-rm --no-cache --quiet --tag <USER>/<IMAGE>:<TAG> <PATH>
```

* `<USER>` - *[required]* The user that will own the container image (e.g.: "johndoe").
* `<IMAGE>` - *[required]* The container name (e.g.: "pritunl").
* `<TAG>` - *[required]* The container tag (e.g.: "latest").
* `<PATH>` - *[required]* The location of the Dockerfile folder.

A build example:

```
docker build --squash --force-rm --no-cache --quiet --tag johndoe/my_pritunl:latest .
```

To clean the _<none>_ image left by the `--squash` option the following command
can be used:

```
docker rmi `docker images --filter "dangling=true" --quiet`
```

### Instantiate a Container

In order to end up with a functional Pritunl service - after having build
the container - some configurations have to be performed.

To help perform those configurations a small set of commands is included on the
Docker container.

- `help` - Usage help.
- `init` - Configure the Pritunl service.
- `start` - Start the Pritunl service.

To store the configuration settings of the Pritunl server as well as the users
A couple of volumes should be created and added the the container when running
the same.

#### Creating Volumes

To be able to make all of the Pritunl data persistent, the same will have to
be stored on a different volume.

Creating volumes can be done using the `docker` tool. To create a volume use
the following command:

```
docker volume create --name <VOLUME_NAME>
```

Two create the two required volumes the following set of commands can be used:

```
docker volume create --name my_pritunl
```

#### Configuring the Pritunl Server

To configure the Pritunl server the `init` command must be used.

```
docker run --volume <PRITUNL_VOL>:/data/pritunl:rw --rm <USER>/<IMAGE>:<TAG> [options] init
```

* `-m <URI>` - *[required]* The MongoDB URI (e.g.: mongodb://mongodb.host:27017/pritunl).

After this step the Pritunl server should be configured and ready to use.

An example on how to configure the Pritunl server:

```
docker run --volume my_pritunl:/data/pritunl:rw --rm johndoe/my_pritunl:latest -m mongodb://mongodb:27017/pritunl init
```

#### Start the Pritunl Server

After configuring the Pritunl server the same can now be started.

Starting the Pritunl server can be done with the `start` command.

```
docker run --volume <PRITUNL_VOL>:/data/pritunl:rw --detach --interactive --tty -p 1194:1194/udp -p 1194:1194 -p 443:443 -p 80:80 --privileged --cap-add=NET_ADMIN --device=/dev/net/tun <USER>/<IMAGE>:<TAG> start
```

The Docker options `--cap-add=NET_ADMIN`, `--device=/dev/net/tun`, and
`--privileged` are required for the container to be able to start.

To help managing the container and the Pritunl instance a name can be given to
the container. To do this use the `--name <NAME>` docker option when starting
the server   

An example on how the Pritunl service can be started:

```
docker run --volume my_pritunl:/data/pritunl:rw --detach --interactive --tty -p 1194:1194/udp -p 1194:1194 -p 443:443 -p 80:80 --privileged --cap-add=NET_ADMIN --device=/dev/net/tun --name my_pritunl johndoe/my_pritunl:latest start
```

To see the output of the container that was started use the following command:

```
docker attach <CONTAINER_ID>
```

Use the `ctrl+p` `ctrl+q` command sequence to detach from the container.

#### Stop the Pritunl Server

If needed the Pritunl server can be stoped and later started again (as long as
the command used to perform the initial start was as indicated before).

To stop the server use the following command:

```
docker stop <CONTAINER_ID>
```

To start the server again use the following command:

```
docker start <CONTAINER_ID>
```

### Pritunl Status

The Pritunl server status can be check by looking at the Unbound server output
data using the docker command:

```
docker container logs <CONTAINER_ID>
```

### Add Tags to the Docker Image

Additional tags can be added to the image using the following command:

```
docker tag <image_id> <user>/<image>:<extra_tag>
```

### Push the image to Docker Hub

After adding an image to Docker, that image can be pushed to a Docker
registry... Like Docker Hub.

Make sure that you are logged in to the service.

```
docker login
```

When logged in, an image can be pushed using the following command:

```
docker push <user>/<image>:<tag>
```

Extra tags can also be pushed.

```
docker push <user>/<image>:<extra_tag>
```

## Contributing

1. Fork it!
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request

Please read the [CONTRIBUTING.md](CONTRIBUTING.md) file for more details on how
to contribute to this project.

## Versioning

This project uses [SemVer](http://semver.org/) for versioning. For the versions
available, see the [tags on this repository](https://github.com/fscm/docker-pritunl/tags).

## Authors

* **Frederico Martins** - [fscm](https://github.com/fscm)

See also the list of [contributors](https://github.com/fscm/docker-pritunl/contributors)
who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE)
file for details
