## Running Vault and Consul on Kubernetes in High Availability Mode


### Prerequisites

You will need do  the following:
1. Clone this repo
1. Install [Go](https://golang.org/doc/install)
1. Install CloudFlare's [SSL ToolKit](https://github.com/cloudflare/cfssl) (`cfssl` and `cfssljson`)
1. Install [Consul](https://www.consul.io/docs/install/index.html)
1. Install [Vault](https://www.vaultproject.io/docs/install/)
1. Install [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)

### Minikube

Start the cluster:

```sh
$ minikube start --vm-driver=virtualbox
$ minikube dashboard
```

### TLS Certificates

Create a Certificate Authority:

```sh
$ cfssl gencert -initca certs/config/ca-csr.json | cfssljson -bare certs/ca
```

Create the private keys and TLS certificates:

```sh
$ cfssl gencert \
    -ca=certs/ca.pem \
    -ca-key=certs/ca-key.pem \
    -config=certs/config/ca-config.json \
    -profile=default \
    certs/config/consul-csr.json | cfssljson -bare certs/consul

$ cfssl gencert \
    -ca=certs/ca.pem \
    -ca-key=certs/ca-key.pem \
    -config=certs/config/ca-config.json \
    -profile=default \
    certs/config/vault-csr.json | cfssljson -bare certs/vault
```

### Vault and Consul

Spin up Vault and Consul on Kubernetes:

```sh
$ sh create.sh
```

### Environment Variables

In a new terminal window, navigate to the project directory and set the following environment variables:

```sh
$ export VAULT_ADDR=https://127.0.0.1:8200
$ export VAULT_CACERT="certs/ca.pem"
```

### Initialize Vault

cd into the directory of the project. my project is in ```/users/matthewpeters/git/vault-consul-kubernetes```
run the vault commands below

```sh
$ cd /users/matthewpeters/git/vault-consul-kubernetes
$ vault operator init -key-shares=1 -key-threshold=1
$ vault status 
```


### Vault and Consul UI

 Vault UI:
 ```sh $ https://127.0.0.1:8200 ```

Consul UI:
 ```sh $ https://127.0.0.1:8080 ```

