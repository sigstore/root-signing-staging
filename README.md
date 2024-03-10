## Sigstore root-signing-staging

Sigstore uses a TUF repository to securely deliver the _Sigstore trust root
(trusted_root.json)_ to Sigstore clients, see
[root-signing](https://github.com/sigstore/root-signing). This project maintains
a **staging** version of the root-signing TUF repository using
[tuf-on-ci](https://github.com/theupdateframework/tuf-on-ci): this is a development
and testing resource and should never be used as an actual source of truth by
Sigstore clients.

While the plan is to eventually maintain root-signing with the same processes as
root-signing-staging, this is not currently the case.

More detail:
* [infrastructure doc](docs/infrastructure.md) goes into detail about the required
  services and configuration.
* [signer manual](docs/signer-manual.md) documents the process and requirements from
  signers perspective

### Operation

root-signing-staging publishes the TUF repository at `https://tuf-repo-cdn.sigstage.dev/` 
(see e.g. [current timestamp metadata](https://tuf-repo-cdn.sigstage.dev/timestamp.json)).

The TUF repository is modified in two ways:
1. _signing events_ where human signers collaborate to sign changes with hardware keys and
2. _online signing_ where the root-signing-staging machinery signs changes using KMS keys

#### Signing events

Signing events are pull requests created and managed by root-signing-staging. They may happen
for multiple reasons:
* Maintainer proposes a change to trusted_root.json
* Maintainer proposes a change to repository configuration (signer list, signature thresholds, etc)
* root-signing-staging proposes resigning when signatures are close to expiry

In all cases the trigger to creating a signing event is a push to a "sign/*"
branch (either by maintainer or a workflow) .

#### Online signing

Online signing happens in two situations:
* A signing event PR has been merged
* A online signature is close to expiry

In practice online signing happens at least every three days because of online signature expiry.

### Workflows

The important workflows in root-signing-staging are:
* `create-signing-events` creates branches for signing events when signatures are close to expiry.
  Runs on schedule
* `signing-event` creates and manages the signing event pull requests. Runs when "sign/*" branches
  are pushed to
* `online-sign` commits and merges online signatures, also dispatches `publish`. Runs on when
  "main" is pushed to (but can be manually dispatched at any time)
* `publish` publishes a test repository to GitHub Pages, runs client tests, and finally publishes
  the repository. Runs on dispatch from `online-sign`

### Contact

* Feel free to file an issue on this project
* [tuf-on-ci](https://github.com/theupdateframework/tuf-on-ci) issue tracking may be
  most useful for software issues, 
  [tuf-on-ci slack channel](https://cloud-native.slack.com/archives/C04SHK2DPK9)
  on CNCF slack works too
