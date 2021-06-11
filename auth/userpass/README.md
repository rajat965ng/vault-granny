[Enable the userpass auth method]
```
vault auth enable userpass
```

[Create/Update User]
- Create a new user or update an existing user. 
- This path honors the distinction between the create and update capabilities inside ACL policies.

* **token_policies** (array: [] or comma-delimited string: "") 

``` List of policies to encode onto generated tokens. Depending on the auth method, this list may be supplemented by user/group/other values.```

* **token_bound_cidrs** (array: [] or comma-delimited string: "") 

```List of CIDR blocks; if set, specifies blocks of IP addresses which can authenticate successfully, and ties the resulting token to these blocks as well.```

 ```
 curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @create_user.json http://127.0.0.1:8200/v1/auth/userpass/users/chris
 ```

[Read User]
- Reads the properties of an existing username.
  
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" http://127.0.0.1:8200/v1/auth/userpass/users/chris | jq
``` 

[Delete User]
- This endpoint deletes the user from the method.
```
curl --header "X-Vault-Token: ..." --request DELETE http://127.0.0.1:8200/v1/auth/userpass/users/mitchellh
```

[Update Password on User]
- Update password for an existing user.
```
curl --header "X-Vault-Token: s.FKDlkxVNgkdHQdwOXT12rt7n" --request POST --data @update_password.json http://127.0.0.1:8200/v1/auth/userpass/users/chris/password
```

[Login]
- Login with the username and password.
```
curl --request POST --data @login.json http://127.0.0.1:8200/v1/auth/userpass/login/chris


```

[lookup token generated after successful login]

```
curl --header "X-Vault-Token: s.G0YtWgVtr9U7Wo9a6xMJXmBN" http://127.0.0.1:8200/v1/auth/token/lookup-self
```