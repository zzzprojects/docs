---
Name: Getting Started with Licensing
LastMod: 2023-02-27
---

# Licensing

## Evaluation Period
- You can evaluate the library for several months before purchasing it.
- The latest version always contains a trial that expires at the **end of the month**. 
- You can extend your trial for several months by downloading the latest version at the beginning of every month.

## How can I purchase the library?
- You can purchase the library [here](pricing)
- Upon purchase, you will receive an email with a license name and a license key.
- Make sure to check your **SPAM** folder if you don't receive the license within 24h.

## How can I get a free license for a personal or academic purpose?
We don't offer free licenses.

However, you can benefit from all the prime features for personal or academic projects for free by downloading the trial at the beginning of every month.

## Setup License from config file
The license name and key can be added directly in the app.config or web.config file in the appSettings section.


```csharp
<appSettings>
	<add key="Z_EntityFramework_Extensions_LicenseName" value="[licenseName]"/>
	<add key="Z_EntityFramework_Extensions_LicenseKey" value="[licenseKey]"/>
</appSettings>
```

## Setup License from appsettings.json file (.NET Core)
The license name and key can be added directly in the appsettings.json.

```csharp
{
  "Z.EntityFramework.Extensions": {
    "LicenseName": "[licenseName]",
    "LicenseKey": "[licenseKey]"
  }
}
```

## Setup License from code
The license can be added directly in the code of your application. Make sure to follow recommendations about where to add this code.


```csharp
Z.EntityFramework.Extensions.LicenseManager.AddLicense([licenseName], [licenseKey]);
```

### Recommendations
- **Web App:** Use Application_Start in global.asax to activate your license.
- **WinForm App:** Use the main thread method to activate your license.
- **Win Service:** Use the OnStart method to activate your license

> Add the license before the first call to the library. Otherwise, the library will be enabled using the evaluation period.

## Setup License with Azure User Secrets
The LicenseManager doesn't have access to the `appsettings.json` modified with the user secret but only the original one.

You will have to add it manually with the `AddLicense` method:

```csharp
string licenseName = //... PRO license name
string licenseKey = //... PRO license key

Z.EntityFramework.Extensions.LicenseManager.AddLicense([licenseName], [licenseKey]);
```

Ensure you **DO NOT** store the license name and key under the same section we previously recommended, `Z.EntityFramework.Extensions`. Otherwise, the LicenseManager will also automatically add this invalid license.

## How can I check if my license is valid?
You can use the **ValidateLicense** method to check if the current license is valid or not.


```csharp
// CHECK if the license is valid for the default provider (SQL Server)
string licenseErrorMessage;
if (!Z.EntityFramework.Extensions.LicenseManager.ValidateLicense(out licenseErrorMessage))
{
    throw new Exception(licenseErrorMessage);
}

// CHECK if the license is valid for a specific provider
string licenseErrorMessage;
if (!Z.EntityFramework.Extensions.LicenseManager.ValidateLicense(out licenseErrorMessage, ProviderType.SqlServer))
{
   throw new Exception(licenseErrorMessage);
}
```
