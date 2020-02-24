## :warning: End of project :warning:

[![development](https://img.shields.io/badge/development-halted-red.svg)](https://github.com/rkt/rkt/issues/4024)

This project [has ended](https://github.com/rkt/rkt/issues/4024), and **all development/maintenance activities have halted**.

As it is free software, people are free and welcome to fork and develop the codebase on their own.
However, to avoid any confusion, the original repository is archived and we recommend any further fork/development to proceed with an explicit rename and rebranding first.

We encourage all interested parties to mirror any relevant bits as we can't actively guarantee their existence in the future.

---

# rkt-builder

This repository holds scripts and releases for the rkt-in-rkt builder ACI.

## Usage

### Building a new rkt-in-rkt builder ACI

To build the builder ACI image, first update the version variable `IMG_VERSION` in `acbuild.sh`, and execute:

    $ sudo ./acbuild.sh

The rkt project key must be used to sign the generated image. `$RKTSUBKEYID` is the key ID of the rkt Yubikey. Connect the key and run `gpg2 --card-status` to get the ID.

The public key for GPG signing can be found at [CoreOS Application Signing Key](https://coreos.com/security/app-signing-key) and is assumed as trusted.

    $ gpg2 -u $RKTSUBKEYID'!' --armor --output rkt-builder.aci.asc --detach-sign rkt-builder.aci

Commit any changes to `acbuild.sh`, and push them.

Add a signed tag:

    $ GIT_COMMITTER_NAME="CoreOS Application Signing Key" GIT_COMMITTER_EMAIL="security@coreos.com" git tag -u $RKTSUBKEYID'!' -s v1.2.0 -m "rkt-builder v1.2.0"`

Push the tag to GitHub:

    $ git push --tags

### Building rkt-in-rkt

    $ git clone github.com/rkt/rkt
    $ cd rkt
    $ sudo rkt run \
        --volume src-dir,kind=host,source="$(pwd)" \
        --volume build-dir,kind=host,source="$(pwd)/release-build" \
        --interactive \
        coreos.com/rkt/builder:1.3.0

## Overview

This repository consists of two scripts:

- `acbuild.sh`: This script builds the rkt-in-rkt builder ACI.
- `build.sh`: This script is added to the rkt-in-rkt builder ACI as `/scripts/build.sh`, and is defined as the entrypoint.

The built rkt-in-rkt ACI declares the following volumes:

- `src-dir`: Points to the directory holding the rkt source code.
- `build-dir`: Points to the output directory where the build artifacts are being placed.
