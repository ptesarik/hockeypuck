# Hockeypuck

Hockeypuck is an OpenPGP public keyserver. 

# Quick start with docker-compose (for testing)

A quick and easy way to run Hockeypuck locally is with `docker-compose`.
First, obtain a keydump from an existing server.
A partial list of dump sources can be found at https://github.com/SKS-Keyserver/sks-keyserver/wiki/KeydumpSources .
Unpack the `*.gpg` files from the dump under `contrib/docker-compose/devel/keydump/`, then:

    cd contrib/docker-compose/devel
    docker-compose up -d

You can change the configuration by editing `contrib/docker-compose/devel/etc/hockeypuck.conf` and running `docker-compose restart`.
To stop, run `docker-compose down`.

# Production deployment with docker-compose (RECOMMENDED for most users)

You can use `docker-compose` to build and deploy a standalone Hockeypuck instance in a production environment.
This includes automated database population and TLS certificate management.

See `contrib/docker-compose/standalone/README.md` for full instructions.

# Building

## Any platform/arch

With Go 1.12 installed, from this directory:

    export GOPATH=$(pwd)
    go build hockeypuck/server/cmd/...
    go install hockeypuck/server/cmd/...

Hockeypuck binaries will be in `bin/`.

## Vendored Dependencies

The dependencies for this project are managed via Go modules.
To update the dependencies run:

    cd src/hockeypuck
    go get -u -m
    go mod vendor

After which you can ensure that the code continues to build and
that the tests still pass.

## Ubuntu package maintainers

To locally build the hockeypuck binaries:

    make install-build-depends
    make

# Releases

## Building a Snap

If it's installed, remove the snapcraft Ubuntu package:

    sudo apt remove snapcraft

Install snapcraft from the Snap store:

    snap install snapcraft

Confirm that you're using a recent enough version of snapcraft:

    $ which snapcraft
    /snap/bin/snapcraft
    $ snapcraft version
    snapcraft, version 3.7.2
    $ _

Now you can build the snap:

    snapcraft snap

snapcraft defaults to building in a multipass VM.  If you're already
in a throwaway environment, you can build the snap as follows instead:

    SNAPCRAFT_BUILD_ENVIRONMENT=host snapcraft snap

Or use LXD for a lighter-weight isolated build:

    SNAPCRAFT_BUILD_ENVIRONMENT=lxd snapcraft snap

Enabling cpu or memory profiling:

    sudo snap set hockeypuck cpuprof=1
    sudo snap set hockeypuck memprof=1
    sudo systemctl restart snap.hockeypuck.hockeypuck.service

Disabling cpu or memory profiling:

    sudo snap unset hockeypuck cpuprof
    sudo snap unset hockeypuck memprof
    sudo systemctl restart snap.hockeypuck.hockeypuck.service

## Ubuntu package maintainers

In order to release a new version of hockeypuck:

    make dch
    git add debian/changelog
    git commit -m 'x.y.z release'
    git tag -s -u <keyid> -m 'x.y.z release' x.y.z
    git push --tags
    make deb-src
    dput <your ppa> ../hockeypuck\_x.y.z\_source.changes

Where `x.y.z` is the appropriate version number.
This will upload the debian source package to the Launchpad PPA for building.

# About

Copyright 2022, The Hockeypuck Developers; see CONTRIBUTORS and LICENSE for details.
