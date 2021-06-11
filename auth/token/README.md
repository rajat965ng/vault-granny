[List Accessors]
- requires *sudo* capability, and access to it should be tightly controlled
``` 
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request LIST http://127.0.0.1:8200/v1/auth/token/accessors | jq
```

[Create Token]
- If used via the */auth/token/create-orphan* endpoint, a root token is not required to create an orphan token (otherwise set with the no_parent option)
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n"  --request POST --data @create_token.json http://127.0.0.1:8200/v1/auth/token/create 
```

[Lookup a Token]
- Returns information about the client token.
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @lookup_token.json http://127.0.0.1:8200/v1/auth/token/lookup
```  

[Lookup a Token (Self)]
- Returns information about the current client token.
```
curl --header "X-Vault-Token: s.Tv8khZZHic1uzONHDqk2pJfW" http://127.0.0.1:8200/v1/auth/token/lookup-self
```

[Lookup a Token (Accessor)]
- Returns information about the client token from the accessor.

```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @accessor_token.json http://127.0.0.1:8200/v1/auth/token/lookup-accessor
```

[Renew a Token]
- Renews a lease associated with a token.
- Prevent the expiration of a token, and the automatic revocation.

```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @renew_token.json http://127.0.0.1:8200/v1/auth/token/renew
```

[Renew a Token (Self)]
- Renews a lease associated with the calling token. 

```
curl --header "X-Vault-Token: ..." --request POST --data @payload.json http://127.0.0.1:8200/v1/auth/token/renew-self
```

[Renew a Token (Accessor)]
- Renews a lease associated with a token using its accessor. 

```
curl --header "X-Vault-Token: ..." --request POST --data @payload.json http://127.0.0.1:8200/v1/auth/token/renew-accessor
```

[Revoke a Token]
- Revokes a token and all child tokens. When the token is revoked, all dynamic secrets generated with it are also revoked.

```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @revoke_token.json http://127.0.0.1:8200/v1/auth/token/revoke
```