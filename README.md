# K3S cluster

Configuring private registries

Note that your k3s cluster needs a configuration file `/etc/rancher/k3s/registries.yaml` for the authentication.
```
mirrors:
  ghcr:
    endpoint:
      - "https://ghcr.io"
configs:
  "ghcr.io":
    auth:
      username: $YOUR_USERNAME
      password: $YOUR_PRIVATE_ACCESS_TOKEN
```
