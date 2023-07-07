# Uloq Auth Integration Guide for .NET 6 Web API Security

[Uloq Auth](https://auth.uloq.io) is a robust OAuth2 and OpenID Connect service based on IdentityServer4. This guide provides code examples and step-by-step instructions to help you integrate Uloq Auth with your .NET 6 Web API for security.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Install Required Packages](#step-1-install-required-packages)
- [Step 2: Configure Authentication](#step-2-configure-authentication)
- [Step 3: Secure API Endpoints](#step-3-secure-api-endpoints)
- [Additional Resources](#additional-resources)

## Prerequisites

Before integrating Uloq Auth with your .NET 6 Web API, make sure you have the following:

- Uloq Auth client credentials (client ID and client secret).
- Redirect URI(s) configured in Uloq Auth.
- Basic understanding of OAuth2 and OpenID Connect concepts.

## Step 1: Install Required Packages

To get started, you'll need to install the required packages for your .NET 6 Web API project. Here are the recommended packages:

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
dotnet add package IdentityModel.AspNetCore
```

## Step 2: Configure Authentication

In your .NET 6 Web API project, configure the authentication middleware to use JWT bearer authentication with Uloq Auth. Here's an example in `Startup.cs`:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // Configure authentication
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Authority = "https://auth.uloq.io";
                options.Audience = "YourApiAudience"; // Replace with your API audience
                options.TokenValidationParameters = new TokenValidationParameters
                {
                    ValidateIssuer = true,
                    ValidIssuer = "https://auth.uloq.io",
                    ValidateIssuerSigningKey = true,
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("YourApiSecretKey")), // Replace with your API secret key
                    ValidateAudience = true,
                    ValidAudience = "YourApiAudience" // Replace with your API audience
                };
            });

        // Other service configurations
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        // Other app configurations

        app.UseAuthentication();
        app.UseAuthorization();
        
        // Other middleware configurations
    }
}
```

Make sure to replace `"YourApiAudience"` with the appropriate audience value for your API, and `"YourApiSecretKey"` with the actual secret key for your API.

## Step 3: Secure API Endpoints

To secure your API endpoints, use the `[Authorize]` attribute on the desired controllers or actions. This will require authentication and authorization for accessing those endpoints. Here's an example:

```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize]
public class YourController : ControllerBase
{
    // Your API endpoints
}
```

Ensure that the `Authorize` attribute is applied to the desired controllers or actions that require authentication and authorization.

## Additional Resources

- [Uloq Auth Documentation](https://auth.uloq.io/docs)
- [Microsoft Authentication Documentation](https://docs.microsoft.com/aspnet/core/security/authentication)

Refer to the Uloq Auth documentation for more details on available endpoints, supported flows, and configuration options. Additionally, consult the Microsoft Authentication documentation for further guidance on ASP.NET Core authentication.


Feel free to customize the code examples and configuration settings based on your specific requirements and API setup. Remember to replace the placeholders (`YourApiAudience`, `YourApiSecretKey`, etc.) with the actual values relevant to your Uloq Auth integration.

Note: The code examples provided above are for illustrative purposes only. It's essential to refer to the Uloq Auth documentation and the Microsoft Authentication documentation for accurate and up-to-date integration instructions.
