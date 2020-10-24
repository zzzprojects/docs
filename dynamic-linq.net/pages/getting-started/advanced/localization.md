---
Permalink: advanced-localization
Name: Localization
---

# Localization

Currently, there are two configuration settings in the ParsingConfig related to localization.

## DateTime parsing

By default, DateTime (like `Fri, 10 May 2019 11:03:17 GMT`) is parsed as local time.

If you want to parse all DateTimes as UTC, see [ParsingConfig : DateTimeIsParsedAsUTC](advanced-configuration#datetimeisparsedasutc).

## Number Parsing

By default, all numbers (float and double) are parsed using InvariantCulture.

If you want to parse a number using a different Culture, see [ParsingConfig : DateTimeIsParsedAsUTC](advanced-configuration#numberparseculture).
