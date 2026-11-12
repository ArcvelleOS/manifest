# ArcvelleOS

ArcvelleOS is a fork based on Paranoid Android (AOSPA) and the Android Open Source Project. This repository contains the manifest used to sync the ArcvelleOS source tree.

## Set up your machine

You must run a 64-bit Linux distribution to build ArcvelleOS.
Follow the system setup instructions on the [Android Open Source Project website](https://source.android.com/source/initializing.html#setting-up-a-linux-build-environment).
Google provides Ubuntu-specific setup packages and instructions.
Complete the environment setup before you proceed.

## Obtain the source

[Repo](https://source.android.com/source/developing.html) is a tool provided by Google that simplifies using [Git](https://git-scm.com/book) with Android source trees.

### Install Repo

Create a directory for `repo` and add it to your `PATH`:
```bash
mkdir -p ~/.local/bin
export PATH=~/.local/bin:$PATH
```

Download the `repo` binary:
```bash
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.local/bin/repo
```

Make the binary executable:
```bash
chmod a+x ~/.local/bin/repo
```

### Initialize Repo

Create a working directory on a case-sensitive filesystem and navigate into it.
Replace `WORKSPACE` with your chosen directory path.
```bash
mkdir WORKSPACE
cd WORKSPACE
```

Initialize the manifest repository:
```bash
repo init -u https://github.com/ArcvelleOS/manifest -b vertex --git-lfs
```

### Download the source tree

Run `repo sync` to pull the source code.

> [!NOTE]
> Initial synchronization downloads the entire source history and takes significant time.

Use all available CPU cores for the sync:
```bash
repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags
```

> [!TIP]
> If your connection is limited, lower the job count (for example, `-j4`).

#### Sync specific projects

You can synchronize individual projects instead of the entire source tree.
Specify projects by repository path or remote name.

> [!WARNING]
> Partial synchronization can cause build failures if changes span across projects.

For example, specify `frameworks/base` or `ArcvelleOS/android_frameworks_base`:
```bash
repo sync PROJECT
```

## Build

The bundled builder script `./rom-build.sh` automates all build steps for a target device.
Provide the target device codename as the argument.
For Xiaomi marble:
```bash
cd WORKSPACE
./rom-build.sh marble
```

Run the builder with `-h` to see every option:
```text
Usage: ./rom-build.sh <device> [options]

Options:
  -h, --help            Display this help message
  -c, --clean           Wipe the tree before building
  -i, --installclean    Dirty build - Use 'installclean'
  -r, --repo-sync       Sync before building
  -v, --variant         AOSPA variant - Can be alpha, beta or stable
  -t, --build-type      Specify build type
  -j, --jobs            Specify jobs/threads to use
  -m, --module          Build a specific module
  -s, --sign-keys       Specify path to sign key mappings
  -p, --pwfile          Specify path to sign key password file
  -b, --backup-unsigned Store a copy of unsigned package along with signed
  -d, --delta           Generate a delta ota from the specified target_files zip
  -z, --imgzip          Generate fastboot flashable image zip from signed target_files
  -n, --version         Specify build minor version (number)
  -l, --vanilla         Disable GMS for this build
```

### Vanilla build

Use `-l` to disable GMS.
This example creates a user build with both recovery and fastboot images:
```bash
./rom-build.sh marble -t user -z -l
```

### User build

The default build includes GMS.
This example creates a user build with both recovery and fastboot images:
```bash
./rom-build.sh marble -t user -z
```

### Release signing keys

Official builds use the private ArcvelleOS release keys at `vendor/arcvelle-priv/keys`.
They are not downloaded by a normal `repo sync`.
With access to the private keys repository, sync them with:
```bash
repo sync -g arcvelle-keys
```

Builds without the keys still work, but are marked `Vertex Unofficial`.
See `ArcvelleOS/android_vendor_arcvelle-priv_keys` for full signing details.

## Manifest layout

| File | Contents |
| --- | --- |
| `default.xml` | Remotes and includes for the full manifest. |
| `system.xml` | Merged Qualcomm system snapshot. |
| `vendor.xml`, `wlan.xml` | Qualcomm vendor and WLAN projects. |
| `aosp.xml` | AOSP projects pinned by the baseline. |
| `aospa.xml` | Stock AOSPA projects. |
| `obsolete.xml` | Retired project removals. |
| `arcvelle.xml` | Full ArcvelleOS project list, grouped by component, with additional projects at the bottom. |
| `internal.xml` | Internal projects excluded from normal sync, including the private release keys. |
| `vanilla.xml` | Vanilla apps, WebView prebuilts, MicroG and Pixel clocks. |

## Contributing

ArcvelleOS is developed on GitHub. Fork the relevant repository, create a topic branch, and open a pull request against the `vertex` branch.

## Project assets and licensing

### Source code

The codebase uses the Apache License, Version 2.0 unless otherwise specified.
Retain all copyright and license notices when you use or modify the source code.
State any modifications you make to the code.
Read the full license text at https://www.apache.org/licenses/LICENSE-2.0.

### Images and branding assets

Unless otherwise specified, all project assets (including images and branding) use the Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0) license.
You may share and adapt these assets for non-commercial purposes.
You must provide attribution to the original author (ArcvelleOS, Paranoid Android Project, or the relevant upstream project).
Include a reference to the license, note any modifications, and link to https://github.com/ArcvelleOS.
Read the full license text at https://creativecommons.org/licenses/by-nc/4.0/.
