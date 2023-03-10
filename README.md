# learn-vault-hello-world

A sample app to test Vault KV engine with root token and k8s authentication

![kubernetes](/images/kubernetes.png)

## Local Development

This application is built to run in a cluster and not locally. It would take
some additional changes to have it work locally.

### Run a local vault server
```shell
$ vault server -dev -dev-root-token-id=root
$ vault kv put secret/webapp/config username="static-user" password="static-password"
```

### Run the local app server
```shell
$ export VAULT_ADDR="http://localhost:8200"
$ go run *.go
```

## Docker Image

Build and push the Docker image. (multi-arch)

```shell
$ docker buildx build --push --platform linux/amd64,linux/arm64,linux/arm/v7 -t USERNAME/simple-vault-client .
```

## Load it into Kubernetes

The assumption is helm installed and k8s cluster is configured. Update the configuration file to use your Docker image.

Add the HashiCorp Helm repository and update repositories for the latest versions.

```shell
$ helm repo add hashicorp https://helm.releases.hashicorp.com
$ helm repo update
```

Install the latest version of the Vault server running in development mode.

```shell-session
$ helm install vault hashicorp/vault --set "server.dev.enabled=true"
```

Apply the configuration that describes the vault-hello-world pod.

```shell
$ kubectl apply -f deploy.yaml
```

Check the logs of the server.

```shell
$ kubectl logs webapp-e54r445b4c-psdlk
```

Login to the instance.

```shell
$ kubectl exec -it webapp-e54r445b4c-psdlk /bin/bash
```