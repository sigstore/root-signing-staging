## root-signing-staging infrastructure

### Cloud key management

Online signing for snapshot and timestamp roles uses Google Cloud KMS.
* The current KMS keyid is defined in [root.json](https://github.com/sigstore/root-signing-staging/blob/main/metadata/root.json)
* The used KMS key can be changed with `tuf-on-ci-delegate`: this requires the maintainer to
  have the `cloudkms.publicKeyViewer` role for the new key, and will result in a root signing event.
* The service account and workload identity provider are stored in
  [repository variables](https://github.com/sigstore/root-signing-staging/settings/variables/actions)
* KMS management happens in the public-good-instance repository using the tuf module from
  [sigstore/scaffolding](https://github.com/sigstore/scaffolding)

### Bot account and custom token

Several workflows in this repository require elevated permissions. This is achieved using a custom token:
* Custom token is stored in repository secret `TUF_ON_CI_TOKEN`.
* Required token permissions are documented in tuf-on-ci
  [maintenance manual](https://github.com/theupdateframework/tuf-on-ci/blob/main/docs/REPOSITORY-MAINTENANCE.md#custom-github-token)
* token was created in the bot account @sigstore-bot and needs to be re-created once a year

### Repository publishing

root-signing-staging publishes a testing repository with GitHub Pages at https://sigstore.github.io/root-signing-staging/.

The final publish is done using Google Cloud Storage and is made available at https://tuf-repo-cdn.sigstage.dev/.
* The service account and workload identity provider are stored in
  [repository variables](https://github.com/sigstore/root-signing-staging/settings/variables/actions)
* Other details, like the GCS bucket name, are defined in the
  [deploy-to-gcs workflow](https://github.com/sigstore/root-signing-staging/blob/main/.github/workflows/deploy-to-gcs.yml)
* GCS management happens in the public-good-instance repository using the tuf module from
  [sigstore/scaffolding](https://github.com/sigstore/scaffolding)
