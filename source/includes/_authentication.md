# Authentication

## Description

Before you can get started with sending messages, you need to obtain an access token, that will give access to one of your environments. 
The Notihub API uses API keys to authenticate requests. You can view and manage your API keys in the Notihub Dashboard.

Your API keys carry many privileges, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth. 

All API requests must be made over **HTTPS**. Calls made over plain HTTP will fail. API requests without authentication will also fail.

## Access Keys

To access the Notihub API you will need to have your user account credentials `publicKey` and `secretKey` and also your specific environment credentials `clientId` and `clientSecret`.
With those keys you have to generate a Base64 encoded string `Base64(publicKey:secretKey:clientId:clientSecret)` that is exchangeble for an access token

## Token lifetime

Access tokens are short lived: 6 hours. To refresh authorization on an application access token, your application will simply exchange its client credentials for a new app access token. Any access tokens that have been previously initialized will not be invalidated with the creation of a new one; they will simply expire within an hour of the time of their creation.

## Create Access Token

Your application need to send a POST request with the `Authorization` header with the word `Basic` followed by a space and a base-64 encoded string 
`publicKey:secretKey:clientId:clientSecret`

> Request

```curl
curl "https://api.notihub.io/token/"
  -X POST
  -H "Authorization: Basic Base64(publicKey:secretKey:clientId:clientSecret)" 
```

```java
NotihubClient defaultNotihubClient = NotihubClient.builder()
            .defaultCredentialsProvider()
            .defaultHttpClientFactory()
            .build();

// Exchange the public credentials for an access token 
NotihubAccessToken accessToken = defaultNotihubClient.authenticationService()
            .exchangeCredentials();
```

```js
import Notihub from '@notihub/notihub-nodejs-sdk';

const notihub = new Notihub({
  publicKey: '<your auth public key>',
  secretKey: '<your auth secret key>',
  environmentClientId: '<your auth environment client id>',
  environmentClientSecret: '<your auth environment client secret>'
});
```

> 200 - Successful Response:

```json
{
  "accessToken": "cbb0aa4e5e4939d9686ae380a3b14e94eb35513df24237c3506f9b21d50e9f55",
  "expiresAt": 1570407929672
}
```

> 401 - Invalid Credentials Response:

```json
{
  "code": 401,
  "message": "Invalid credentals"
}
```


### HTTP Request

`POST https://api.notihub.io/token/`

### Response

Parameter | Type | Description
--------- | ------- | -----------
accessToken | String | The access token to use in secured endpoints that belongs to one environment of your account.
expiresAt | Number | 6 hours ahead from now in milliseconds when the token will expire. You can use it to ask for a new token before this one expires. 

<aside class="success">
Remember — keep your access token secure!
</aside>

## Using the Access Token

Once you obtain your access token, you can use it to access the secured endpoints like customers and messages. Set the `Authorization` header with Bearer token like this:

`Authorization: Bearer <<your-access-token>>`



