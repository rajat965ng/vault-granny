- kubectl create ns secret-manager

- git clone https://github.com/hashicorp/vault-helm.git
- helm install vault vault-helm/ -n secret-manager

- kubectl -n secret-manager exec -it vault-0 vault operator init

```
Unseal Key 1: ZuSLQn1arojTAY2ofIhg0ahdjFSuMxksqBEMQ5gIEwpq
Unseal Key 2: Ex068y3POYJQSN2oxYxzJa3fyNti2TjUPNBFO8vdnUtS
Unseal Key 3: y/hXvRZ+wMGqAxSS7O23pW8oK/032bTsi1FROfrObyYw
Unseal Key 4: eyTbc0F6AG7OsZsQfYBPRky4lg54cmu1RBen1et+6+jm
Unseal Key 5: iGdGt7eTlYAvTbZbDF1pQkEMpJRNEzuCaRX7mUrfyVdA

Initial Root Token: s.p3HUvS27mkEq77wW7lY9AtcK

```
- kubectl -n secret-manager exec -it vault-0 vault operator unseal

- kubectl -n secret-manager exec -it vault-0 vault login

- kubectl -n secret-manager  exec -it vault-0 vault auth enable kubernetes

- kubectl -n secret-manager exec -it vault-0 vault write auth/kubernetes/config token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443" kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt

- vault write auth/kubernetes/role/demoapp \
    bound_service_account_names=demoapp-ac \
    bound_service_account_namespaces=msapp \
    policies=demoapp-policy \
    ttl=24h
    

- vault policy write demoapp-policy - <<EOF
path "secret/demoapp/*" {
  capabilities = ["read"]
}
EOF


- vault secrets enable -path=secret/ kv
      
- vault kv put secret/demoapp/dev username=dbuser password=sUp3rS3cUr3P@ssw0rd

- kubectl create ns msapp

- kubectl apply -f app.yml