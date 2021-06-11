vault secrets enable transit

{
"type": "aes256-gcm96",
"derived": false
}

curl -H "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" -d @create-payload.json http://localhost:8200/v1/transit/keys/myapp


echo "{ \"plaintext\": \"$(echo "ajar ajar" | base64)\"}" > req_payload.json

curl -H "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" -d @req_payload.json http://localhost:8200/v1/transit/encrypt/myapp


curl -H "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" -d @req_payload.json http://localhost:8200/v1/transit/encrypt/myapp | jq ".data" | tee cipher.json

curl -H "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" -d @cipher.json http://localhost:8200/v1/transit/decrypt/myapp | jq -r ".data.plaintext" | base64 -d







