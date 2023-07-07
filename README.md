
# Uloq Auth Integration Guide

[Uloq Auth](https://auth.uloq.io) is a robust OAuth2 and OpenID Connect service based on IdentityServer4. This guide provides code examples and step-by-step instructions to help you integrate your application with Uloq Auth.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Install Required Packages](#step-1-install-required-packages)
- [Step 2: Obtain Access Token (OAuth2)](#step-2-obtain-access-token-oauth2)
    - [.NET/C#](#netc)
    - [Node.js/JavaScript](#nodejsjavascript)
- [Step 3: Authenticate with OpenID Connect](#step-3-authenticate-with-openid-connect)
    - [.NET/C#](#netc-1)
    - [Node.js/JavaScript](#nodejsjavascript-1)
- [Additional Resources](#additional-resources)

## Prerequisites

Before integrating with Uloq Auth, make sure you have the following:

- Uloq Auth client credentials (client ID and client secret).
- Redirect URI(s) configured in Uloq Auth.
- Basic understanding of OAuth2 and OpenID Connect concepts.

## Step 1: Install Required Packages

To get started, you'll need to install the required packages for your application. Here are the recommended packages for different platforms:

### .NET/C#

If you're using .NET/C#, you can use the `IdentityModel` package to simplify the integration process. Install it using NuGet:

```bash
dotnet add package IdentityModel
```

### Node.js/JavaScript

For Node.js or JavaScript-based applications, you can use the `openid-client` package, which provides a powerful OpenID Connect client library. Install it using npm:

```bash
npm install openid-client
```

## Step 2: Obtain Access Token (OAuth2)

To obtain an access token using OAuth2, you'll need to make a token request to Uloq Auth using the client credentials flow. Here's an example in different programming languages:

### .NET/C#

```csharp
using IdentityModel.Client;
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class Program
{
    public static async Task Main()
    {
        var client = new HttpClient();
        var disco = await client.GetDiscoveryDocumentAsync("https://auth.uloq.io");
        if (disco.IsError)
        {
            Console.WriteLine(disco.Error);
            return;
        }

        var tokenResponse = await client.RequestClientCredentialsTokenAsync(new ClientCredentialsTokenRequest
        {
            Address = disco.TokenEndpoint,
            ClientId = "YourClientId",
            ClientSecret = "YourClientSecret",
            Scope = "YourRequestedScopes"
        });

        if (tokenResponse.IsError)
        {
            Console.WriteLine(tokenResponse.Error);
            return;
        }

        var accessToken = tokenResponse.AccessToken;
        Console.WriteLine(accessToken);
    }
}
```

### Node.js/JavaScript

```javascript
const { Issuer, Client } = require('openid-client');

const issuerUrl = 'https://auth.uloq.io';
const clientId = 'YourClientId';
const clientSecret = 'YourClientSecret';
const requestedScopes = 'YourRequestedScopes';

Issuer.discover(issuerUrl)
  .then((issuer) => {
    const client = new issuer.Client({ client_id: clientId, client_secret: clientSecret });

    client.grant({
      grant_type: 'client_credentials',
      scope: requestedScopes
    })
      .then((tokenSet) => {
        const accessToken = tokenSet.access_token;
        console.log(accessToken);
      })
      .catch((error) => {
        console.error(error);
      });
  })
  .catch((error) => {
    console.error(error);
  });
```

## Step 3: Authenticate with OpenID Connect

To authenticate with Uloq Auth using OpenID Connect, you'll need to initiate the authentication flow and handle the authorization response. Here's an example in different programming languages:

### .NET/C#

```csharp
using IdentityModel.Client;
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class Program
{
    public static async Task Main()
    {
        var client = new HttpClient();
        var disco = await client.GetDiscoveryDocumentAsync("https://auth.uloq.io");
        if (disco.IsError)
        {
            Console.WriteLine(disco.Error);
            return;
        }

        var authRequest = new AuthorizationRequest
        {
            Address = disco.AuthorizeEndpoint,
            ClientId = "YourClientId",
            RedirectUri = "YourRedirectUri",
            Scope = "openid profile email", // Requested scopes
            ResponseType = OidcConstants.ResponseTypes.Code,
            // Additional parameters if needed
        };

        var authUrl = authRequest.CreateAuthorizeUrl();

        // Redirect the user to `authUrl` to start the authentication flow

        // Handle the authorization response after the user grants permission
        var authResponse = new AuthorizationResponse
        {
            // Parse the response from the redirect URI
            // and extract the authorization code
        };

        var tokenResponse = await client.RequestAuthorizationCodeTokenAsync(new AuthorizationCodeTokenRequest
        {
            Address = disco.TokenEndpoint,
            ClientId = "YourClientId",
            ClientSecret = "YourClientSecret",
            Code = authResponse.Code,
            RedirectUri = "YourRedirectUri"
        });

        if (tokenResponse.IsError)
        {
            Console.WriteLine(tokenResponse.Error);
            return;
        }

        var accessToken = tokenResponse.AccessToken;
        var idToken = tokenResponse.IdentityToken;
        Console.WriteLine(accessToken);
        Console.WriteLine(idToken);
    }
}
```

### Node.js/JavaScript

```javascript
const { Issuer, generators } = require('openid-client');

const issuerUrl = 'https://auth.uloq.io';
const clientId = 'YourClientId';
const clientSecret = 'YourClientSecret';
const redirectUri = 'YourRedirectUri';

Issuer.discover(issuerUrl)
  .then((issuer) => {
    const client = new issuer.Client({ client_id: clientId, client_secret: clientSecret });

    // Generate state and nonce values
    const state = generators.state();
    const nonce = generators.nonce();

    // Generate the authorization request URL
    const authUrl = client.authorizationUrl({
      redirect_uri: redirectUri,
      scope: 'openid profile email', // Requested scopes
      response_type: 'code',
      state,
      nonce,
    });

    // Redirect the user to `authUrl` to start the authentication flow

    // Handle the authorization response after the user grants permission
    const authorizationCode = 'AuthorizationCodeFromRedirectUri';

    client
      .authorizationCallback(redirectUri, { code: authorizationCode, state, nonce })
      .then((tokenSet) => {
        const accessToken = tokenSet.access_token;
        const idToken = tokenSet.id_token;
        console.log(accessToken);
        console.log(idToken);
      })
      .catch((error) => {
        console.error(error);
      });
  })
  .catch((error) => {
    console.error(error);
  });
```

## Additional Resources

- [Uloq Auth Documentation](https://auth.uloq.io/docs)
- [IdentityServer4 Documentation](https://identityserver4.readthedocs.io)

Refer to the Uloq Auth documentation for more details on available endpoints, supported flows, and configuration options.


Feel free to customize the code examples based on your specific requirements and programming language preferences. Remember to replace the placeholders (`YourClientId`, `YourClientSecret`, `YourRequestedScopes`, `YourRedirectUri`, etc.) with the actual values relevant to your Uloq Auth setup.

Note: The code examples provided above are for illustrative purposes only. It's essential to refer to the Uloq Auth documentation for accurate and up-to-date integration instructions.
