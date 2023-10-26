## Sigstore root-signing-staging

Sigstore uses a TUF repository as the delivery mechanism for the keys and certificates
that Sigstore clients need. Please see [root-signing](https://github.com/sigstore/root-signing).

This project maintains a **staging** version of the root-signing TUF repository using
[tuf-on-ci](https://github.com/theupdateframework/tuf-on-ci): this is a development and testing
resource and should never be used as an actual source of truth by Sigstore clients.

### Status

root-signing-staging is not yet ready for use:
* Project is currently being setup and is not fully functional
* While the plan is to eventually maintain root-signing with the same processes as
  root-signing-staging, this is not currently the case

### Contact

* Feel free to file an issue on this project
* [tuf-on-ci](https://github.com/theupdateframework/tuf-on-ci) issue tracking may be
  most useful for software issues, 
  [tuf-on-ci slack channel](https://cloud-native.slack.com/archives/C04SHK2DPK9)
  on CNCF slack works too
