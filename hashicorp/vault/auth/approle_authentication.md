---
title: Configure AppRole Authentication with HashiCorp Vault using Vault CLI
---
# Configure AppRole Authentication with HashiCorp Vault using Vault CLI
## Setup the dev environment
1. Create a dev server
```bash
vault server -dev -dev-root-token-id=root
```
2. Add Credentials as Environment Variables
```bash
export VAULT_ADDR=http://127.0.0.1:8200
export VAULT_TOKEN=root
```
## Enable AppRole Authentication
```bash
vault auth enable approle
```
Note: You can mount the auth method to a specific path using the `-path` option.
## Create a Role
1. Create a file called `payload.json`
```bash
touch payload.json
```
2. Add configurations to `payload.json`
```json
{
  "token_ttl":3600,
  "token_max_ttl":86400
}
```
Here the time to live (TTL) of the token is set to 1h and the max TTL of the token is being set to 24 hours.
3. Create a role with the configurations in `payload.json`
```bash
vault write /auth/approle/role/my-role @payload.json
```
4. Read the created Role
```bash
vault read /auth/approle/role/my-role
```
**Sample Output**
```
Key                        Value
---                        -----
bind_secret_id             true
local_secret_ids           false
secret_id_bound_cidrs      <nil>
secret_id_num_uses         0
secret_id_ttl              0s
token_bound_cidrs          []
token_explicit_max_ttl     0s
token_max_ttl              24h
token_no_default_policy    false
token_num_uses             0
token_period               0s
token_policies             []
token_ttl                  1h
token_type                 default
```
## Generate a RoleId
```
vault read /auth/approle/role/my-role/role-id
```
**Sample Output**
```
Key        Value
---        -----
role_id    c9293fe1-ff30-1130-0b4f-ee48a77db8ae
```
## Generate a SecretId (Pull Method)
```
vault write -f /auth/approle/role/my-role/secret-id
```
Here the `-f` option is used since we are using the pull method to generate a SecretId.
***Sample Output***
```
Key                   Value
---                   -----
secret_id             825dc111-08b2-3793-234e-e3f7d50b6ff5
secret_id_accessor    4d82a758-5ec6-949c-4874-0b90f060fd0b
secret_id_ttl         0s
```
