---
title: VaulTLS
description: Set up VaulTLS with Pocket ID authentication
---

## Pocket ID Setup

1. In Pocket ID create a new OIDC Client, name it i.e. `vaultls`.
2. Set a logo for this OIDC Client if you would like too.
3. Set the callback URL to: `https://vaultls.yoururl.com/api/auth/oidc/callback`.
4. Copy the `Client ID`, and `Client Secret` for use in the next steps.

## VaulTLS Setup

VaulTLS has OIDC support built in, so no additional packages are required. Configure it via environment variables (or the equivalent fields in the web UI):

```sh
VAULTLS_OIDC_AUTH_URL=https://pocketid.yoururl.com
VAULTLS_OIDC_CALLBACK_URL=https://vaultls.yoururl.com/api/auth/oidc/callback
VAULTLS_OIDC_ID=yourclientid
VAULTLS_OIDC_SECRET=yourclientsecret
```

`VAULTLS_OIDC_AUTH_URL` only needs the base URL of your Pocket ID instance — VaulTLS discovers the rest of the endpoints automatically via `/.well-known/openid-configuration`.

Restart the container after setting these variables.

> **Note:** the callback URL VaulTLS sends must match exactly what's registered in Pocket ID, including scheme (`http` vs `https`) and host. If you access VaulTLS through multiple URLs (e.g. a local IP and a domain via reverse proxy), OIDC login will only work through whichever one is registered here.
