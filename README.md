# Wails Build Cross platform

This repository provides an image container for Podman and Docker (or any other container engine as `nerdctl`)
to build Wails applications for different platforms.

It is initially created to be able to compile Windows binaries and installer from Linux. But, it may also work
inside Windows.

## Usage

The OCI image can be pulled with Podman or Docker, or with others OCI runner:

- `podman pull ghcr.io/metal3d/wails-build:main`
- `docker pull ghcr.io/metal3d/wails-build:main`

Then, go to the root of your application. Then launch the command:

```bash
# With podman or docker
podman run --rm -v $(pwd):/app:z --userns=keep-id ghcr.io/metal3d/wails-build:main linux
podman run --rm -v $(pwd):/app:z --userns=keep-id ghcr.io/metal3d/wails-build:main windows
# Warning, with Docker, the binary will have 1000:1000 ownership, you will need to fix this
# with chown command
docker run --rm -v $(pwd):/app:z ghcr.io/metal3d/wails-build:main linux

# if you want to add options, for example to compress the binary with UPX
# and create the installer with NSIS
podman run --rm -v $(pwd):/app:z --userns=keep-id ghcr.io/metal3d/wails-build:main windows -nsis -upx
```

Binaries will be created on the `build/bin` directory of your project.

- please, don't forget `:Z` suffix in volumes to be compatible with SELinux distributions like Fedora or CentOS.
- the `--userns` option is useful for rootless container engines like Podman to keep the user ID of the output files. It
  is possible to make [the same with Docker](https://docs.docker.com/engine/security/userns-remap/) but I didn't tried.

## Windows consideration

You need to build once for Linux first. This generates bindings. I cannot, at this time, find a way to build them. The
main problem is that `wails` wants to use a binary for the target platform to generate the bindings. At this time,
my script skips the generation of bindings for Windows.
