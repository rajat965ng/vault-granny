- The cert auth method allows authentication using SSL/TLS client certificates which are either signed by a CA or self-signed.
- CA certificates are associated with a role; role names and CRL names are normalized to lower-case.
- To use this auth method, tls_disable must be false in the Vault configuration. This is because the certificates are sent through TLS communication itself.


[Configuration]
- Enable the certificate auth method:
```
vault auth enable cert
```

[Generate certificate format]
```
mkcert localhost
```

```
openssl genrsa -out ca.key 4096
openssl req -x509 -new -nodes -key ca.key -sha256 -days 1825 -out ca.crt
```

[Convert certificate into single line]
```
awk 'NF {sub(/\r/, ""); printf "%s\\n",$0;}'  ca.crt
```

[Create CA Certificate Role]
- Sets a CA cert and associated parameters in a role name.

```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST  --data @create.json http://127.0.0.1:8200/v1/auth/cert/certs/test-ca
```

[Read CA Certificate Role]
- Gets information associated with the named role.

```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n"  http://127.0.0.1:8200/v1/auth/cert/certs/test-ca | jq
```

[List Certificate Roles]
- Lists configured certificate names.
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request LIST http://127.0.0.1:8200/v1/auth/cert/certs | jq
```

[Delete Certificate Role]
- Deletes the named role and CA cert from the method mount.
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request DELETE http://127.0.0.1:8200/v1/auth/cert/certs/test-ca
```

[Create CRL]
- I’d prefer to define a certificate revocation list (CRL) as a blacklist of X.509 digital certificates that a CA revokes prior to their assigned expiration dates.
- It’s a way for CAs (or CRL issuers) to make it known that one or more of their digital certificates is no longer trustworthy for one reason or another.

[Configure TLS Certificate Method]
- Configuration options for the method.

```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @tls.json http://127.0.0.1:8200/v1/auth/cert/certs/test-ca | jq
```

[Login with TLS Certificate Method]
- Log in and fetch a token.
- If there is a valid chain to a CA configured in the method and all role constraints are matched, a token will be issued.

```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --key ca.key --cert cert.pem --data @login.json http://127.0.0.1:8200/v1/auth/cert/login | jq
```