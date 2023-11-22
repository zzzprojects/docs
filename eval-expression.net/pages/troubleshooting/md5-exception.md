---
Name: MD5 Exception Troubleshooting
---

# MD5 Exception

**NOTE**: Since 2020, the default generation is FIPS by default. So, this error only happens with old license key.

## Problem

You execute a method from the Eval Expression.NET library, and the following error is thrown:

> This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.

## Solution

### Cause

The default algorithm to validate the license key & name is not supported with FIPS enabled.

### Fix

#### Ask for a compatible key

Contact us and we will send you a new key supporting FIPS: <a href="mailto:info@zzzprojects.com">info@zzzprojects.com</a>

#### Disable FIPS

Article: [Disable FIPS](https://docs.trendmicro.com/all/ent/sc/v3.0/en-US/cmcolh/t_fips.html){:target="_blank"}
