# arm64-vm

A complete virtual **ARM64 (aarch64) computer** running in QEMU, plus a small Android app
built alongside it.

**Project site: <https://aviruprs-lgtm.github.io/arm64-vm/>**

## What it is

Not a container and not an emulated shell — a real machine:

- QEMU `virt` board, emulated Cortex-A72 (ARMv8-A, implementer `0x41`, part `0xd08`)
- Linux **6.18.58-0-virt**, raw aarch64 `Image`
- Alpine **3.24** userland (musl, BusyBox, apk) — 130 packages
- Persistent **ext4** root filesystem, 768 MiB, on a virtio-blk disk
- **X11 desktop** that starts by itself: Xorg 21.1.25 with the `modesetting` driver on
  virtio-gpu, twm + xterm, no `xorg.conf` anywhere
- virtio-net with user-mode NAT, sshd reachable on `127.0.0.1:2222`
- Serial console on `ttyAMA0`

Everything runs under QEMU's TCG translator, so it boots on any x86-64 host with no ARM
hardware and no KVM.

## Run it

Download the machine from the [latest release](https://github.com/aviruprs-lgtm/arm64-vm/releases/latest).
The archive ships as two parts, because it is larger than the publishing pipeline's per-file
limit; concatenating them reproduces the original file exactly.

```sh
curl -LO https://github.com/aviruprs-lgtm/arm64-vm/releases/latest/download/arm64vm.tar.xz.part-00
curl -LO https://github.com/aviruprs-lgtm/arm64-vm/releases/latest/download/arm64vm.tar.xz.part-01
cat arm64vm.tar.xz.part-* > arm64vm.tar.xz
tar -xJf arm64vm.tar.xz
cd arm64vm
./run.sh
```

- Desktop: any VNC viewer at `127.0.0.1:5900`
- Shell: `ssh -p 2222 root@127.0.0.1` (password `arm64vm`)
- Console: the terminal `run.sh` was started from (`Ctrl-A X` to quit)

Tunables: `MEM`, `SMP`, `TB`, `SSH_PORT`, `VNC_DISPLAY`.

## AArch64 Inspector

The companion Android app is attached to the same release:
[`AArch64Inspector.apk`](https://github.com/aviruprs-lgtm/arm64-vm/releases/latest/download/AArch64Inspector.apk).

17 KB, pure DEX with no native libraries, so it installs on arm64-v8a, armeabi-v7a and
x86_64 alike. It reports the device's ABIs, CPU cores, `/proc/cpuinfo` implementer/part and
memory, and benchmarks the CPU with SHA-256 and an integer loop. minSdk 21, targetSdk 34,
signed with the v1, v2 and v3 schemes.

SHA-256 of the APK:

```
160a91eb3e17608090daa8c9cbecdc22e691ea14afce8ed710172721d1701b64
```

## Notes

- Emulation only: expect roughly 10–40× slower than native.
- The site (`index.html`) is served by GitHub Pages from the `main` branch root.
