---
Title: How to Setup and validate the C# Eval Expression License
MetaDescription: Learn about how to add the license from the code or config file in the C# Eval Expression library. Learn how to validate the license was correctly added.
LastMod: 2024-03-22
---

# Licensing

## Is C# Eval Expression free?

You can use the library for free when:

- You only evaluate expressions under 50 characters
- You use [LINQ Dynamic](/linq-dynamic) methods

In all other cases, you have to [purchase](/pricing) a license.

## Evaluation/Trial Period

You can evaluate our library for several months before purchasing it.

The trial period stops at the end of the month. However, you can extend the trial by another month by [downloading](/download) the latest version.

## How do I purchase a License?

Once you have completed your evaluation, you can purchase the library [here](/pricing).

A few hours after the purchase, you will receive an email with your **license name** and a **license key**.

## How can I get a free license for personal or academic purposes?

We don't offer free licenses.

However, you can easily use our library for free within your personal or academic projects by downloading the trial at the beginning of every month.

So technically, you can use it for free forever. But we don't recommend doing this trick for commercial purposes.

## How do I add the license from appsettings.json file?

You can add the license in an `appsettings.json` file by creating a new section for our library:

```csharp
{
  "Z.Expressions.Eval": {
    "LicenseName": "[licenseName]",
    "LicenseKey": "[licenseKey]"
  }
}
```

However, be careful:

- The name of the json file should be exactly `appsettings.json`
- The json file should be in the root of the project, not within a folder

## How do I add the license from a config file?

You can add the license name directly in an `app.config` or `web.config` file in the appSettings section:

```csharp
<appSettings>
	<add key="Z_Expressions_Eval_LicenseName" value="[licenseName]"/>
	<add key="Z_Expressions_Eval_LicenseKey" value="[licenseKey]"/>
</appSettings>
```

## How do I add the license directly in the code?

The latest way to add the license is by adding it directly in the code by using the `AddLicense` method. The license name and license key can be either hardcoded or read from a file/key vault or anywhere else:

```csharp
string licenseName = // hardcoded value or read from a file
string licenseKey = // hardcoded value or read from a file

EvalManager.AddLicense(licenseName, licenseKey);
``` 

## How to validate I added my license correctly?

Use the `ValidateLicense` method to validate that the license has been added correctly. If no license has been added or is invalid, the method will return `false`, and you will know the reason in the `licenseErrorMessage` variable:

```csharp
string licenseErrorMessage;
if (!Z.Expressions.EvalManager.ValidateLicense(out licenseErrorMessage))
{
    throw new Exception(licenseErrorMessage);
}
```

### Why should you always use the ValidateLicense method?

Here is a common mistake reported to us:

An application has been released in a production environment. However, the license was missing in the `appsettings.json` file.

No error currently happens since our library automatically goes into the monthly trial mode when no license is found, which means that the production environment works correctly for several days/weeks.

When the next month begins, a bad surprise will happen because the trial expires and part of the application using our library doesn't work anymore.

Using the `ValidateLicense` method could have easily avoided this mistake as the method would throw an error that no license has been added directly after your application has been released.