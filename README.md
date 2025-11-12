<div align="center">

# IUBIK

#### Isolating User Bytes in Commodity Operating System Kernels <br/> via Memory Tagging Extensions
##### _IEEE Security and Privacy (S&P) 2024_

</div>

**Abstract.** Hardening OS kernels against memory errors is generally addressed by
protecting security-critical data against corruption and disclosure. However, establishing
a sound model for identifying sensitive memory objects in need of protection is hard,
leading to emergent attack vectors that can be abused by attackers. In this paper, we
propose rethinking how OS kernels are hardened by introducing IUBIK for compartmentalizing
kernel memory. IUBIK prevents kernel exploitation by segregating attacker-controlled
data—frequently used to manipulate security-critical data—in shadow memory, preventing it
from interacting with sensitive kernel objects. To achieve this, IUBIK uses MTE: a recent
hardware feature, available in ARM CPUs, which allows mitigating exploits based on both
spatial and temporal memory-errors, efficiently. We ensure that segregated objects do not
contain sensitive fields, such as pointers, by rewriting their struct definitions.
Moreover, we develop a profiling framework that explores the kernel codebase in-depth and
records code sites where attacker-controlled objects are allocated, allowing IUBIK to
isolate them; our profiler recorded 292 privileged and 212 non-privileged allocation sites
for a diverse set of workloads. Finally, we evaluate an implementation of IUBIK for the
Linux kernel, across a suite of micro- and macro-benchmarks, demonstrating that our
prototype incurs no runtime overhead in most tests and negligible additional memory
consumption.

Further information about the design and implementation of IUBIK can be found in our
[paper](https://ieeexplore.ieee.org/abstract/document/11023322) presented at S&P 2025.

```
@inproceedings{momeu2025iubik,
  title={IUBIK: Isolating User Bytes in Commodity Operating System Kernels via Memory Tagging Extensions},
  author={Momeu, Marius and Gaidis, Alexander J and vd Heidt, Jasper and Kemerlis, Vasileios P},
  booktitle={2025 IEEE Symposium on Security and Privacy (SP)},
  pages={867--885},
  year={2025},
  organization={IEEE}
}
```

## NEW!!

IUBIK can now isolate user-controlled memory allocated by `Buddy`! Developers can now 
pass the `__GFP_IUBIK` flag when allocating pages to isolate them in DomU.

## Build Instructions

Intall [`repo`](https://gerrit.googlesource.com/git-repo/+/HEAD/README.md).

Clone Linux kernel v5.15 repo for Pixel 8 (Android 14):

```bash
repo init -u https://android.googlesource.com/kernel/manifest -b android-gs-shusky-5.15-android14-qpr3
repo sync -c --no-tags
```

Apply IUBIK patches:
```bash
cd <linux-kernel-android>
cd aosp
git apply iubik-kernel.patch
cd -
cd private/google-modules/wlan/bcm4383
git apply iubik-bcm4383.patch
cd -
cd private/google-modules/wlan/bcm4398
git apply iubik-bcm4398.patch
```

Build kernel (if you encounter errors see next item):
```
cd <linux-kernel-android>
BUILD_AOSP_KERNEL=1 ./build_shusky.sh
```

Flash images on the Pixel 8 and reboot:
```
adb reboot bootloader
fastboot flash boot out/shusky/dist/boot.img
fastboot flash vendor_kernel_boot out/shusky/dist/vendor_kernel_boot.img
fastboot flash dtbo out/shusky/dist/dtbo.img
fastboot reboot fastboot
fastboot flash system_dlkm out/shusky/dist/system_dlkm.img
fastboot flash vendor_dlkm out/shusky/dist/vendor_dlkm.img
fastboot reboot
```
