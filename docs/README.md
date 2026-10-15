---
description: >-
  A credential helper that will keep the container registry credentials used
  by Docker, Podman, Skopeo and Buildah in 1Password.
---

# Overview

{% hint style="warning" %}
This project is in early development and doesn't work yet. There are no releases. Installation and usage instructions will be added here when the first release is ready.
{% endhint %}

`docker-credential-onepassword` will be a credential helper for container tools. When you log in to a container registry, it will save your username and password in 1Password. When you pull or push an image, your container tool will ask the helper for those credentials, and the helper will read them from 1Password.

## Why use a credential helper

Without a credential helper, Docker Engine saves registry credentials in `~/.docker/config.json`. Podman, Skopeo and Buildah save them in an `auth.json` file, which on Linux is `$XDG_RUNTIME_DIR/containers/auth.json`. In both files the username and password are only base64-encoded, not encrypted. (Docker Desktop uses your operating system's keychain instead.)

A credential helper keeps your credentials out of these files.

## Planned tool support

The helper is being built for these container tools:

* Docker
* Podman
* Skopeo
* Buildah

## Source code and license

The source code is on [GitHub](https://github.com/froazin/docker-credential-onepassword). The project is released under the [MIT License](https://github.com/froazin/docker-credential-onepassword/blob/main/LICENSE).

This is an independent project. It isn't affiliated with or endorsed by AgileBits Inc. (1Password) or Docker, Inc. 1Password is a trademark of AgileBits Inc. Docker is a trademark of Docker, Inc.
