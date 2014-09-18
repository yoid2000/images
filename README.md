init
==================

## Auditor Notes

This directory contains Aircloak scripts that are run by /sbin/init during the "post-kernel" phase of booting.  These scripts are installed in the /etc/rc0.d through /etc/rc6.d directories by the `Aircloak/cloak-debian-setup/setup_cloak_image.sh` script.  These scripts run under the initrc_t SELinux domain, and therefore have root privledges.

`tpm_init.sh` is the script that unseals the crypto-disk key and sets up the crypto-disk, thus making user data readable.  Note that all rcX.d boot scripts running before tpm_init.sh cannot access user data.  The daemons launched by the scripts running before `tpm_init.sh` (i.e. tcsd and syslog) are transitioned to their own SELinux domains, and are constrained by their SELinux policies from reading any user data or crypto private keys.

The auditor should inspect the `tpm_init.sh` script and the source code for the various binaries that it calls (found in `Aircloak/cloak-debian-setup/attestation`).

The auditor may also wish to run tests on an operating cloak to ensure that `tpm_init.sh` is operating correctly:

1.  After running a correct cloak, modify the manifest checker and check that the subsequently booted new cloak is unable to unseal the crypto-disk key.
1.  Inspect the disk of a cloak and check that it is encrypted.
1.  Remove the disk of one cloak, and install it on another machine.  Check that the other machine fails to unseal the crypto-disk key (even if the other machine has an identical hardware configuration, including the same type of TPM).

`tpm_init.sh` starts and monitors all Aircloak processes and other dependent processes (if not already started earlier in the boot process).  The auditor may validate from the SELinux policies that each such process runs under its own SELinux domain.
