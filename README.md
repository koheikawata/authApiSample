# Azure Active Directory authentication and authorization for ASP.NET Core 5.0 Web API
## Add NugetPackage
```
Install-Package Microsoft.Identity.Web
```

## Add codes
`Startup.cs`
```
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(this.Configuration.GetSection("AzureAd"));
```
```
app.UseAuthentication();
```

`WeatherForecast.cs`
```
[Authorize]
```

`Properties/launchSettings.json`
```
"sslPort": 44321
```

## App registration in Azure Portal
1. Register app in AAD
2. Set up AAD app
    - Redirect URI: `https://localhost:44321/signin-oidc`
    - Front-channel logout URL: `https://localhost:44321/signout-oidc`
    - Implicit grant and hybrid flows: `ID tokens`
3. Expose an API > Add a scope
    - Scope: `api://{clientId}`
    - Scope name: `access_as_user`
    - Who can consent: `Admins and users`
    - Admin consent display name: `Access WeatherForecast as a user`
    - Admin consent description: `Accesses the WeatherForecast web API as a user`
    - User consent display name: `Access WeatherForecast as a user`
    - User consent description: `Accesses the WeatherForecast web API as a user`
    - State: `Enabled`
4. Manifest
    - `"accessTokenAcceptedVersion": 2`

## Set up AAD information for API codes
`appsettings.json`
```
"AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "",
    "ClientId": "{clientId}",
    "TenantId": "{tenantId}",
    "CallbackPath": "/signin-oidc"
  }
```

## API test with Postman
1. Run the app with Visual Studio
2. Create GET request at Postman
    - Request URI: `https://localhost:44321/WeatherForecast`
    - Get token
        - Type: `OAuth 2.0`
        - Header Prefix: `Bearer`
        - Grant Type: `Authorization Code`
        - Callback URL: `https://localhost:44321/signin-oidc`
        - Auth URL: `https://login.microsoftonline.com/{tenantId}/oauth2/v2.0/authorize`
        - Access Token URL: `https://login.microsoftonline.com/{tenantId}/oauth2/v2.0/token`
        - Client ID: `{clientId}`
        - Client Secret: `{Your client secret of AAD app}`
        - Scope: `api://{clientId}/access_as_user`
    - Add the acquired token to Header
        - KEY: `Authorization`
        - Value: `Bearer {token}`
