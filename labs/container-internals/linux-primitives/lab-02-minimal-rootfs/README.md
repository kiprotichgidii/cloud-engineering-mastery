# Lab 1.2 — Build a Minimal Linux Root Filesystem

> **Module:** Month 1 — Container Internals and Runtime Engineering  
> **Week:** 1 — Linux Kernel Primitives  
> **Estimated effort:** 5–7 hours | **Difficulty:** Intermediate / advanced  
> **Environment:** Disposable Ubuntu Server 24.04 or Debian 13 VM on Proxmox  
> **Tools:** `debootstrap`, BusyBox, `unshare`, `mount`, `findmnt`, `chroot`, `pivot_root`, `nsenter`, `readelf`

## 1. Objective

Build a minimal Linux root filesystem, enter it using progressively stronger isolation techniques, and investigate what a process needs in order to run inside a container-like filesystem environment.

You will compare a plain rootfs directory, `chroot`, a private mount namespace, `pivot_root`, and a Docker/Podman container. The objective is to understand what a container runtime prepares—not just memorize commands.

## 2. Engineering context

A Linux process normally resolves absolute paths from `/`. A container process instead needs a filesystem tree containing its executables, libraries, configuration and runtime interfaces, without being presented with the host's ordinary root filesystem.

A container image supplies a root filesystem assembled from image layers. At container creation time, a runtime prepares mounts, configures a mount namespace, establishes the process root, applies other isolation and security settings, and starts the workload.

The image does not normally contain a separate kernel. The container process shares the host kernel and accesses kernel interfaces through mounts such as `/proc` and `/sys`, subject to namespace, permission and security restrictions.

| Concept | Meaning | Container relevance |
|---|---|---|
| Root filesystem (rootfs) | Directory tree presented as `/` | Supplies application files and userspace |
| `chroot` | Changes a process's root directory | Filesystem testing; not a standalone security boundary |
| Mount namespace | Independent mount table | Allows container-specific mount layout |
| Bind mount | Makes an existing path available at another path | Volumes and runtime-provided files |
| Pseudo-filesystem | Kernel-provided interface such as procfs/sysfs | Process, device and kernel information |
| `pivot_root` | Changes the root mount in a mount namespace | Establishes a new root mount |
| OverlayFS | Combines lower and upper filesystem layers | Common container writable-layer implementation |

## 3. Safety

Use only the disposable lab VM. Take a Proxmox snapshot first and keep a second SSH session or console open.

- Work under `/var/tmp/rootfs-lab`; never use `/` as a lab target.
- Do not bind-mount the host's entire root into the test root.
- Do not treat `chroot` as a security sandbox.
- Inspect mounts with `findmnt` before unmounting.
- Do not run broad cleanup commands against `/proc`, `/sys`, `/dev` or `/`.
- If mounting is denied, record the restriction; do not weaken host security policy blindly.

## 4. Preparation

Install tools:

```bash
sudo apt update
sudo apt install -y debootstrap busybox-static util-linux \
  mount procps file binutils lsof
```

Create workspace:

```bash
sudo install -d -m 0755 /var/tmp/rootfs-lab
sudo chown "$USER":"$USER" /var/tmp/rootfs-lab
```

Record environment:

```bash
uname -a
cat /etc/os-release
findmnt /
findmnt /proc
findmnt /sys
```

Save output to `evidence/environment.txt`. Record the kernel version, distribution, architecture and package versions.

Create `diagrams/rootfs-construction.mmd` showing the shared host kernel, host filesystem, separate rootfs, pseudo-filesystems and a process whose `/` is the new root. Create `diagrams/chroot-vs-pivot-root.mmd` showing how the two operations differ.

---

## Exercise 1 — Build a minimal BusyBox rootfs

BusyBox combines many Unix utilities into one executable. A statically linked BusyBox binary is useful for a tiny rootfs because it does not need a separate dynamic loader and library set.

Inspect the installed binary:

```bash
command -v busybox
file "$(command -v busybox)"
ldd "$(command -v busybox)" || true
```

Create the rootfs:

```bash
sudo mkdir -p /var/tmp/rootfs-lab/busybox-root/{bin,sbin,etc,proc,sys,dev,tmp,root}
sudo cp "$(command -v busybox)" /var/tmp/rootfs-lab/busybox-root/bin/busybox
sudo chmod 0755 /var/tmp/rootfs-lab/busybox-root/bin/busybox
```

If the binary is statically linked, create applet links:

```bash
sudo chroot /var/tmp/rootfs-lab/busybox-root \
  /bin/busybox --install -s /bin
```

If `chroot` cannot execute it, check the architecture and dynamic loader. Do not assume the binary is static merely because it is BusyBox.

Inspect:

```bash
sudo find /var/tmp/rootfs-lab/busybox-root -maxdepth 2 -type f -o -type l
sudo du -sh /var/tmp/rootfs-lab/busybox-root
```

**Investigate:** Why does a static executable simplify a rootfs? Which applets are symlinks? Is there an init system? Is there a kernel? What additional files would a real application require?

**Evidence:** `file`, `ldd`, rootfs listing and size.

## Exercise 2 — Build a distribution rootfs with debootstrap

BusyBox is intentionally small. Real applications commonly require a shell, libraries, CA certificates and other userspace components. `debootstrap` creates a Debian-family filesystem tree without installing a complete VM.

For Debian 13:

```bash
sudo debootstrap --variant=minbase trixie \
  /var/tmp/rootfs-lab/debian-root https://deb.debian.org/debian
```

For Ubuntu 24.04, if supported by your installed debootstrap:

```bash
sudo debootstrap --variant=minbase noble \
  /var/tmp/rootfs-lab/ubuntu-root https://archive.ubuntu.com/ubuntu
```

Use one rootfs consistently for the rest of the lab. Do not mix suites or distributions.

Inspect size and layout:

```bash
sudo du -sh /var/tmp/rootfs-lab/debian-root
sudo find /var/tmp/rootfs-lab/debian-root -maxdepth 2 -type d | sort | head -80
sudo file /var/tmp/rootfs-lab/debian-root/bin/sh
sudo readelf -l /var/tmp/rootfs-lab/debian-root/bin/sh | grep interpreter || true
```

**Investigate:** Compare the BusyBox and debootstrap rootfs sizes. What libraries does the shell need? What does the ELF interpreter path tell you? Why is a rootfs not the same as a bootable VM disk?

**Evidence:** Size comparison, directory listings and ELF interpreter details.

## Exercise 3 — Enter with chroot and understand its limits

```bash
sudo chroot /var/tmp/rootfs-lab/debian-root /bin/bash
```

Inside:

```bash
pwd
hostname
ls -la /
ps -ef
```

`ps` may show incomplete information because `/proc` has not been mounted in the new root.

Exit and compare with a private mount namespace:

```bash
sudo unshare --mount --fork /bin/bash
mount --make-rprivate /
chroot /var/tmp/rootfs-lab/debian-root /bin/bash
```

Inside the chroot:

```bash
readlink /proc/$$/ns/mnt
findmnt
```

Exit both shells. Compare mount namespace inode identifiers from the host and isolated shell.

**Key distinction:** `chroot` changes path resolution for a process. It does not create PID, network, mount or user namespaces, and it does not apply cgroup limits. It is not a complete container and is not, by itself, a secure sandbox.

**Investigate:** Which property changed due to `chroot`? Which changed due to `unshare --mount`? Why is an independent mount namespace important to a runtime?

## Exercise 4 — Mount procfs, sysfs and a controlled /dev

A rootfs made from ordinary files is not enough for many Linux programs.

- `/proc` exposes process and kernel information.
- `/sys` exposes device and kernel subsystem information.
- `/dev` provides device nodes and interfaces.
- `/dev/pts` provides pseudo-terminals.

Start a private mount namespace:

```bash
sudo unshare --mount --fork /bin/bash
mount --make-rprivate /
ROOT=/var/tmp/rootfs-lab/debian-root
mkdir -p "$ROOT"/{proc,sys,dev,dev/pts,run,tmp}
chmod 1777 "$ROOT/tmp"
```

Mount procfs and a read-only sysfs view:

```bash
mount -t proc proc "$ROOT/proc"
mount -t sysfs -o ro,nosuid,nodev,noexec sysfs "$ROOT/sys"
```

Use a fresh tmpfs for `/dev` and create only the basic nodes needed by this exercise:

```bash
mount -t tmpfs -o mode=755,nosuid tmpfs "$ROOT/dev"
mkdir -p "$ROOT/dev/pts"
mknod -m 666 "$ROOT/dev/null" c 1 3
mknod -m 666 "$ROOT/dev/zero" c 1 5
mknod -m 666 "$ROOT/dev/random" c 1 8
mknod -m 666 "$ROOT/dev/urandom" c 1 9
mount -t devpts -o newinstance,ptmxmode=0666,mode=0620 devpts "$ROOT/dev/pts"
ln -s pts/ptmx "$ROOT/dev/ptmx"
```

Some environments restrict `mknod` or mount operations. Record the denial and investigate the relevant privilege/policy.

Inspect and enter:

```bash
findmnt -R "$ROOT"
chroot "$ROOT" /bin/bash
mount
ps -ef
ls -l /dev
head /proc/self/status
exit
```

Exit the namespace.

**Investigate:** Why does `ps` need procfs? Why should a container not casually inherit the host's `/proc`? Why is sysfs often read-only? What risks arise from exposing host devices? Why are these mounts configured by the runtime rather than simply copied into the image?

**Evidence:** `findmnt -R`, process information and `/dev` listing from inside.

## Exercise 5 — Bind mounts and propagation

Create controlled data:

```bash
sudo mkdir -p /var/tmp/rootfs-lab/shared-data
echo 'data from the lab VM' | sudo tee /var/tmp/rootfs-lab/shared-data/message.txt
```

Enter a private mount namespace:

```bash
sudo unshare --mount --fork /bin/bash
mount --make-rprivate /
ROOT=/var/tmp/rootfs-lab/debian-root
mkdir -p "$ROOT/mnt/shared"
mount --bind /var/tmp/rootfs-lab/shared-data "$ROOT/mnt/shared"
chroot "$ROOT" /bin/bash
cat /mnt/shared/message.txt
exit
findmnt "$ROOT/mnt/shared"
```

Exit the namespace.

Research and explain `private`, `shared`, `slave` and `unbindable` propagation modes. Test propagation only in a disposable mount namespace.

**Investigate:** Does a bind mount copy file contents? What happens when a file is modified through either path? Why does mount propagation matter for container volumes and Kubernetes workloads? When might a mount created in one namespace need to be visible in another?

**Evidence:** Mount table, test output and a propagation diagram.

## Exercise 6 — Change root mount using pivot_root

`pivot_root` changes the root mount of a mount namespace. It has strict requirements: the new root must be a mountpoint and the old root must be placed beneath the new root.

Start a new mount namespace:

```bash
sudo unshare --mount --fork /bin/bash
mount --make-rprivate /
ROOT=/var/tmp/rootfs-lab/debian-root
mkdir -p "$ROOT"/{oldroot,proc,sys,dev,run}
mount --bind "$ROOT" "$ROOT"
```

For this focused experiment, mount procfs and the controlled `/dev` layout as in Exercise 4. Do not expose host devices.

Perform the pivot:

```bash
cd "$ROOT"
pivot_root . oldroot
cd /
findmnt /
findmnt /oldroot
pwd
ls -la /
```

The former root should be visible at `/oldroot`. Detach it inside this disposable namespace:

```bash
umount -l /oldroot
findmnt /
```

Exit the shell; the namespace will be torn down when its processes exit.

If `pivot_root` fails with `EINVAL`, `EBUSY` or `EXDEV`, inspect mount topology, mount propagation and the old-root directory. Do not try random command combinations.

**Investigate:** Why must the new root be a mountpoint? Why must `put_old` be under the new root? Why change directory to `/` after the pivot? How does this differ from `chroot`? Which responsibilities would an OCI runtime perform around this operation?

**Evidence:** `findmnt` before and after, root directory listing and error investigation if applicable.

## Exercise 7 — Compare with a real container

Run a reference container using Docker or Podman. Example:

```bash
docker run -d --name rootfs-reference debian:stable-slim sleep 600
docker exec -it rootfs-reference sh
```

Inside:

```bash
cat /etc/os-release
mount
ls -la /
head /proc/self/mountinfo
```

On the host:

```bash
docker inspect rootfs-reference
```

Cleanup:

```bash
docker rm -f rootfs-reference
```

Compare the manual rootfs and runtime-created container:

| Area | Manual rootfs | Runtime-created container |
|---|---|---|
| Rootfs source | Directory assembled in lab | Image layers and runtime snapshot |
| Root switch | `chroot`/`pivot_root` manually | OCI runtime |
| Mount namespace | Created manually | Configured by runtime |
| `/proc` and `/dev` | Mounted manually | Runtime-provided mount layout |
| Cgroups | Not automatic | Runtime can configure resource controls |
| Security | Manually configured | Runtime defaults plus explicit settings |

Record actual runtime and version; do not assume Docker and Podman have identical defaults.

## 8. Failure injection

Perform at least four controlled tests using a copied lab rootfs:

| Failure | Experiment | Expected symptom | Diagnosis |
|---|---|---|---|
| Missing executable | Remove/rename shell in copied rootfs | chroot cannot execute it | Check path and permissions |
| Missing dynamic loader | Remove loader in copied rootfs | Existing executable may report “No such file” | `readelf -l` |
| Missing procfs | Enter without mounting `/proc` | Process tools incomplete | Inspect mounts |
| Bind over populated path | Bind empty directory over a test directory | Original contents hidden | `findmnt` |
| Read-only mount | Remount test path read-only | Writes fail | Mount options |
| Invalid pivot setup | Use invalid mount arrangement in disposable namespace | pivot_root error | Mount topology |

For each test record initial state, change, expected/actual result, diagnostic evidence, root cause and recovery.

## 9. Cleanup

Exit all namespace and chroot shells. Inspect remaining mounts:

```bash
findmnt -R /var/tmp/rootfs-lab
```

Unmount only mounts that actually exist, deepest first. For example:

```bash
sudo umount /var/tmp/rootfs-lab/debian-root/dev/pts
sudo umount /var/tmp/rootfs-lab/debian-root/dev
sudo umount /var/tmp/rootfs-lab/debian-root/proc
sudo umount /var/tmp/rootfs-lab/debian-root/sys
```

If a mount is busy, use `fuser`, `lsof` and `findmnt` to identify the holder. Do not immediately force or lazily unmount unknown mounts.

Keep the rootfs until your evidence is reviewed. Then remove the lab workspace if no longer needed:

```bash
sudo rm -rf /var/tmp/rootfs-lab
```

## 10. Completion checklist

- [ ] Build BusyBox rootfs.
- [ ] Build Debian/Ubuntu rootfs with debootstrap.
- [ ] Inspect binary dependencies and ELF interpreter.
- [ ] Enter using chroot and explain its limitations.
- [ ] Create private mount namespace.
- [ ] Mount procfs, sysfs and controlled `/dev`.
- [ ] Demonstrate bind mount and explain propagation.
- [ ] Use pivot_root and inspect before/after.
- [ ] Compare with Docker/Podman.
- [ ] Reproduce four failures and document root causes.
- [ ] Test cleanup from a clean state.

## 11. GitHub deliverables

```text
lab-02-minimal-rootfs/
├── README.md
├── scripts/
│   ├── build-busybox-rootfs.sh
│   ├── build-debian-rootfs.sh
│   ├── prepare-mounts.sh
│   ├── enter-rootfs.sh
│   └── cleanup.sh
├── diagrams/
│   ├── rootfs-construction.mmd
│   └── chroot-vs-pivot-root.mmd
├── evidence/
│   ├── environment.txt
│   ├── rootfs-comparison.md
│   ├── mount-layout.txt
│   └── pivot-root-observations.md
└── troubleshooting.md
```

Scripts must validate that the target path is the intended lab rootfs and refuse to operate on `/`. Do not create empty files just to match the tree.

## 12. Blog article

**Suggested title:** Building a Linux Container Filesystem from Scratch: chroot, pivot_root and Mount Namespaces

Suggested sections: motivation; rootfs versus image versus VM; building BusyBox and distribution rootfs; chroot limitations; mount namespaces and propagation; procfs/sysfs/dev; pivot_root; runtime comparison; failure analysis; security and cleanup.

### References

- [chroot(2)](https://man7.org/linux/man-pages/man2/chroot.2.html)
- [pivot_root(2)](https://man7.org/linux/man-pages/man2/pivot_root.2.html)
- [mount_namespaces(7)](https://man7.org/linux/man-pages/man7/mount_namespaces.7.html)
- [mount(8)](https://man7.org/linux/man-pages/man8/mount.8.html)
- [Linux OverlayFS documentation](https://docs.kernel.org/filesystems/overlayfs.html)
- [OCI Runtime Specification](https://github.com/opencontainers/runtime-spec)
- [Debootstrap documentation](https://wiki.debian.org/Debootstrap)

## 13. Lab review

- **What I built:** rootfs variants, mount layouts and scripts.
- **What I learned:** explain chroot, pivot_root, mount namespaces and pseudo-filesystems.
- **Failures:** symptoms, evidence, root causes and recovery.
- **Remaining questions:** topics to revisit before Week 2.
- **Links:** repository, diagrams and article draft.
