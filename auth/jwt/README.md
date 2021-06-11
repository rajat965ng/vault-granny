[configuration]
```
 vault auth enable jwt
```

[Generate private key]
```
openssl genrsa -out private.pem 2048
```

[Generate public key]
```
openssl rsa -in private.pem -outform PEM -pubout -out public.pem
```

[Generate JWKS from pem file]
```
npm install -g pem-jwk
pem-jwk public.pem | jq 'tostring'
```



[Configure]
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @config.json http://127.0.0.1:8200/v1/auth/jwt/config
```

[Read Config]
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" http://127.0.0.1:8200/v1/auth/jwt/config | jq
```

[Create Role]
- Registers a role in the method.
- Role types have specific entities that can perform login operations against this endpoint.
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @create_role.json http://127.0.0.1:8200/v1/auth/jwt/role/demo | jq
```

[Read Role]
- Returns the previously registered role configuration.
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" http://127.0.0.1:8200/v1/auth/jwt/role/demo | jq
```

[Convert date into EPOCH (command line)]
```
date -j -f '%Y%m%d%H%M%S' 20210530073900 +%s
```

[JWT Login]
- Fetch a token. This endpoint takes a signed JSON Web Token (JWT) and a role name for some entity. 
- It verifies the JWT signature to authenticate that entity and then authorizes the entity for the given role.
```
curl --request POST --data @login.json http://127.0.0.1:8200/v1/auth/jwt/login | jq
```

[Token Lookup]
```
curl --header "X-Vault-Token: s.Tv8khZZHic1uzONHDqk2pJfW" http://127.0.0.1:8200/v1/auth/token/lookup-self | jq
```

[Keycloak OIDC - Generate Token]
```
curl -XPOST http://localhost:8080/auth/realms/master/protocol/openid-connect/token -d grant_type=password -d client_id=vault -d username=rajat.nigam -d password=test1234 -d scope=openid -d response_type=id_token | jq
```

[Configure Keycloak]
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @ck_config.json http://127.0.0.1:8200/v1/auth/jwt/config
```

[Create Role Keycloak]
- Registers a role in the method.
- Role types have specific entities that can perform login operations against this endpoint.
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @ck_create_role.json http://127.0.0.1:8200/v1/auth/jwt/role/keycloak | jq
```

[OIDC Authorization URL Request]
- Obtain an authorization URL from Vault to start an OIDC login flow.
```
curl --request POST --data @ck_auth_req.json http://127.0.0.1:8200/v1/auth/jwt/oidc/auth_url | jq
```

```
http://localhost:8080/auth/realms/master/protocol/openid-connect/auth?client_id=vault&response_type=code&state=st_Q1fVwSB0FyqCQQrCfhRe

curl -X POST -H "Content-Type: application/x-www-form-urlencoded" http://IP_ADDR:8080/auth/realms/master/protocol/openid-connect/auth?client_id=vault&nonce=n_ZiN30jStScehaco9wMtY&redirect_uri=http%3A%2F%2FIP_ADDR%3A8200%2Fui%2Fvault%2Fauth%2Fjwt%2Foidc%2Fcallback&response_type=code&scope=openid&state=st_p69IHVKSnpNvlsbJv8Ms

curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -u "rajat.nigam:testuser1234" -d "client_id=vault&nonce=n_xdE4M3Uwz9RPey59raIg&redirect_uri=http%3A%2F%2FIP_ADDR%3A8200%2Fui%2Fvault%2Fauth%2Fjwt%2Foidc%2Fcallback&response_type=code&scope=openid&state=st_ofv4P1V0T78MHQucBguV" http://IP_ADDR:8080/auth/realms/master/protocol/openid-connect/auth


http://IP_ADDR:8200/ui/vault/auth/jwt/oidc/callback?state=st_ofv4P1V0T78MHQucBguV&session_state=6fcb2deb-a049-46f8-9b69-7cbb80850e87&code=a1bd4c29-774f-478b-9507-2172d5aaaff0.6fcb2deb-a049-46f8-9b69-7cbb80850e87.11be143e-35e6-4c2c-9a13-be9851b6c033
```

[OIDC Callback]
- Exchange an authorization code for an OIDC ID Token. 
- The ID token will be further validated against any bound claims, and if valid a Vault token will be returned.
```
curl -k http://127.0.0.1:8200/v1/auth/jwt/oidc/callback?state=st_ofv4P1V0T78MHQucBguV&nonce=n_xdE4M3Uwz9RPey59raIg&code=a1bd4c29-774f-478b-9507-2172d5aaaff0.6fcb2deb-a049-46f8-9b69-7cbb80850e87.11be143e-35e6-4c2c-9a13-be9851b6c033 | jq
```


[Get Access Token of keycloak user from vault]
curl -X POST 'http://127.0.0.1:8080/auth/realms/master/protocol/openid-connect/token' \
 -H "Content-Type: application/x-www-form-urlencoded" \
 -d "username=rajat.nigam" \
 -d 'password=abc123' \
 -d 'grant_type=password' \
 -d 'client_id=vault' | jq


curl --request POST --data '{"jwt": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICIyLWQtd1RGZEZaTHpnd1BUUVJrWjliV1pRMUV3bFBPek81Q3I5eG9RR0pRIn0.eyJleHAiOjE2MjI0Mzg3NTksImlhdCI6MTYyMjQzODY5OSwianRpIjoiZWIzYTEyNWQtYjMzZS00MTQzLThkN2EtOTkzY2Q3ZDRiYzNkIiwiaXNzIjoiaHR0cDovLzEyNy4wLjAuMTo4MDgwL2F1dGgvcmVhbG1zL21hc3RlciIsImF1ZCI6ImFjY291bnQiLCJzdWIiOiJlZTI5Y2JmNy1hYzFlLTQzMDEtOWVjYi02MTk3MDY0ZmY4OWEiLCJ0eXAiOiJCZWFyZXIiLCJhenAiOiJ2YXVsdCIsInNlc3Npb25fc3RhdGUiOiI4ZTU2OGVjYS02NmMzLTQ5YjktYTZhMi1iNTRkMzBjNDM1NjUiLCJhY3IiOiIxIiwicmVhbG1fYWNjZXNzIjp7InJvbGVzIjpbImRlZmF1bHQtcm9sZXMtbWFzdGVyIiwib2ZmbGluZV9hY2Nlc3MiLCJ1bWFfYXV0aG9yaXphdGlvbiJdfSwicmVzb3VyY2VfYWNjZXNzIjp7InZhdWx0Ijp7InJvbGVzIjpbInZhdWx0X3JvbGUiXX0sImFjY291bnQiOnsicm9sZXMiOlsibWFuYWdlLWFjY291bnQiLCJtYW5hZ2UtYWNjb3VudC1saW5rcyIsInZpZXctcHJvZmlsZSJdfX0sInNjb3BlIjoicHJvZmlsZSBlbWFpbCIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwibmFtZSI6IlJhamF0IE5pZ2FtIiwicHJlZmVycmVkX3VzZXJuYW1lIjoicmFqYXQubmlnYW0iLCJnaXZlbl9uYW1lIjoiUmFqYXQiLCJmYW1pbHlfbmFtZSI6Ik5pZ2FtIiwiZW1haWwiOiJyYWphdG5pZ2FtODlAZ21haWwuY29tIn0.iLcPk8oG869LF7AZQ7f5ghbcel_iwTTmilRean1D7amra_7SMAYt7ljUINYuUElmUF2_39zsUiYqiMb2j-yvwUpRw4lKnwbunM8HsjqER0yxfeNCR7NDLnPBfNx9ESqnwmrthvy_bOQvI0Ud5xvMKYg0yVmLjPqBLjHkeAv94cm62ABIhWzqahwMPQwtvwkd_zYr3y12YTHg-tFnU87nWyhWLilQWum3tQrwXbB9gko_ubXyeoIgr-j4r6tdy90PsZVTPnSu5JoT1TMzVdUNB2t2vsBuqyeQK6DBv_-zzoxNgd0cggIaqypbGe23xW2rN8jrtGh3oC54p31aOx0GSw", "role": "keycloak"}' http://127.0.0.1:8200/v1/auth/jwt/login | jq