# NodeJs-SDK #

## What is NodeJs-SDK? ##

This SDK provides tools for the integration of the Open Banking authorization flow into your NodeJs server application.

This repository contains two subfolders:
* */src* contains the SDK source code
* */example* contains an example on how to use the SDK

## How to use SDK ##

First read throught the Authorization part of API documentation.

Account-information API documentation: https://<sandbox_portal_host_of_the_bank>/api-documentation/account-info-1.0

Payment-initiation API documentation: https://<sandbox_portal_host_of_the_bank>/api-documentation/payment-init-1.0

### Create an OpenBankingAuth instance ###

**OpenBankingAuth(clientId, privateKey, keyID, redirectUri, tokenEndpointUri, authEndpointUri, scope, issuer, jwksUri) - constructor**

*Required parameters*

* clientId (e.g. myApp@account-info.1.0)
* privateKey (your private key, the public key has to be uploded on the developer portal)
* keyID (the id of the keypair in your keystore, can be any string)
* redirectUri (the OAuth2 callback url of your application)
* tokenEndpointUri (token endpoint uri of OIDC server)
* authEndpointUri (authentication endpoint uri of OIDC server)
* scope (depends on API, read documentation)
* jwksUri (certs endpoint uri of OIDC server)
* issuer = (sandbox endpoint uri of OIDC server);

**Usage**

```javascript
const OpenBankingAuth = require('../src/OpenBankingAuth').OpenBankingAuth;
...
const accountInfoAuth = new OpenBankingAuth(clientId, privateKey, keyID, redirectUri, tokenEndpointUri, authEndpointUri, scope, issuer, jwksUri);
```

### Get an access-token ###

**getAccessToken():string**

**Usage**

```javascript
const accessToken = await accountInfoAuth.getAccessToken();
```

### Generate authorization url ###

**generateAuthorizationUrl(intentId, state, nonce):string**

*Required parameters*

* intentId (identification of previously created intent, e.g. ConsentId)
* state (random string)
* nonce (random string)

**Usage**

```javascript
const authUrl = await accountInfoAuth.generateAuthorizationUrl(intentId, state, nonce);
```

### Exhange authorization code to tokens ###

**exchangeToken(code):object**

*Required parameters*

* code (the authorization code received from the authorization server)

**Usage**

```javascript
const newTokens = await accountInfoAuth.exchangeToken(code);
```

## Extra functionality ##

### Create signature header ###

**createSignatureHeader(body):string**

*Required parameters*

* body (intent, e.g. an account-request)

**Usage**
```javascript
const xJwsSignature = await accountInfoAuth.createSignatureHeader(body);
```

### Check if a token is expired ###

**isTokenExpired(token [, expiredAfterSeconds]):boolean**

*Required parameters*

* token (jwt)

*Optional parameters*

* expiredAfterSeconds (number of seconds * 1000)

**Usage**

```javascript
const isExpired = accountInfoAuth.isTokenExpired(token, 5000); // will token expire after five seconds?
```

### Use a refresh token ###

**refreshToken(refreshToken):object**

*Required parameters*

* refresh token

**Usage**

```javascript
const newTokens = accountInfoAuth.refreshToken(refreshToken);
```

## How to run the example ##

Open *example/app.js* and replace <sandbox_api_host_of_the_bank> with correct value (e.g. api.sandbox.bank.hu).

Run example.

```shell
cd example
npm start
```

Open your browser and navigate to *http://localhost:3000/account-info* or *http://localhost:3000/payment-init*.