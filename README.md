# OpenStack CLI

Use a Docker image for OpenStack client tooling! [![Docker Repository on Quay](https://quay.io/repository/rackspace/openstack-cli/status "Docker Repository on Quay")](https://quay.io/repository/rackspace/openstack-cli)

## Quick start

Download the [openrc.sh file](https://docs.openstack.org/user-guide/common/cli-set-environment-variables-using-openstack-rc.html) from Horizon in your OpenStack Cloud.

```bash
source openrc.sh

# ${PWD} is mounted to allow for actions requiring host filesystem access.
# See 'Tips' section below
docker run -it --rm --volume ${PWD}:/data --env-file <(env | grep OS_) quay.io/rackspace/openstack-cli
/ # openstack --version
/ # openstack server list
/ # openstack volume list
```

## Tips

### Openstack Clusters with Self-Signed CA certs

If your cluster is using a self signed certificate for the API endpoints, you can provide that when you run the container:

* `mkdir certs` in the root of this repo
* `cp path/to/my/ca.crt certs/ca.crt`
* `docker run -it --rm --volume ${PWD}:/data -e OS_CACERT=/data/certs/ca.crt --env-file <(env | grep OS_) quay.io/rackspace/openstack-cli`

### Accessing a host directory

The `/data` directory is exposed as a volume that can be mounted.  This is convenient for OpenStack
commands that might require reading/writing host filesystems.  It is important to remember that
commands such as `openstack image save` should ensure that the location where the image is saved is
in the `/data` folder when using the `--rm` command line option.  Example:

```bash
source openrc.sh

docker run -it --rm \
  --volume ${PWD}:/data \
  --env-file <(env | grep OS_) \
  quay.io/rackspace/openstack-cli \
  openstack image save --file /data/test_image.img ${IMAGE_GUID}
```

### Run one-off commands

Run individual commands easily by passing them as the command to run and overriding the default `/bin/sh` command.  For one-off commands, it's a good practice to remove the container with the `--rm` argument so that you don't collect a bunch of orphaned containers.

```bash
source openrc.sh

docker run -it --rm \
  --volume ${PWD}:/data \
  --env-file <(env | grep OS_) \
  quay.io/rackspace/openstack-cli \
  openstack volume list
```

### Simplify your typing with aliases

```bash
source openrc.sh

# Get into a shell to run openstack commands
alias osc='docker run -it --rm --volume ${PWD}:/data --env-file <(env | grep OS_) quay.io/rackspace/openstack-cli'

# Make it look like you're running openstack locally
alias openstack='osc openstack'
```

## Update the OpenStack CLI version

Submit a PR with an update to the version in the [Dockerfile](Dockerfile). Once the PR merges, the `master` tag images at [quay.io/rackspace/openstack-cli](https://quay.io/rackspace/openstack-cli) will automatically be updated to that version.

To also update the `latest` tag do the following:

1. Go to the [tags page](https://quay.io/repository/rackspace/openstack-cli?tab=tags).
1. Click the gear icon for the `master` tag.
1. Choose Add New Tag.
1. Enter `latest` and click Move Tag.

## Inspired by

https://github.com/jmcvea/docker-openstack-client
