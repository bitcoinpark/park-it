# Park IT

Some initial documentation for managing the network and server infrastructure at the Park.

## Notes:
Proxmox Admin URL: https://10.31.0.6:8006/

## Secrets:
We're using SOPS (https://github.com/getsops/sops) for secrets management.

The gist is that for new secrets, you will encrypt them locally using the key at `./clusters/bplab/.sops.pub.asc` 

Ensure that you have have both gpg and the sops cli installed:
```
brew install gnupg sops
```
Clone this repo or download the `./clusters/bplab/.sops.pub.asc` public key, then import it into your keyring:
```
gpg --import ./clusters/bplab/.sops.pub.asc
```
Here's a basic example of how to create and encrypt a secret using SOPS:
```
# Create an example secret and save it as basic-auth.yaml
kubectl -n default create secret generic basic-auth \
--from-literal=user=admin \
--from-literal=password=change-me \
--dry-run=client \
-o yaml > basic-auth.yaml
```
Encrypt the secret with SOPS using the GPG key:
```
sops --config ./clusters/bplab/.sops.yaml --encrypt --in-place basic-auth.yaml
```
If you're having an issue with the above command, ensure that you're pointing to the config located at `./clusters/bplab/.sops.yaml`. This file contains `creation_rules` that dictate which key fingerprint to use for encryption.

Store this secret alongside your other manifests in this repo and it will be deployed and decrypted upon deployment to the cluster.