# Signer manual for root-signing-staging

## One-time setup

### Yubikey configuration

Generate a PIV Digital Signature key on your hardware key if you don't have one yet.

Using [Yubikey Manager](https://www.yubico.com/support/download/yubikey-manager/)
this is possible in _Applications -> PIV -> Configure certificates -> Digital signature_.

### Software install

* Install tuf-on-ci-sign
  ```
  # this example uses a virtualenv: feel free to install tuf-on-ci-sign elsewhere
  python3 -m venv ~/.venvs/tuf-on-ci-sign
  source ~/.venvs/tuf-on-ci-sign/bin/activate
  pip install tuf-on-ci-sign

  # If you are on MacOS and the install fails, you can try
  #   brew install swig
  ```
* Install Yubicos PKCS#11 module
  * on Debian `sudo apt install ykcs11`
  * on MacOS `brew install yubico-piv-tool`

### Repository setup

* Fork the repository on github: https://github.com/sigstore/root-signing-staging/fork
* clone your fork and add the upstream as a remote:
  ```
  git clone https://github.com/<YOUR-GITHUB-USER-NAME>/root-signing-staging.git
  cd root-signing-staging
  git remote add upstream https://github.com/sigstore/root-signing-staging.git
  ```
* Create `.tuf-on-ci-sign.ini` with this content:
  ```
  [settings]
  pykcs11lib = <PATH-TO-PKCS11-MODULE>
  user-name = @<YOUR-GITHUB-USER-NAME>
  push-remote = origin
  pull-remote = upstream
  ```
  Replace pykcs11lib value with the location of the PKCS11 module installed earlier: On linux it may be
  `/usr/lib/x86_64-linux-gnu/libykcs11.so` or `/usr/local/lib/libykcs11.so`. On Mac maybe
  `/opt/homebrew/lib/libykcs11.dylib` or `/usr/local/lib/libykcs11.dylib`.

## Signing

When a signing event asks you to sign or to accept an invite:
* Change into `root-signing-staging` directory
* Enter your virtualenv if you use one: `source ~/.venvs/tuf-on-ci-sign/bin/activate`
* Run signing tool: `tuf-on-ci-sign <SIGNING-EVENT>`
  * if you are accepting an invite, choose "Yubikey" as your key type
  * if you are signing, review the changes
  * Signing automatically commits the signature and pushes it to a branch on your fork
* After signing, click the provided link to create a PR to the signing event branch
