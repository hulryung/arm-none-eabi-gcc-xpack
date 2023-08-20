[![license](https://img.shields.io/github/license/xpack-dev-tools/arm-none-eabi-gcc-xpack)](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/blob/xpack/LICENSE)
[![GitHub issues](https://img.shields.io/github/issues/xpack-dev-tools/arm-none-eabi-gcc-xpack.svg)](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/issues/)
[![GitHub pulls](https://img.shields.io/github/issues-pr/xpack-dev-tools/arm-none-eabi-gcc-xpack.svg)](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/pulls)

# Maintainer info

## Prerequisites

The build scripts run on GNU/Linux and macOS. The Windows binaries are
generated on Intel GNU/Linux, using [mingw-w64](https://mingw-w64.org).

For GNU/Linux, the prerequisites are:

- `curl` (installed via the system package manager)
- `git` (installed via the system package manager)
- `docker` (preferably a recent one, installed from **docker.com**)
- `npm` (shipped with Node.js; installed via **nvm**, **not**
  the system package manager)
- `xpm` (installed via `npm`)

For macOS, the prerequisites are:

- `npm` (shipped with Node.js; installed via **nvm**)
- `xpm` (installed via `npm`)
- the **Command Line Tools** from Apple

For details on installing them, please read the
[XBB prerequisites](https://xpack.github.io/xbb/prerequisites/) page.

If you already have a functional configuration from a previous run,
it is recommended to update **xpm**:

```sh
npm install --location=global xpm@latest
```

## Get project sources

The project is hosted on GitHub:

- <https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack.git>

To clone the stable branch (`xpack`), run the following commands in a
terminal (on Windows use the _Git Bash_ console):

```sh
rm -rf ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
git clone https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack.git \
  ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

For development purposes, clone the `xpack-develop` branch:

```sh
rm -rf ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
mkdir -p ~/Work/xpack-dev-tools && \
git clone \
  --branch xpack-develop \
  https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack.git \
  ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

Or, if the repo was already cloned:

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull
```

## Get helper sources

The project has a dependency to a common **helper**; clone the
`xpack-develop` branch and link it to the central xPacks store:

```sh
rm -rf ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
mkdir -p ~/Work/xpack-dev-tools && \
git clone \
  --branch xpack-develop \
  https://github.com/xpack-dev-tools/xbb-helper-xpack.git \
  ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git
```

Or, if the repo was already cloned:

```sh
git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git
```

## Release schedule

The xPack GNU Arm Embedded GCC release schedule generally follows the
[Arm GNU Toolchain](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads/)
release schedule, which is about two releases per year.

## How to make new releases

Before starting the build, perform some checks and tweaks.

### Download the build scripts repo

The build scripts are available in the `scripts` folder of the
[`xpack-dev-tools/arm-none-eabi-gcc-xpack`](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack)
Git repo.

To download them on a new machine, clone the `xpack-develop` branch,
as seen above.

### Check Git

In the `xpack-dev-tools/arm-none-eabi-gcc-xpack` Git repo:

- switch to the `xpack-develop` branch
- pull new changes
- if needed, merge the `xpack` branch

No need to add a tag here, it'll be added when the release is created.

### Update helper & other dependencies

Check the latest versions at <https://github.com/xpack-dev-tools/> and
update the dependencies in `package.json`.

### Update to latest Arm release

Download the new _Source code_ archive (like
`arm-gnu-toolchain-src-snapshot-*.tar.xz` from
[Arm](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/downloads)

Download the latest **macOS (x86_64) hosted cross toolchains** archive (like
`arm-gnu-toolchain-*-darwin-x86_64-arm-none-eabi.tar.xz`)
and copy the file with the configurations
(`*-darwin-x86_64-arm-none-eabi-manifest.txt`) to extras.

Download the ABE manifest with the individual source URLs
(`arm-gnu-toolchain-arm-none-eabi-abe-manifest.txt`), and copy to extras.

From the ABE manifest, identify the `gcc_url` and `gcc_filespec`,
compose the url (like `https://developer.arm.com/-/media/Files/downloads/gnu/12.3.rel1/src/gcc.tar.xz) and download the archive.

To get the **newlib** version, check the manifest.txt; download the archive from:

- <https://developer.arm.com/-/media/Files/downloads/gnu/12.3.rel1/src/newlib-cygwin.tar.xz>

and search for `VERSION=` in `newlib/configure`.

### Increase the version

From `gcc/BASE-VER`, determine the GCC version (like `12.3.1`)
and update the `scripts/VERSION`
file; the format is `12.3.1-1.1`. The fourth number is the Arm release
number and the fifth is the xPack release number
of this version. A sixth number will be added when publishing
the package on the `npm` server.

### Fix possible open issues

Check GitHub issues and pull requests:

- <https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/issues/>

and fix them; assign them to a milestone (like `12.3.1-1.1`).

### Check `README.md`

Normally `README.md` should not need changes, but better check.
Information related to the new version should not be included here,
but in the version specific release page.

### Update versions in `README` files

Update both full 5 numbers (`12.3.1-1.1`) and short 3 numbers (`12.3.1`)
versions in:

- update version in `README-MAINTAINER.md`
- update version in `README.md`

### Update version in `package.json` to a pre-release

Use a new version, suffixed by `.pre`.

### Update `CHANGELOG.md`

- open the `CHANGELOG.md` file
- check if all previous fixed issues are in
- add a new entry like _* v12.3.1-1.1 prepared_
- commit with a message like _prepare v12.3.1-1.1_

### Update local binutils-gdb fork

With a Git client:

- checkout the branch mentioned in the release notes
  (like `binutils-2_40-branch`)
- identify the commit ID
- add a tag like `binutils-2.40-arm-none-eabi-12.3.rel1`
- push the tag to origin
- check the tag at <https://github.com/xpack-dev-tools/binutils-gdb/tags/>

Similarly for GDB:

- checkout the branch mentioned in the release notes
  (like `gdb-13-branch`)
- identify the commit ID
- add a tag like `gdb-13-arm-none-eabi-12.3.rel1`
- push the tag to origin
- check the tag at <https://github.com/xpack-dev-tools/binutils-gdb/tags/>

### Update local gcc fork (if the commit ID present in manifest.txt)

With a Git client:

- checkout the branch mentioned in the release notes
  (like `ARM/arm-12`)
- identify the commit ID
- create a branch like `arm-12-arm-none-eabi-12.3.rel1`

### Update the gdb-*-cross.git.patch in helper

Copy/paste a previous one; adjust if necessary.

### Update the version specific code

- open the `scripts/versioning.sh` file
- add a new `if` with the new version before the existing code

To find the actual versions of the dependent libraries, check the
snapshot archive and the ABE manifest provided by Arm.

## Build

The builds currently run on 5 dedicated machines (Intel GNU/Linux,
Arm 32 GNU/Linux, Arm 64 GNU/Linux, Intel macOS and Apple Silicon macOS).

### Development run the build scripts

Before the real build, run test builds on all platforms.

#### Visual Studio Code

All actions are defined as **xPack actions** and can be conveniently
triggered via the VS Code graphical interface, using the
[xPack extension](https://marketplace.visualstudio.com/items?itemName=ilg-vscode.xpack).

#### Temporarily disable multi-lib

In the `scripts/application.sh` enable the `WITHOUT_MULTILIB="y"` definition.

#### Patches

Possibly add binutils & gdb patches.

#### Intel macOS

For Intel macOS, first run the build on the development machine
(`wksi`, a recent macOS):

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull

xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git

git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull
xpm run link-deps -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git

# For backup overhead reasons, on the development machine
# the builds happen on a separate Work folder.
rm -rf ~/Work/xpack-dev-tools-build/arm-none-eabi-gcc-[0-9]*-*

xpm install --config darwin-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run build-develop --config darwin-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

For a debug build:

```sh
xpm run build-develop-debug --config darwin-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

The build takes about 1h30 (without multi-libs).

When functional, push the `xpack-develop` branch to GitHub.

Run the native build on the production machine
(`xbbmi`, an older macOS);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbmi
```

Repeat the same steps as before.

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run deep-clean --config darwin-x64  -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm install --config darwin-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run build-develop --config darwin-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

About 1h later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/build/darwin-x64/deploy
total 229480
-rw-r--r--  1 ilg  staff  107426547 Aug 19 17:29 xpack-arm-none-eabi-gcc-12.3.1-1.1-darwin-x64.tar.gz
-rw-r--r--  1 ilg  staff        119 Aug 19 17:29 xpack-arm-none-eabi-gcc-12.3.1-1.1-darwin-x64.tar.gz.sha
```

#### Apple Silicon macOS

Run the native build on the production machine
(`xbbma`, an older macOS);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbma
```

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run deep-clean --config darwin-arm64  -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm install --config darwin-arm64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run build-develop --config darwin-arm64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

About 35 minutes later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/build/darwin-arm64/deploy
total 230480
-rw-r--r--  1 ilg  staff  103576156 Aug 19 17:04 xpack-arm-none-eabi-gcc-12.3.1-1.1-darwin-arm64.tar.gz
-rw-r--r--  1 ilg  staff        121 Aug 19 17:04 xpack-arm-none-eabi-gcc-12.3.1-1.1-darwin-arm64.tar.gz.sha
```

#### Intel GNU/Linux

Run the docker build on the production machine (`xbbli`);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbli
```

##### Build the GNU/Linux binaries

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run deep-clean --config linux-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-prepare --config linux-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-link-deps --config linux-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run docker-build-develop --config linux-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

About 45 minutes later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/build/linux-x64/deploy
total 111984
-rw-r--r-- 1 ilg ilg 114665185 Aug 19 14:11 xpack-arm-none-eabi-gcc-12.3.1-1.1-linux-x64.tar.gz
-rw-r--r-- 1 ilg ilg       118 Aug 19 14:11 xpack-arm-none-eabi-gcc-12.3.1-1.1-linux-x64.tar.gz.sha
```

##### Build the Windows binaries

Clean the build folder and prepare the docker container:

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
\
xpm run deep-clean --config win32-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-prepare --config win32-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-link-deps --config win32-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run docker-build-develop --config win32-x64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

About 32 minutes later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/build/win32-x64/deploy
total 111248
-rw-r--r-- 1 ilg ilg 113912789 Aug 19 14:03 xpack-arm-none-eabi-gcc-12.3.1-1.1-win32-x64.zip
-rw-r--r-- 1 ilg ilg       115 Aug 19 14:03 xpack-arm-none-eabi-gcc-12.3.1-1.1-win32-x64.zip.sha
```

#### Arm GNU/Linux 64-bit

Run the docker build on the production machine (`xbbla`);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbla
```

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run deep-clean --config linux-arm64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-prepare --config linux-arm64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-link-deps --config linux-arm64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run docker-build-develop --config linux-arm64 -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

About 4h35 later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/build/linux-arm64/deploy
total 108932
-rw-r--r-- 1 ilg ilg 111535746 Aug 19 18:04 xpack-arm-none-eabi-gcc-12.3.1-1.1-linux-arm64.tar.gz
-rw-r--r-- 1 ilg ilg       120 Aug 19 18:04 xpack-arm-none-eabi-gcc-12.3.1-1.1-linux-arm64.tar.gz.sha
```

#### Arm GNU/Linux 32-bit

Run the docker build on the production machine (`xbbla32`);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbla32
```

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
git -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpack-dev-tools/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run deep-clean --config linux-arm -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-prepare --config linux-arm -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
xpm run docker-link-deps --config linux-arm -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run docker-build-develop --config linux-arm -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

About 4h20 later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/build/linux-arm/deploy
total 102068
-rw-r--r-- 1 ilg ilg 104508664 Aug 19 17:50 xpack-arm-none-eabi-gcc-12.3.1-1.1-linux-arm.tar.gz
-rw-r--r-- 1 ilg ilg       118 Aug 19 17:50 xpack-arm-none-eabi-gcc-12.3.1-1.1-linux-arm.tar.gz.sha
```

### Building a debug version

In some cases it is necessary to run a debug session in the binaries,
or even in the libraries functions.

For these cases, the build script accepts the `--debug` options.

There are also xPack actions that use this option (`build-develop-debug`
and `docker-build-develop-debug`).

### Files cache

The XBB build scripts use a local cache such that files are downloaded only
during the first run, later runs being able to use the cached files.

However, occasionally some servers may not be available, and the builds
may fail.

The workaround is to manually download the files from an alternate
location (like
<https://github.com/xpack-dev-tools/files-cache/tree/master/libs>),
place them in the XBB cache (`Work/cache`) and restart the build.

## Run the CI build

The automation is provided by GitHub Actions and three self-hosted runners.

### Temporarily disable multi-lib

It is recommended to do **a first run without the multi-libs**
(see the `application.sh` file), **test** it,
and, when ready, rerun the full build (no kidding,
this usually saves time, given that a full build takes
more than half day).

### Generate the GitHub workflows

Run the `generate-workflows` to re-generate the
GitHub workflow files; commit and push if necessary.

### Start the self-hosted runners

- on the development machine (`wksi`) open ssh sessions to the build
machines (`xbbmi`, `xbbma`, `xbbli`, `xbbla` and `xbbla32`):

```sh
caffeinate ssh xbbmi
caffeinate ssh xbbma
caffeinate ssh xbbli
caffeinate ssh xbbla
caffeinate ssh xbbla32
```

For `xbbli` & `xbbla` start two runners:

```sh
screen -S ga

~/actions-runners/xpack-dev-tools/1/run.sh &
~/actions-runners/xpack-dev-tools/2/run.sh &

# Ctrl-a Ctrl-d
```

On all other machines start a single runner:

```sh
screen -S ga

~/actions-runners/xpack-dev-tools/run.sh &

# Ctrl-a Ctrl-d
```

## Push the build scripts

In this Git repo:

- push the `xpack-develop` branch to GitHub
- possibly push the helper project too

From here it'll be cloned on the production machines.

### Publish helper

Publish a new release of the helper and update the reference in `package.json`.

### Check for disk space

Check if the build machines have enough free space and eventually
do some cleanups (`df -BG -H /` on Linux, `df -gH /` on macOS).

### Manually trigger the build GitHub Actions

To trigger the GitHub Actions builds, use the xPack actions:

- `trigger-workflow-build-xbbmi`
- `trigger-workflow-build-xbbma`
- `trigger-workflow-build-xbbli`
- `trigger-workflow-build-xbbla`
- `trigger-workflow-build-xbbla32`

This is equivalent to:

```sh
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbmi
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbma
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbli
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbla
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbla32
```

These scripts require the `GITHUB_API_DISPATCH_TOKEN` variable to be present
in the environment, and the organization `PUBLISH_TOKEN` to be visible in the
Settings → Action →
[Secrets](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/settings/secrets/actions)
page.

These commands use the `xpack-develop` branch of this repo.

## Durations & results

The full builds take about 13 hours (about 4h30 without multi-libs)
to complete:

- `xbbmi`: 5h12 (1h05)
- `xbbma`: 1h51 (0h33)
- `xbbli`: 1h54 Linux, 1h52 Windows (0h40 Linux, 0h31 Windows)
- `xbbla`: 12h07 (4h34)
- `xbbla32`: 12h40 (4h20)

The workflows results and logs are available from the
[Actions](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/actions/) page.

The resulting binaries are available for testing from
[pre-releases/test](https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/).

## Testing

### CI tests

The automation is provided by GitHub Actions.

To trigger the GitHub Actions tests, use the xPack actions:

- `trigger-workflow-test-prime`
- `trigger-workflow-test-docker-linux-intel`
- `trigger-workflow-test-docker-linux-arm`

These are equivalent to:

```sh
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-test-prime.sh
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-test-docker-linux-intel.sh
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-workflow-test-docker-linux-arm.sh
```

These scripts require the `GITHUB_API_DISPATCH_TOKEN` variable to be present
in the environment.

These actions use the `xpack-develop` branch of this repo and the
[pre-releases/test](https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/)
binaries.

The tests results are available from the
[Actions](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/actions/) page.

Since GitHub Actions provides a single version of macOS, the
multi-version macOS tests run on Travis.

To trigger the Travis test, use the xPack action:

- `trigger-travis-macos`

This is equivalent to:

```sh
bash ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git/xpacks/@xpack-dev-tools/xbb-helper/github-actions/trigger-travis-macos.sh
```

This script requires the `TRAVIS_COM_TOKEN` variable to be present
in the environment.

The test results are available from
[Travis CI](https://app.travis-ci.com/github/xpack-dev-tools/arm-none-eabi-gcc-xpack/builds/).

### Re-enable multi-lib, build and test again

Once the simple build passed tests, enable multi-lib and rerun:

- comment out `XBB_APPLICATION_WITHOUT_MULTILIB` in `application.sh`
- commit and push the `xpack-develop` branch to the repo
- go back to _Push the build scripts_, build and test again.

### Manual tests

To download the pre-released archive for the specific platform
and run the tests, use:

```sh
git -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git pull
xpm run install -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
xpm run test-pre-release -C ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

For even more tests, on each platform (MacOS, GNU/Linux, Windows),
download the archive from
[pre-releases/test](https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/)
and check the binaries.

On macOS, remove the `com.apple.quarantine` flag:

```sh
xattr -cr ${HOME}/Downloads/xpack-*
```

- unpack the archive in `Downloads`, and rename the version
  folder, by replacing a dash with a space; this will test paths with spaces;
  on Windows the current paths always use spaces, so renaming is not needed;
- on macOS it is necessary to remove the `com.apple.quarantine`
  attribute of archive and possibly the expanded folder:

```sh
xattr -cr ~/Downloads/xpack-arm-none-eabi-gcc-*
```

- clone this repo locally; on Windows use the Git console;

```sh
rm -rf ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git && \
mkdir -p ~/Work/xpack-dev-tools && \
git clone \
  --branch xpack-develop \
  https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack.git \
  ~/Work/xpack-dev-tools/arm-none-eabi-gcc-xpack.git
```

- in a separate workspace, Import → General → Existing Projects into Workspace
  the Eclipse projects available in the
  `tests/eclipse` folder of the build repo; more details in the
  [README.md](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/blob/xpack/tests/eclipse/README.md)
- in Preferences... → MCU, define the **Workspace Arm Toolchain path** to use
  the `Downloads` temporary location
- to test the compiler: for all projects (start with `arm-f4b-fs-lib`)
  - remove all build folders, or **Clean all**
  - build all configs, with the hammer, not with **Build all**, to be sure
    errors are not missed
- to test the debugger: for all QEMU debug configurations (start with
  `arm-f4b-fs-debug-lto-qemu`)
  - start the QEMU debug session,
  - single step a few lines (Step Over)
  - start continuous run (Resume)
  - halt (Suspend)
  - start (Resume)
  - stop (Terminate)
  - (don't miss the LTO cases, since in the past they had problems)
- to test the Python debugger, start it with `--version`

## Create a new GitHub pre-release draft

- in `CHANGELOG.md`, add the release date and a message like _* v12.3.1-1.1 released_
- commit with _CHANGELOG update_
- check and possibly update the `templates/body-github-release-liquid.md`
- push the `xpack-develop` branch
- run the xPack action `trigger-workflow-publish-release`

The workflows results and logs are available from the
[Actions](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/actions/) page.

The result is a
[draft pre-release](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/)
tagged like **v12.3.1-1.1** (mind the dash in the middle!) and
named like **xPack GNU Arm Embedded GCC v12.3.1-1.1** (mind the dash),
with all binaries attached.

- edit the draft and attach it to the `xpack-develop` branch (important!)
- save the draft (do **not** publish yet!)

## Prepare a new blog post

- check and possibly update the `templates/body-jekyll-release-*-liquid.md`
  (for the release dates, use <https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads/>)
- run the xPack action `generate-jekyll-post`; this will leave a file
on the Desktop.

In the `xpack/web-jekyll` GitHub repo:

- select the `develop` branch
- copy the new file to `_posts/releases/arm-none-eabi-gcc`

If any, refer to closed
[issues](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/issues/).

## Update the preview Web

- commit the `develop` branch of `xpack/web-jekyll` GitHub repo;
  use a message like _xPack GNU Arm Embedded GCC v12.3.1-1.1 released_
- push to GitHub
- wait for the GitHub Pages build to complete
- the preview web is <https://xpack.github.io/web-preview/news/>

## Create the pre-release

- go to the GitHub [Releases](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/) page
- perform the final edits and check if everything is fine
- temporarily fill in the _Continue Reading »_ with the URL of the
  web-preview release
- **keep the pre-release button enabled**
- do not enable Discussions yet
- publish the release

Note: at this moment the system should send a notification to all clients
watching this project.

## Update the READMEs listings and examples

- check and possibly update the `ls -l` output
- check and possibly update the output of the `--version` runs
- check and possibly update the output of `tree -L 2`
- commit changes

## Check the list of links

- open the `package.json` file
- check if the links in the `bin` property cover the actual binaries
- if necessary, also check on Windows

## Update package.json binaries

- select the `xpack-develop` branch
- run the xPack action `update-package-binaries`
- open the `package.json` file
- check the `baseUrl:` it should match the file URLs (including the tag/version);
  no terminating `/` is required
- from the release, check the SHA & file names
- compare the SHA sums with those shown by `cat *.sha`
- check the executable names
- commit all changes, use a message like
  _package.json: update urls for 12.3.1-1.1 release_ (without _v_)

## Publish on the npmjs.com server

- select the `xpack-develop` branch
- check the latest commits `npm run git-log`
- update `CHANGELOG.md`, add a line like _* v12.3.1-1.1.1 published on npmjs.com_
- commit with a message like _CHANGELOG: publish npm v12.3.1-1.1.1_
- `npm pack` and check the content of the archive, which should list
  only the `package.json`, the `README.md`, `LICENSE` and `CHANGELOG.md`;
  possibly adjust `.npmignore`
- `npm version 12.3.1-1.1.1`; the first 5 numbers are the same as the
  GitHub release; the sixth number is the npm specific version
- the commits and the tag should have been pushed by the `postversion` script;
  if not, push them with `git push origin --tags`
- `npm publish --tag next` (use `npm publish --access public`
  when publishing for the first time; add the `next` tag)

After a few moments the version will be visible at:

- <https://www.npmjs.com/package/@xpack-dev-tools/arm-none-eabi-gcc?activeTab=versions>

## Test if the binaries can be installed with xpm

Run the xPack action `trigger-workflow-test-xpm`, this
will install the package via `xpm install` on all supported platforms.

The tests results are available from the
[Actions](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/actions/) page.

## Update the repo

- merge `xpack-develop` into `xpack`
- push to GitHub

## Tag the npm package as `latest`

When the release is considered stable, promote it as `latest`:

- `npm dist-tag ls @xpack-dev-tools/arm-none-eabi-gcc`
- `npm dist-tag add @xpack-dev-tools/arm-none-eabi-gcc@12.3.1-1.1.1 latest`
- `npm dist-tag ls @xpack-dev-tools/arm-none-eabi-gcc`

In case the previous version is not functional and needs to be unpublished:

- `npm unpublish @xpack-dev-tools/arm-none-eabi-gcc@12.3.1-1.1.1`

## Update the Web

- in the `master` branch, merge the `develop` branch
- wait for the GitHub Pages build to complete
- the result is in <https://xpack.github.io/news/>
- remember the post URL, since it must be updated in the release page

## Create the final GitHub release

- go to the GitHub [Releases](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/) page
- check the download counter, it should match the number of tests
- add a link to the Web page `[Continue reading »]()`; use an same blog URL
- remove the _tests only_ notice
- **disable** the **pre-release** button
- click the **Update Release** button

## Share on Twitter

- in a separate browser windows, open [TweetDeck](https://tweetdeck.twitter.com/)
- using the `@xpack_project` account
- paste the release name like **xPack GNU Arm Embedded GCC v12.3.1-1.1 released**
- paste the link to the Web page
  [release](https://xpack.github.io/arm-none-eabi-gcc/releases/)
- click the **Tweet** button

## Check SourceForge mirror

- <https://sourceforge.net/projects/arm-none-eabi-gcc-xpack/files/>

## Remove the pre-release binaries

- go to <https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/>
- remove the test binaries

## Clean the work area

Run the xPack action `trigger-workflow-deep-clean`, this
will remove the build folders on all supported platforms.

The results are available from the
[Actions](https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/actions/) page.

## Announce to Arm community

Add a new topic in the **Compilers and Libraries** forum of the
[Arm Developer Community](https://community.arm.com/support-forums/f/compilers-and-libraries-forum)

- title: xPack GNU Arm Embedded GCC v12.3.1-1.1 released
- content:

```html
<p>The <strong>xPack GNU Arm Embedded GCC</strong> and <strong>xPack GNU AArch64 Embedded GCC</strong> packages are part of the alternate xPack binary distribution that complements the official Arm GNU Toolchains maintained by Arm.</p>
<p>The latest release is 12.3.1-1.1 following the Arm release 12.3.Rel1 from December 22, 2022.</p>
<ul>
<li><a href="https://github.com/xpack-dev-tools/arm-none-eabi-gcc-xpack/releases/tag/v12.3.1-1.1/" rel="noopener noreferrer" target="_blank">xPack GNU Arm Embedded GCC v12.3.1-1.1</a></li>
<li><a href="https://github.com/xpack-dev-tools/aarch64-none-elf-gcc-xpack/releases/tag/v12.3.1-1.1" rel="noopener noreferrer" target="_blank">xPack GNU AArch64 Embedded GCC v12.3.1-1.1</a></li>
</ul>
```

Tags: xpack, gnu, gcc, arm, toolchain

Update with actual details from
[Arm GNU Toolchain](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads/)
