images
======

## TrustedGRUB

TrustedGRUB is a modified version of GRUB 1.0 that will extend the chain of trust
in the TPM by measuring the boot loader code, the boot settings, the loaded kernel
and the initial RAM disk image used during the boot process.

The original source of the code can be found at:
http://sourceforge.net/projects/trustedgrub/files/TrustedGRUB-1.1.5/

Unfortunately, the project doesn't seem to be actively maintained anymore (last update was in 2010).

Aircloak provides the original archive here for convenience. The auditor can download the project
from the source, unpack it and then check the integrity of the source code archive with a simple
file comparison tool.
The build script was slightly updated to compile with the latest autoconf tool and to
output the relevant binaries in 'bin' sub-folder.

The boot-loader is assembled directly from the stage1 and stage2 code and the fixed MBR
cloak partition information. During the cloak setup process, it is directly copied
(using the 'dd' tool) at the beginning of the cloak disk, before the root partition
(as enough space is present there for the entire boot-loader to fit).
This process will ensure we get consistent measurements in the TPM registers.
If we had used the original installation process, that would need to store the raw disk
offset where stage2 is stored on disk (as stage1.5 doesn't have support for extending
the trust chain) and we would get non-deterministic measurements.

## Auditor Notes

Projects from sourceforge are not signed, and so the auditor must manually compare the sourceforge software with that used in this repository.  To do so, the following steps may be taken:

1.  Download the archive TrustedGRUB-1.1.5.src.tar.gz from sourceforge.net/projects/trustedgrub/, and unpack it.

1.  Within the downloaded archive is another archive with the same name (TrustedGRUB-1.1.5.src.tar.gz).  Check that this inner archive is identical to that in the repository:
`https://github.com/Aircloak/cloak-automation/blob/master/TrustedGRUB/TrustedGRUB-1.1.5.src.tar.gz`

1.  Compare the build script build_tgrub.sh from sourceforge.net with that used by Aircloak in:
`https://github.com/Aircloak/cloak-automation/blob/master/TrustedGRUB/build_tgrub.sh`
Validate that the minor differences do not modify the functionality of TrustedGRUB.

1. The TrustedGRUB bootloader configuration file menu.lst is copied into /boot/grub by create_base_image.sh.  Verify that menu.lst is correct (both in this repository and in the cloak image):
  1. The ```root``` command sets to (hd0,0) (1st hard-disk, 1st partition).
  1. The ```kernel``` command sets to /vmlinuz, and that /vmlinuz points to the debian kernel image /boot/vmlinuz-3.2.0-4-amd64.
  1. The ```kernel``` command enables SELinux (```selinux=1 security=selinux```).
  1. The ```initrd``` command selects /initrd.img, which points to /boot/initrd.img-3.2.0-4-amd64 zzzz what is this?.

Note that the file "default" is not used by cloaks.  It is copied by build_tgrub.sh as a hold-over from the original version of build_tgrub.sh, because we wish to minimize changes to build_tgrub.sh.  Never-the-less, the auditor can verify that the file "default" from sourceforge.net is identical to:

https://github.com/Aircloak/cloak-automation/blob/master/TrustedGRUB/default
