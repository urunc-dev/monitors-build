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
   for Solo5, the version is in [solo5/VERSION](solo5/VERSION)).
2. **Open a new PR**: Create new PR with the version change.
3. **PR workflow gets triggered**: Upon opening a new PR a workflow will check
   whether a) the requested versions of monitors have been built before and if
   these artifacts are still valid and b) a previous release includes these
   monitors. If a monitor with the specified version does not exist, then the
   workflow will build it. The workflow will comment in the PR with the findings.
4. **Merge of PR and creation of new relase**: Upon the approval and merge of
   the PR in the main branch, a new workflow will collect all the artifacts of
   the monitors and create a new release following the [release versioning
   scheme](#Release-Versioning-Scheme).

__Note for QEMU__

Since QEMU is highly configurable, the build process also tracks changes in the
following configuration files:

- [amd64.config](qemu/amd64.config): Configuration for building Qemu on amd64
  architectures,
- [arm64.config](qemu/arm64.config): Configuration for building Qemu on arm64
  architectures.
- [excluded files list](qemu/unnecessary_files.txt): the list with the excluded
  files from the final Qemu installation directory.

## Release Versioning Scheme

Releases are named `vMAJOR.MINOR.PATCH` (e.g. `v0.1.0`):

- `MAJOR` and `MINOR` are specified in the top-level [VERSION](VERSION) file
  (e.g. `v0.1`) and they are bumped manually with a PR.
- `PATCH` is bumped automatically. Upon a merge in the main branch, a new
  release is created only if the versions of the monitors (or the Qemu
  configuration files) differ from the ones in the latest release of the same
  `MAJOR.MINOR` series. The first release of a new series gets `PATCH` 0.

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
