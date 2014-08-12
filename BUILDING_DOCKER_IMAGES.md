# Building Docker Images
This repository comes with a `Rakefile` that is used to build the official
`chef-container` Docker images. There are four commands:
* `docker:build`
* `docker:build_all`
* `docker:release`
* `docker:release_all`

## `docker:build`
Use the `build` command when you want to build a single image locally
but dont want to upload it to the Docker Hub. The `build` command accepts
two parameters: `image_name` and `version`.
* `image_name` is the name of the
`chef-container` image you wish to build. It must correspond with a folder
inside the `Dockerfiles` folder. If no value is specified, it will default
to `ubuntu-12.04`.
* `version` is the version of `chef-client` you wish to install. If no value
is specified, it will default to `latest`.

To use the command, you simply type this command:
```shell
$ rake docker:build IMAGE_NAME VERSION
```

Here are some examples:

* Build the ubuntu-12.04 box with the latest version of chef-client
```shell
$ rake docker:build[ubuntu-12.04]
```

* Build the centos-6 box with version 11.14.2 of chef-client
```shell
$ rake docker:build[centos-6,11.14.2]
```

## `docker:build_all`
The `build_all` command will build each image that is available in the
Dockerfiles directory. It accepts one parameter: `version`.

To build each image with the latest version of Chef Container, use this command:
```shell
$ rake docker:build_all
```

To build each image with a specific version of Chef Container (11.12.8),
use this command:
```shell
$ rake docker:build_all[11.12.8]
```

# `docker:release`
The `release` command will upload the specified image to the Docker Hub. It
accepts one parameter: `image_name`.

To release all versions of the ubuntu-12.04 image, use this command:
```shell
$ rake docker:release[ubuntu-12.04]
```

To release only a specific version of the ubuntu-12.04 image, use this style of
command:
```shell
$ rake docker:release[ubuntu-12.04:11.12]
```

# `docker:release_all`
The `release_all` command will release all images that are currently available
int the Dockerfiles directory. It does not accept any parameters.

```shell
$ rake docker:release_all
```
