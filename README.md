# Azure Active Directory authentication and authorization for ASP.NET Core 6.0 Web API
## Add NugetPackage
```
Install-Package Microsoft.Identity.Web
```

## Add codes
`Program.cs`
```
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(builder.Configuration.GetSection("AzureAD"));
```
```
app.UseAuthentication();
```

`WeatherForecastController.cs`
```
[HttpGet]
[Authorize]
public IEnumerable<WeatherForecast> Get()
{
  ...
}

[HttpGet("reader")]
[Authorize(Roles = "Reader")]
public IEnumerable<WeatherForecast> GetByReader()
{
  ...
}
```

`launchSettings.json`
```
  "iisSettings": {
    "iisExpress": {
      "sslPort": 44321
    }
  },
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
    - Who can consent: `Admins only`
    - State: `Enabled`

## Set up AAD information for API codes
`appsettings.json`
```
"AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{clientId}",
    "TenantId": "{tenantId}",
    "CallbackPath": "/signin-oidc"
  }
```
