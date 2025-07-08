<div id="header" align="center">
  <img src="https://raw.githubusercontent.com/Shardbyte/Shardbyte/main/img/logo-shardbyte-master-light.webp" alt="logo-shardbyte" width="150"/>
</div>

<p align="center">
    <a href="https://github.com/Shardbyte/shard-minideb/actions/workflows/main.yml"><img src="https://github.com/Shardbyte/shard-minideb/actions/workflows/main.yml/badge.svg?branch=main" /></a>
    <a href="https://hub.docker.com/r/Shardbyte/shard-minideb/"><img src="https://badgen.net/docker/pulls/Shardbyte/shard-minideb?icon=docker&label=pulls" /></a>
    <a href="https://hub.docker.com/r/Shardbyte/shard-minideb/"><img src="https://badgen.net/docker/stars/Shardbyte/shard-minideb?icon=docker&label=stars" /></a>
</p>

# What is Shard-Minideb
A minimalist Debian-based image built specifically to be used as a base image for containers.

# Use Shard-Minideb
You can use the image directly, e.g.
```
$ docker run --rm -it shardbyte/shard-minideb:latest
```

There are [tags](https://hub.docker.com/r/shardbyte/shard-minideb/tags/) for the different Debian releases.
```
$ docker run --rm -it shardbyte/shard-minideb:bookworm
```

The images are built daily and have the security release enabled, so will contain any security updates released more than 24 hours ago.

You can also use the images as a base for your own `Dockerfile`:
```
FROM shardbyte/shard-minideb:bookworm
```

# Why use Shard-Minideb
  * This image aims to strike a good balance between having small images and having many quality packages available for easy integration.
  * The image is based on glibc for wide compatibility and is using apt for access to a large number of packages. To reduce the size of the image, some things that aren't required in containers are removed:
    * Packages that aren't often used in containers (hardware-related, init systems, etc.)
    * Some files that aren't usually required (docs, man pages, locales, caches)
  * These images also include an `install_packages` command that you can use instead of apt. This takes care of some things for you:
    * Install the named packages, skipping prompts, etc.
    * Clean up the apt metadata afterward to keep the image small.
    * Retrying if apt fails. Sometimes a package will fail to download due to a network issue, and this may fix that, which is particularly useful in an automated build pipeline.

    For example:
    ```
    $ install_packages apache2 memcached
    ```

# Compatibility
The image points to the Debian archive, so you are free to install the packages from there that you need. However, because some `Essential` packages have been removed they may not always install or work correctly.

In those cases, you can figure out which package is needed and manually specify to install it along with your desired packages.

# Security
Minideb is based on Debian and relies on their security updates. The images are built daily and have the security release enabled, so will contain any security updates released more than 24 hours ago.

Note that Debian [does not fix every CVE that affects their packages](https://www.debian.org/security/faq#cvedsa), which means that CVE scanners may detect unfixed vulnerabilities in Shard-Minideb images. In those cases, you can check the [Debian security tracker](https://security-tracker.debian.org/tracker/) to see whether Debian intends to release an update to fix it.

To keep compatibility with Debian, we will not patch any vulnerabilities in Shard-Minideb directly. If Debian does not fix the CVE then it will also remain in Shard-Minideb.

# Building Shard-Minideb
We provide a Makefile to help you build Shard-Minideb locally. It should be run on a Debian-based machine and requires sudo privileges.
```
$ sudo make
```

To build an individual release (bullseye or bookworm)
```
$ sudo make bookworm
```

To test the resulting image:
```
$ sudo make test-bookworm
```

## Building Shard-Minideb for foreign architecture
Make commands shown above will build an image for the architecture you are currently working on.
To build an image for a foreign architecture (for example to build a multi-arch image), we provide a
simple script that runs a QEMU instance for the target architecture and builds the image inside it.

To build and test a bookworm image for arm64:
```
$ ./qemu_build bookworm arm64
```

The image will be then imported locally through the docker CLI with the `$distribution-$architecture` tag
(example: `shardbyte/shard-minideb:bookworm-arm64`)

Current limitations of the `qemu_build` script:

- Can be run only on Debian-based distributions
- Support `AMD64` and `ARM64` target architectures only

---

> [!NOTE]
> Please be aware this file has not undergone internal testing. Consequently, we advise its use exclusively for development or testing purposes.

## License

MIT License

Copyright (c) 2024-2025 Shardbyte

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
