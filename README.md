# Build Static Binaries for *urunc*-Supported Monitors

This repository provides a reference setup for building and distributing static
binaries of monitors and tools supported by
[**urunc**](https://github.com/urunc-dev/urunc).

## Supported Monitors and Tools

The following components are currently supported:

- [**QEMU**](https://www.qemu.org/)
- [**Firecracker**](https://firecracker-microvm.github.io/)
- [**Cloud-Hypervisor**](https://www.cloudhypervisor.org/)
- [**Solo5**](https://github.com/Solo5/solo5)
- [**virtiofsd**](https://gitlab.com/virtio-fs/virtiofsd)

## How to use

This repository serves as a hub for storing various builds of `urunc`'s
supported monitors and provides an easy platform for building both newer and
older versions of them.

Every user can request the build of any monitor version by opening a new Pull Request
(PR). The process is the following one:

1. **Specify the monitor version to build**: The version of each monitor is
   specified in the `VERSION` file located in its respective directory (e.g.,
   for Solo5, the version is in [solo5/VERSION](solo5/VERSION)). For Qemu,
   Solo5 and Virtiofsd, the `COMMIT` file in the same directory has to be
   updated as well, with the full hash of the commit that the version's tag
   points to (see [Source verification](#Source-verification)).
2. **Open a new PR**: Create new PR with the version change.
3. **PR workflow gets triggered**: Upon opening a new PR a workflow will check
   whether a) the latest release already contains all the requested monitors
   and b) previous releases contain some of them. The monitors that are not
   found in a release are built. The workflow will comment in the PR with the
   findings
4. **Merge of PR and creation of new relase**: Upon the approval and merge of
   the PR in the main branch, a new workflow will collect the monitors, either
   from previous releases or by building them again, and create a new release
   following the [release versioning scheme](#Release-Versioning-Scheme).

__Note for QEMU__

Since QEMU is highly configurable, the build process also tracks changes in the
following configuration files:

- [amd64.config](qemu/amd64.config): Configuration for building Qemu on amd64
  architectures,
- [arm64.config](qemu/arm64.config): Configuration for building Qemu on arm64
  architectures.
- [excluded files list](qemu/unnecessary_files.txt): the list with the excluded
  files from the final Qemu installation directory.

## Source verification

The monitors are either built from source or downloaded as prebuilt binaries
from their upstream releases. In both cases, the workflows verify what they
fetch and fail if the verification does not pass:

- **Qemu, Solo5 and Virtiofsd** are built from a clone of the version's tag.
  Since a tag can be moved, the workflow checks that the cloned tag points to
  the commit in the respective `COMMIT` file (e.g.
  [solo5/COMMIT](solo5/COMMIT)). To find the commit of a tag:
  ```
  git ls-remote --tags <repository> <version> <version>^{}
  ```
  For annotated tags (e.g. Qemu and Virtiofsd) the commit is the one of the
  `^{}` line.
- **Firecracker and Cloud-Hypervisor** are downloaded from their GitHub
  releases and their sha256 is checked. For Firecracker, against the checksum
  file of the release and for Cloud-Hypervisor, against the digest that GitHub
  provides for the release asset.

## Release Versioning Scheme

Releases are named `vMAJOR.MINOR.PATCH` (e.g. `v0.1.0`):

- `MAJOR` and `MINOR` are specified in the top-level [VERSION](VERSION) file
  (e.g. `v0.1`) and they are bumped manually with a PR.
- `PATCH` is bumped automatically. Upon a merge in the main branch, a new
  release is created only if the monitors differ from the ones in the latest
  release of the same `MAJOR.MINOR` series. For Qemu, Solo5 and Virtiofsd,
  which are built from source, this includes any change in their directory
  (e.g. their `COMMIT` file or the Qemu configuration files) or in their build
  workflow, since such a change leads to a new build. The first release of a
  new series gets `PATCH` 0.

The versions of the monitors that a release contains are listed in its
description.

## Release Artifacts

Each release contains the following artifacts for both `amd64` and `arm64`
(`<arch>`):

| Artifact | Contents |
| --- | --- |
| `firecracker-<arch>` | Firecracker static binary |
| `cloud-hypervisor-<arch>` | Cloud-Hypervisor static binary |
| `solo5-hvt-<arch>` | Solo5-hvt static binary |
| `solo5-spt-<arch>` | Solo5-spt static binary |
| `virtiofsd-<arch>` | Virtiofsd static binary |
| `qemu-<arch>.tar.gz` | Qemu static binary along with its firmware files |
| `<monitors>-<arch>.tar.gz` | All of the above in a single tar |

`<monitors>` lists the versions of all monitors:

```
FC-<firecracker_version>_CLH-<cloud-hypervisor-version>_S5-<solo5_version>_VFS-<virtiofsd_version>_QM-<qemu_version>-<qemu_config_and_exclude_files_hash>
```

e.g. `FC-v1.7.0_CLH-v50.0_S5-v0.12.1_VFS-v1.13.0_QM-v10.1.1-9a44e-amd64.tar.gz`.

Both tars contain a `urunc/` directory and Qemu expects to find its files under
`/opt/urunc`. Therefore, they should be extracted with:
