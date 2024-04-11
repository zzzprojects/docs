---
Title: Bulk Operations Licensing
MetaDescription: Learn about how to add the license from the code or config file for the Bulk Operations library. Learn how to validate the license was correctly added.
LastMod: 2024-04-11
---

# Licensing

For developers who have just purchased a license, it's important to always use the [ValidateLicense](/licensing#how-can-i-check-if-my-license-is-valid) method to prevent any unexpected issues caused by the [trial expired error](/trial#you-purchased-a-license-but-didnt-add-the-license-correctly) in your production environment.

## How long is the evaluation/trial period?

You can evaluate our library for several months before making a purchase.

The trial period stops at the end of the month. However, you can extend the trial by an additional month by [downloading](/download) the latest version.

## How do I purchase a license?

Once you have completed your evaluation, you can purchase the library [here](/pricing).

A few hours after the purchase, you will receive an email with your **license name** and a **license key**.

## How do I add the license from the appsettings.json file?

You can add the license in an `appsettings.json` file by creating a new section for our library:

```csharp
{
  "Z.BulkOperations": {
    "LicenseName": "[licenseName]",
    "LicenseKey": "[licenseKey]"
  }
}
```

NOTE:

- The name of the JSON file should be exactly `appsettings.json` (Our library is not aware of any other configuration files).
- The JSON file should be in the root of the project, not within a folder (Our library is not aware of any other configuration files in other directories).
- Make sure you still call the [ValidateLicense](/licensing#how-can-i-check-if-my-license-is-valid) method in your code.

## How do I add the license from a config file?

You can add the license directly in an `app.config` or `web.config` file in the `appSettings` section:

```xml
<appSettings>
	<add key="Z_BulkOperations_LicenseName" value="[licenseName]"/>
	<add key="Z_BulkOperations_LicenseKey" value="[licenseKey]"/>
</appSettings>
```

NOTE:
- Ensure you still call the [ValidateLicense](/licensing#how-can-i-check-if-my-license-is-valid) method in your code.

## How do I add the license directly in the code?

The latest way to add the license is by using the `AddLicense` method directly in the code. The license name and license key can be hardcoded, read from a file, key vault, or any other source:

```csharp
string licenseName = _configuration["licenseName"]; // or any other technique you usually use to read values from the appsettings.json
string licenseKey = _configuration["licenseKey"]; // // or any other technique you usually use to read values from the appsettings.json

Z.BulkOperations.LicenseManager.AddLicense(licenseName, licenseKey);

string licenseErrorMessage;
if (!Z.BulkOperations.LicenseManager.ValidateLicense(out licenseErrorMessage))
{
    throw new Exception(licenseErrorMessage);
}

```

NOTE:
- Make sure to add the license **before making the first use of a paid method** to ensure it is applied correctly. Otherwise, an error will be thrown.

## How can I check if my license is valid?

You should always use the `ValidateLicense` method. We highly recommend it, as every time someone reported a [trial expired error](/trial#you-purchased-a-license-but-didnt-add-the-license-correctly) in their production environment, they were not using this method.

The `ValidateLicense` method allows you to validate that the license has been added correctly. If no license has been added or the license is invalid, the method will return `false`, and you can retrieve the reason in the `licenseErrorMessage` variable:

```csharp
// Check if the license is valid for the default provider (SQL Server)
string licenseErrorMessage;
if (!Z.BulkOperations.LicenseManager.ValidateLicense(out licenseErrorMessage))
{
    throw new Exception(licenseErrorMessage);
}

// Check if the license is valid for a specific provider
string licenseErrorMessage;
if (!Z.BulkOperations.LicenseManager.ValidateLicense(out licenseErrorMessage, ProviderType.SqlServer))
{
   throw new Exception(licenseErrorMessage);
}
```

## I have a license but received a trial period expired error

The `ERROR_005: The monthly trial period has expired...` error occurs when the license has not been added correctly to your project.

You can find the most common cause and solution for this issue [here](/trial#you-purchased-a-license-but-didnt-add-the-license-correctly). This error can always be avoided by using the [ValidateLicense](/licensing#how-can-i-check-if-my-license-is-valid) method.

## How can I get a free license for Personal or Academic purposes?

We don't offer free licenses.

However, you can use our library for free in your personal or academic projects by [downloading](/download) the trial at the beginning of every month.

So, technically, you can use it for free for personal or school projects. However, for commercial purposes [purchasing a license](/pricing) is always required.