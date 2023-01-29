# kubernetes-cluster-vault

## Vault
O Vault é um sistema de gerenciamento de secrets e criptografia baseado em identidade. Para mais informações sobre o Vault, consulte a[documentação do Hashicorp Vault.](https://www.vaultproject.io/).

## Download do project

```
git clone https://github.com/danielbatubenga/kubernetes-cluster-vault.git
cd kubernetes-cluster-vault
```

## Setup
[Este](https://github.com/danielbatubenga/kubernetes-cluster-vault/blob/main/kind-config.yaml) é o exemplo do cluster kubernetes no kind com 3 workers.

crie o cluster 

```
kind create cluster --name vault --config kind-config.yaml
```

rode os manifestos kubernetes

```
kubectl apply -f rbac.yaml
kubectl apply -f configmap.yaml
kubectl apply -f services.yaml
kubectl apply -f statefulset.yaml
```

Inicializando o vault

Você precisar instal o jq, para executar essa etapa.

```
kubectl exec vault-0 -- vault operator init -key-shares=1 -key-threshold=1 -format=json > keys.json

VAULT_UNSEAL_KEY=$(cat keys.json | jq -r ".unseal_keys_b64[]")
echo $VAULT_UNSEAL_KEY

VAULT_ROOT_KEY=$(cat keys.json | jq -r ".root_token")
echo $VAULT_ROOT_KEY
```

vault unseal

```
kubectl exec vault-0 -- vault operator unseal $VAULT_UNSEAL_KEY
´´´

Logando no vault pelo CLI.

```
kubectl exec -it vault-0 -- /bin/sh
´´´

A partir daqui você já pode criar os seus segredos e fazer as suas integrações.