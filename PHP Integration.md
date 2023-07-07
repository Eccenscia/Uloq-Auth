# Uloq Auth Integration Guide for PHP

[Uloq Auth](https://auth.uloq.io) is a robust OAuth2 and OpenID Connect service based on IdentityServer4. This guide provides code examples and step-by-step instructions to help you integrate Uloq Auth with your PHP application for authentication using OAuth2 and OpenID Connect.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Install Required Packages](#step-1-install-required-packages)
- [Step 2: Obtain Access Token (OAuth2)](#step-2-obtain-access-token-oauth2)
- [Step 3: Authenticate with OpenID Connect](#step-3-authenticate-with-openid-connect)
- [Additional Resources](#additional-resources)

## Prerequisites

Before integrating Uloq Auth with your PHP application, make sure you have the following:

- Uloq Auth client credentials (client ID and client secret).
- Redirect URI(s) configured in Uloq Auth.
- Basic understanding of OAuth2 and OpenID Connect concepts.

## Step 1: Install Required Packages

To get started, you'll need to install the required packages for your PHP application. Here are the recommended packages:

```bash
composer require league/oauth2-client
```

## Step 2: Obtain Access Token (OAuth2)

To obtain an access token using OAuth2, you'll need to make a token request to Uloq Auth using the client credentials flow. Here's an example in PHP:

```php
use League\OAuth2\Client\Provider\Exception\IdentityProviderException;
use League\OAuth2\Client\Provider\GenericProvider;

$provider = new GenericProvider([
    'clientId' => 'YourClientId',
    'clientSecret' => 'YourClientSecret',
    'urlAuthorize' => 'https://auth.uloq.io/authorize',
    'urlAccessToken' => 'https://auth.uloq.io/token',
    'urlResourceOwnerDetails' => 'https://auth.uloq.io/userinfo'
]);

try {
    $accessToken = $provider->getAccessToken('client_credentials');
    $token = $accessToken->getToken();
    echo $token;
} catch (IdentityProviderException $e) {
    echo $e->getMessage();
}
```

Make sure to replace `'YourClientId'` and `'YourClientSecret'` with the actual client credentials provided by Uloq Auth.

## Step 3: Authenticate with OpenID Connect

To authenticate with Uloq Auth using OpenID Connect, you'll need to initiate the authentication flow and handle the authorization response. Here's an example in PHP:

```php
use League\OAuth2\Client\Provider\Exception\IdentityProviderException;
use League\OAuth2\Client\Provider\GenericProvider;

$provider = new GenericProvider([
    'clientId' => 'YourClientId',
    'clientSecret' => 'YourClientSecret',
    'redirectUri' => 'YourRedirectUri',
    'urlAuthorize' => 'https://auth.uloq.io/authorize',
    'urlAccessToken' => 'https://auth.uloq.io/token',
    'urlResourceOwnerDetails' => 'https://auth.uloq.io/userinfo'
]);

$authorizationUrl = $provider->getAuthorizationUrl([
    'scope' => 'openid profile email',
    'state' => 'YourStateValue'
]);

// Redirect the user to $authorizationUrl

// Handle the authorization response after the user grants permission
if (isset($_GET['code'])) {
    try {
        $accessToken = $provider->getAccessToken('authorization_code', [
            'code' => $_GET['code']
        ]);
        
        $token = $accessToken->getToken();
        $idToken = $accessToken->getIdToken();
        echo $token;
        echo $idToken;
    } catch (IdentityProviderException $e) {
        echo $e->getMessage();
    }
}
```

Make sure to replace `'YourClientId'`, `'YourClientSecret'`, `'YourRedirectUri'`, and `'YourStateValue'` with the appropriate values provided by Uloq Auth.

## Additional Resources

- [Uloq Auth Documentation](https://auth.uloq.io/docs)
- [OAuth2 Client PHP Library](https://oauth2-client.thephpleague.com/)

Refer to the Uloq Auth documentation for more details on available endpoints, supported flows, and configuration options. Additionally, consult the OAuth2 Client PHP Library documentation for further guidance on integrating OAuth2 with PHP.

Feel free to customize the code examples and configuration settings based on your specific requirements and PHP application setup. Remember to replace the placeholders (`YourClientId`, `YourClientSecret`, `YourRedirectUri`, `YourStateValue`, etc.) with the actual values relevant to your Uloq Auth integration.

Note: The code examples provided above are for illustrative purposes only. It's essential to refer to the Uloq Auth documentation and the OAuth2 Client PHP Library documentation for accurate and up-to-date integration instructions.
