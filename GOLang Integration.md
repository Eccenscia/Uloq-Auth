# Uloq Auth Integration Guide for Go

[Uloq Auth](https://auth.uloq.io) is a robust OAuth2 and OpenID Connect service based on IdentityServer4. This guide provides code examples and step-by-step instructions to help you integrate Uloq Auth with your Go application for authentication using OAuth2 and OpenID Connect.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Install Required Packages](#step-1-install-required-packages)
- [Step 2: Obtain Access Token (OAuth2)](#step-2-obtain-access-token-oauth2)
- [Step 3: Authenticate with OpenID Connect](#step-3-authenticate-with-openid-connect)
- [Additional Resources](#additional-resources)

## Prerequisites

Before integrating Uloq Auth with your Go application, make sure you have the following:

- Uloq Auth client credentials (client ID and client secret).
- Redirect URI(s) configured in Uloq Auth.
- Basic understanding of OAuth2 and OpenID Connect concepts.

## Step 1: Install Required Packages

To get started, you'll need to install the required packages for your Go application. Here are the recommended packages:

```bash
go get -u golang.org/x/oauth2
go get -u golang.org/x/oauth2/clientcredentials
```

## Step 2: Obtain Access Token (OAuth2)

To obtain an access token using OAuth2, you'll need to make a token request to Uloq Auth using the client credentials flow. Here's an example in Go:

```go
package main

import (
	"context"
	"fmt"
	"golang.org/x/oauth2"
	"golang.org/x/oauth2/clientcredentials"
)

func main() {
	conf := &clientcredentials.Config{
		ClientID:     "YourClientId",
		ClientSecret: "YourClientSecret",
		TokenURL:     "https://auth.uloq.io/token",
		Scopes:       []string{"YourRequestedScopes"},
	}

	client := conf.Client(context.Background())

	// Use the client to make requests
	// ...

	fmt.Println("Access token:", conf.TokenSource(context.Background()).Token())
}
```

Make sure to replace `"YourClientId"`, `"YourClientSecret"`, and `"YourRequestedScopes"` with the actual client credentials and requested scopes provided by Uloq Auth.

## Step 3: Authenticate with OpenID Connect

To authenticate with Uloq Auth using OpenID Connect, you'll need to initiate the authentication flow and handle the authorization response. Here's an example in Go:

```go
package main

import (
	"fmt"
	"golang.org/x/oauth2"
	"golang.org/x/oauth2/clientcredentials"
	"net/http"
)

var (
	oauth2Config *oauth2.Config
)

func main() {
	oauth2Config = &oauth2.Config{
		ClientID:     "YourClientId",
		ClientSecret: "YourClientSecret",
		RedirectURL:  "YourRedirectURL",
		Endpoint: oauth2.Endpoint{
			AuthURL:  "https://auth.uloq.io/authorize",
			TokenURL: "https://auth.uloq.io/token",
		},
		Scopes: []string{"openid", "profile", "email"},
	}

	// Redirect the user to the authentication URL
	http.HandleFunc("/", handleMain)
	http.HandleFunc("/callback", handleCallback)
	http.ListenAndServe(":8080", nil)
}

func handleMain(w http.ResponseWriter, r *http.Request) {
	url := oauth2Config.AuthCodeURL("YourStateValue")
	http.Redirect(w, r, url, http.StatusTemporaryRedirect)
}

func handleCallback(w http.ResponseWriter, r *http.Request) {
	code := r.URL.Query().Get("code")
	token, err := oauth2Config.Exchange(oauth2.NoContext, code)
	if err != nil {
		http.Error(w, "Failed to exchange token", http.StatusInternalServerError)
		return
	}

	// Use the token for authentication
	// ...

	fmt.Fprintf(w, "Access token: %s\n", token.AccessToken)
	fmt.Fprintf(w, "ID token: %s\n", token.Extra("id_token"))
}
```

Make sure to replace `"YourClientId"`, `"YourClientSecret"`, `"YourRedirectURL"`, and `"YourStateValue"` with the appropriate values provided by Uloq Auth.

## Additional Resources

- [Uloq Auth Documentation](https://auth.uloq.io/docs)
- [OAuth2 Package for Go](https://pkg.go.dev/golang.org/x/oauth2)

Refer to the Uloq Auth documentation for more details on available endpoints, supported flows, and configuration options. Additionally, consult the OAuth2 Package for Go documentation for further guidance on integrating OAuth2 with Go.

Feel free to customize the code examples and configuration settings based on your specific requirements and Go application setup. Remember to replace the placeholders (`YourClientId`, `YourClientSecret`, `YourRequestedScopes`, `YourRedirectURL`, `YourStateValue`, etc.) with the actual values relevant to your Uloq Auth integration.

Note: The code examples provided above are for illustrative purposes only. It's essential to refer to the Uloq Auth documentation and the OAuth2 Package for Go documentation for accurate and up-to-date integration instructions.
