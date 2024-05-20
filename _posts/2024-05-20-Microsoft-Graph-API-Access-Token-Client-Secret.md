---
title: "Connecting to Microsoft Graph API with a Client Secret - C#"
date: 2024-05-20
---

# Integrating Microsoft Graph API in .NET: A Quickstart Guide

In this blog post, we’ll walk you through the process of integrating Microsoft Graph API into your .NET application. Microsoft Graph provides a unified programmability model that you can use to access a vast amount of data in Microsoft 365. My use case was push data into Sharepoint.

## Step 1: Register Your App in Microsoft Entra

Before you can start coding, you need to register your application in Microsoft Entra. This step allows your app to authenticate with Microsoft and gain access to Microsoft Graph resources. Follow the [official guide to register your app](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app).

## Step 2: Set Up Your Project

Once your app is registered, you can set up your project to authenticate and interact with Microsoft Graph. Below is sample code to get you started in C#. This code demonstrates how to use the Microsoft Authentication Library (MSAL) to acquire tokens and interact with Microsoft Graph using the GraphServiceClient.

**NuGet Libraries Needed**: Microsoft.Identity.Client, Microsoft.Graph

Sample Code:

```csharp

BaseBearerTokenAuthenticationProvider authenticationProvider = new BaseBearerTokenAuthenticationProvider(new TokenProvider(clientId, clientSecret, tenantId));
GraphServiceClient graphClient = new GraphServiceClient(authenticationProvider);

internal class TokenProvider : IAccessTokenProvider
{
    private string _clientId;
    private string _clientSecret;
    private string _tenantId;

    public TokenProvider(string clientId, string clientSecret, string tenantId) 
    {
        _clientId = clientId;
        _clientSecret = clientSecret;
        _tenantId = tenantId;
    }

    public Task<string> GetAuthorizationTokenAsync(Uri uri, Dictionary<string, object> additionalAuthenticationContext = default,
        CancellationToken cancellationToken = default)
    {
        var app = ConfidentialClientApplicationBuilder.Create(_clientId)
            .WithClientSecret(_clientSecret)
            .WithAuthority(new Uri($"https://login.microsoftonline.com/{_tenantId}"))
            .Build();

        string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

        var result = app.AcquireTokenForClient(scopes).ExecuteAsync().Result;

        return Task.FromResult(result.AccessToken);
    }

    public AllowedHostsValidator AllowedHostsValidator { get; }
}
```

## Explanation:

**TokenProvider Class**: This class handles the token acquisition using the MSAL library. It takes in the client ID, client secret, and tenant ID of your registered app.

**Constructor**: Initializes the TokenProvider with the necessary credentials.

**GetAuthorizationTokenAsync**: Acquires an access token from Microsoft Entra using the client credentials and the scope "https://graph.microsoft.com/.default".

**BaseBearerTokenAuthenticationProvider**: This provider uses the TokenProvider to get the authorization token, which is then used to authenticate requests to Microsoft Graph.

**GraphServiceClient**: This is the main client provided by the Microsoft Graph SDK to interact with the Microsoft Graph API. It uses the authentication provider to ensure that all requests are authenticated.

## Conclusion

With your app registered and your project set up with the above code, you can now start interacting with Microsoft Graph. The GraphServiceClient allows you to perform various operations like reading user profiles, sending emails, uploading files to Sharepoint, and more.
