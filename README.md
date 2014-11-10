# Auditor Guidelines

The auditor can verify from the SELinux policies that erlattest has no access to user data or postgresql.  

erlattest has access to the sealed crypto-disk key (at `/aircloak/common/config/runtime/sealedfskey`).  The auditor may inspect the code to ensure that erlattest never attempts to unseal the key.  (Note however that even such an attempt would fail, because the TPM PCRs will have been extended one last time after unsealing during boot.)

erlattest receives messages from nginx via port 13500 (attestation in nginx.conf) via the URLs `/attest-strong` and `/attest`.

From fast_attestation_resource.erl, the auditor can see that erlattest expects only a challenge, and uses it only in the attestation function.  From strong_attestation_resource.erl, the auditor can see that erlattest expects only a binary blob, and uses it only in the attestation function.
