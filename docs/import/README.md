This file documents the import process for this repository

The starting point was the "static" repository in https://github.com/sigstore/root-signing.git staging/ subdirectory:
All keys were file-based keys committed in the repository, there were no automated maintenance processes except publishing
to GCS.

The goal is:
* A repository managed by tuf-on-ci in a separate GitHub project that can be used to test out new ideas in the sigstore
  root-signing trust root delivery system
* Processes that are closer to the production root-signing processes
* An example to later move the production root-signing repository to tuf-on-ci as well
* Uninterrupted service for staging using sigstore clients
* This initial signing event aims to add just a single maintainer to keep things a bit simpler:
  others can be added (and import user can be removed) in future signing events

## Preparation

In a normal PR (so not a signing event):
* Copy metadata and artifacts from https://github.com/sigstore/root-signing.git
* Rewrite files with python-tuf (just to get whitespace consistency so that future reviews are easy):
  ```
  from tuf.api.metadata import Metadata
  from tuf.api.serialization.json import JSONSerializer

  for rolename in ["root", "timestamp", "snapshot", "targets", "registry.npmjs.org"]:
      md: Metadata = Metadata.from_file(f"metadata/{rolename}.json")
      md.to_file(f"metadata/{rolename}.json", JSONSerializer())
  ```

## Initial signing event (planned)

### as myself (@jku)

* Run `tuf-on-ci-import-repo sign/import` to get the required configuration
* Fill in the configuration values (see docs/import/import-config.json)
* run `tuf-on-ci-import-repo sign/import docs/import/import-config.json` to apply the config
* Add myself as 2nd root signer: `tuf-on-ci-delegate sign/import root`
* Make myself sole signer for targets and npmjs `tuf-on-ci-delegate sign/initial-import targets`,
  `tuf-on-ci-delegate sign/initial-import registry.npmjs.org`
* Setup proper online signing: `tuf-on-ci-delegate sign/initial-import timestamp`

### as the "import user"

* Use a temporary configuration .tuf-on-ci-sign.ini for the import user: This user and key will only be used
  once during the initial signing event.
  ```
  # username for the import key owner
  user-name = @-repo-import
  # import key: this is the key from https://github.com/sigstore/root-signing/tree/main/staging/keys/76651934
  [signing-keys]
  c8e09a68b5821b75462ae0df52151c81deb7f1838246dc1da8c34cc91ec12bda = file:docs/import/import_root_priv.pem?encrypted=false
  ```
* Sign with the import key: `tuf-on-ci-sign sign/initial-import`

At this point the signing event should be ready.
