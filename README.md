Manifest Checker
================

- [What it does](#what-it-does)
- [Running](#running)
- [Creating-the-manifest](#creating-the-manifest)


## What it does

  Manifest Checker is the component that implements the transition of the attested booting process from
  a static root of trust to a dynamic root of trust. It will check that the booted file-system has a valid
  manifest and extend the TPM PCRs (platform configuration registers) with the hash of the manifest. This
  will allow us to update the software running on the cloaks and keep the data present there, assuming that
  the new version of the software was signed with the same keys as the previous version.


## Running

The Manifest Checker needs an action and root path as inputs.

The following actions are supported:

  - inspect : inspects the file-system and outputs to stdout the computed hash for it.

  - verify : inspects the file-system, verifies that the file-system has a valid manifest 
  and outputs to stdout the computed hash for the manifest.

  - attest : inspects the file-system, verifies that the file-system has a valid manifest
  and extends PCR10 with the hash of the manifest is successful, otherwise extends PCR10
  with the string "Invalid manifest ..."; if it returns an error, the caller must stop the boot process.

The Manifest Checker expects the manifest to be at "/aircloak/manifest", inside the file-system to be checked.

A manifest consists of:

  - exceptions : the file containing the paths to be excepted from inspection (one path per line,
  comments can be added with #, white-spaces are trimmed before checking, empty lines are ignored).

  - public_keys : the folder containing the public keys of the trusted signers for the cloak; the manifest
  hash is computed from this list.

  - signatures : the folder containing the signatures for the file-system hash, one for each public key present.

## Creating the manifest

Generate a new RSA key protected with a password, if you don't already have one:
  ```openssl genrsa -aes128 -passout stdin -out testkey.pem 2048```

Extract the public key from the RSA key: ```openssl rsa -in testkey.pem -pubout > testkey.pub```

Run Manifest Checker on the target file-system to obtain the hash: ```manifest_checker inspect / > fs_hash```

Sign the hash: ```openssl rsautl -sign -in fs_hash -inkey testkey.pem -out signature```

Manually view the file-system hash from a signature:
  ```openssl rsautl -verify -in signature -inkey testkey.pub -pubin```

Manually inspect and check the manifest of a file-system: ```manifest_checker verify / > manifest_hash```

## Auditor Notes

In addition to inspecting the source code itself, the auditor may run a number of tests on a working cloak.  For each test, the auditor makes each of the following modifications to an otherwise correct cloak image, installs and boots the cloak, and observes that the manifest_checker refuses to allow the cloak to boot:

1. Modify any file in the image (except those listed in `/aircloak/manifest/exceptions`).
2. Modify or remove the auditor public key in `/aircloak/manifest/public_keys/`.
3. Modify or remove any file-system hash signature in `/aircloak/manifest/signatures/`.
