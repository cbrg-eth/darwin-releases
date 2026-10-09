# Darwin releases

Prebuilt releases of **Darwin**, an interpreted programming language for
bioinformatics: sequence alignment, phylogenetic trees, protein databases and
more. Darwin is developed by the Computational Biochemistry Research Group at
ETH Zurich.

This repository hosts the release downloads and the source of the Darwin
library. See [Releases](https://github.com/cbrg-eth/darwin-releases/releases)
for the downloads, and [`lib/`](lib) for the library code of the latest
release. The library of a specific release is under its tag, for example
`https://github.com/cbrg-eth/darwin-releases/tree/v1.2.3/lib`.

## Supported platforms

| Platform | Binary | Requirement |
|---|---|---|
| Linux x86_64 | `darwin-linux` | glibc 2.28 or newer |
| Linux arm64 (aarch64) | `darwin-linux-arm64` | glibc 2.28 or newer |
| macOS, Apple Silicon and Intel | `darwin-macos` | macOS 11 (Big Sur) or newer |

glibc 2.28 or newer covers RHEL/Rocky/Alma 8, Debian 10 and Ubuntu 20.04, and
any later release of these distributions.

`darwin-macos` is a universal binary that runs natively on both Apple Silicon
and Intel Macs. Releases up to v2026.10.08 contain an Apple Silicon-only
binary; on an Intel Mac, use a newer release or the Docker image.

## Installation

### Bundle (recommended)

`darwin-bundle.tar.gz` contains the binaries for all platforms, the Darwin
library and a launcher script that picks the right binary for your machine:

```sh
curl -L https://github.com/cbrg-eth/darwin-releases/releases/latest/download/darwin-bundle.tar.gz \
  | tar -xz -C ~/.local
# put the launcher on your PATH
mkdir -p ~/.local/bin
ln -s ~/.local/darwin/bin/darwin ~/.local/bin/darwin
```

You can unpack the bundle anywhere; the launcher finds its library relative to
its own location, also when called through a symlink.

The bundle has this layout:

```
darwin/
├── bin/
│   ├── darwin               launcher script, run this
│   ├── darwin-linux
│   ├── darwin-linux-arm64
│   └── darwin-macos
└── share/darwin/
    └── lib/                 the Darwin library
```

### Docker

A multi-platform image (linux/amd64 and linux/arm64) is available on Docker Hub:

```sh
docker run --rm -it cbrg/darwin
```

To work on files from your current directory, mount it into the container:

```sh
docker run --rm -it -v "$PWD":/app cbrg/darwin
```

### Single binaries

The `darwin-linux`, `darwin-linux-arm64` and `darwin-macos` files are the bare
executables. They need the Darwin library, so you will normally want the
bundle. If you run a binary directly, point it at a library with `-l` and
raise the stack limit first (see below):

```sh
ulimit -s unlimited
./darwin-linux -l /path/to/darwin/share/darwin/lib
```

## Getting started

Start an interactive session with `darwin`. Statements end with `;` and `done`
leaves the session:

```
$ darwin
> 6*7;
42
> done
```

To run a script non-interactively, pipe it in:

```sh
darwin < myscript.drw
```

## Data directory

Some functions need external data, such as protein databases. Darwin looks
for them in the directory set by `DARWIN_DATA_DIRECTORY`. When you use the
bundle launcher this defaults to `darwin/share/darwin/data` inside the bundle;
set the variable to use another location:

```sh
export DARWIN_DATA_DIRECTORY=$HOME/darwinDB
```

Databases are not included in the release. For example, to get SwissProt:

```sh
mkdir -p "$DARWIN_DATA_DIRECTORY"
curl -L -o "$DARWIN_DATA_DIRECTORY/SwissProt.Z" https://omabrowser.org/darwinDB/SwissProt.Z
```

## Stack size

Darwin recurses deeply and needs a large stack. The launcher and the Docker
image raise the stack limit for you. On macOS the limit cannot go above 64 MB,
which is enough for normal use. If you run a binary directly and see crashes
on large inputs, run `ulimit -s unlimited` first.

## Versions

Releases are tagged `vX.Y.Z`. The
[latest release](https://github.com/cbrg-eth/darwin-releases/releases/latest)
is always the newest tagged version.

## Reporting problems

Please open an issue in this repository. Include the release version, your
operating system and CPU (`uname -sm`), and a small script that reproduces the
problem. If the problem is in a library function, a link to the relevant lines
in [`lib/`](lib) helps a lot.

## Citing Darwin

If you use Darwin in published work, please cite:

> Gonnet GH, Hallett MT, Korostensky C, Bernardin L. Darwin v. 2.0: an
> interpreted computer language for the biosciences. *Bioinformatics*
> 2000;16(2):101–103.

## License

Distributed under the Mozilla Public License 2.0; see [LICENSE](LICENSE).
