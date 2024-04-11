---
Name: Bulk Operations Trial Troubleshooting
LastMod: 2024-04-11
---

# Trial Period Expired

Every month, we offer a new trial that expires at the end of this month or the next.

However, when the trial period is expired, the following exception is thrown:

> ERROR_005: The monthly trial period is expired (date: [date], version = [version]). You can extend your trial by downloading the latest version at the beginning of every month. More information can be found here: https://bulk-operations.net/trial

There are 2 reasons why this exception can be thrown:

1. You have no license and the trial period expired.
2. You purchased a license but didn't [add the license](/licensing) correctly.

## 1. You have no license and the trial period expired.

In this case, you can continue to evaluate the library for several months by downloading the latest version at the beginning of every month.

<a class="btn btn-lg btn-z" role="button" href="/download" onclick="ga('send', 'event', { eventAction: 'download'});">
	<i class="fa fa-cloud-download" aria-hidden="true"></i>
	Download
	<i class="fa fa-angle-right"></i>
</a>

All you have to do is upgrade to the latest version, rebuild your project, and you are ready to go for another month.

While this solution is perfect for a development environment, you should **never use a trial in a production environment**. We sometimes release the new trial only a few days before the end of the month or even a few days after the trial has expired. So, using a **trial in a production environment** is very dangerous as it can put your application down for a few days until the new trial is released.

For a production environment, you should **always** purchase our [perpetual license](/pricing).

## 2. You purchased a license but didn't add the license correctly.

Almost every time this issue is reported, it is caused by letting our library automatically find the license from the `appsettings.json` file instead of explicitly using the [AddLicense](/licensing#how-do-i-add-the-license-directly-in-the-code) method.

In addition, this error could have been avoided every time by using the [ValidateLicense](/licensing#how-can-i-check-if-my-license-is-valid) to ensure your license was valid. So, as soon as the `ValidateLicense` method would have been called, you would have known that there was an issue with the license instead of getting this error at the end of the month when the trial expires.

Here are common causes and solutions:
- The license is missing from the appsettings.json for the production environment
- The license is in another file than the appsettings.json
- The appsettings.json is not in the project root 
- The current directory doesn't contain the appsettings.json file
- The license is in a Key Vault

### The license is missing from the appsettings.json for the production environment

The error can happen when your development and production configurations are different.

Everything works great in your development environment as it contains the license name and the license key. However, your production environment doesn't have those values, so it goes into trial mode and raises the trial exception at the end of the month.

The solution to fix this issue is easy: just ensure that your production environment also contains the license name and the license key.

This error could also be avoided if the [ValidateLicense](/licensing#how-can-i-check-if-my-license-is-valid) method had been added to the project, as we recommended.


### The license is in another file than the appsettings.json

It's possible to split your configuration into multiple different configuration files. However, our library can only read the `appsettings.json` file at the project root.

So the easiest solution is to make sure the `appsettings.json` at the project root is the file that contains our license name and the license key.

An alternative solution is reading the values from the configuration on your side and explicitly calling the [AddLicense](/licensing#how-do-i-add-the-license-directly-in-the-code) method:

```csharp
string licenseName = _configuration["licenseName"]; // or any other technique you usually use to read values from the appsettings.json
string licenseKey = _configuration["licenseKey"]; // or any other technique you usually use to read values from the appsettings.json

Z.BulkOperations.LicenseManager.AddLicense(licenseName, licenseKey);

string licenseErrorMessage;
if (!Z.BulkOperations.LicenseManager.ValidateLicense(out licenseErrorMessage))
{
    throw new Exception(licenseErrorMessage);
}
```

### The appsettings.json is not in the project root

Our library can only read the `appsettings.json` file located in the project root. It is not aware of any file located in another directory, such as '/config/appsettings.json'.

So, the solution is the same as the issue described in the section "The license is in another file than the appsettings.json".

### The current directory doesn't contain the appsettings.json file

This error is more tricky and more common for `Web API` / `Windows Service` projects. The appsettings.json exists in the same directory as the executable, but the service runs in another directory. So our library doesn't find the `appsettings.json` file.

In this case, you can either explicitly call the [AddLicense](/licensing#how-do-i-add-the-license-directly-in-the-code) method.

Or alternatively, change the current directory before validating the license:

```csharp
Directory.SetCurrentDirectory(AppDomain.CurrentDomain.BaseDirectory);

string licenseErrorMessage;
if (!Z.BulkOperations.LicenseManager.ValidateLicense(out licenseErrorMessage))
{
  throw new Exception(licenseErrorMessage);
}
// ...code...
```

### The license is in a Key Vault

This error is common for people storing sensitive values in an Azure Key Vault or anything similar.

Our library can only read the original value of the `appsettings.json`, so our library is not aware of any value the Key Vault replaces at runtime.

In this case, you should read the values directly from your Key Vault or configuration file and use the [AddLicense](/licensing#how-do-i-add-the-license-directly-in-the-code) method:

```csharp
string licenseName = _configuration["licenseName"]; // or any other technique you usually use to read values from the Key Vault or appsettings.json
string licenseKey = _configuration["licenseKey"]; // or any other technique you usually use to read values from the Key Vault or appsettings.json

Z.BulkOperations.LicenseManager.AddLicense(licenseName, licenseKey);

string licenseErrorMessage;
if (!Z.BulkOperations.LicenseManager.ValidateLicense(out licenseErrorMessage))
{
    throw new Exception(licenseErrorMessage);
}
```