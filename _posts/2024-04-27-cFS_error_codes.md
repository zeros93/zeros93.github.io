---
layout: post
title: cFS Standard Error Codes
---


# cFS Standard Error Codes

The status code is designed to have the following bit format:

| Sev |Rsrvd| Service|      Mission Defined     |                      Code                     |
|-----|-----|--------|--------------------------|-----------------------------------------------|
|31 30|29 28|27 26 25|24 23 22 21 20 19 18 17 16|15 14 13 12 11 10  9  8  7  6  5  4  3  2  1  0|

Where:

```
Sev -- Severity
    00 = Success
    01 = Informational
    10 = Warning
    11 = Error

Rsrvd -- Reserved

Service -- cFE Service that generated status code
    000 = Not a cFE Service
    001 = Events Services
    010 = Executive Services
    011 = File Services
    100 = OS API Services
    101 = Software Bus Services
    110 = Table Services
    111 = Time Services

 Mission Defined -- Each mission can choose how to categorize their own
 error codes

 Code -- The number that uniquely identifies the status code. A code
 value of 0xFFFF is always defined as a "Not Implemented" error. This
 is useful for identifying features that are not present either because
 of a platform restriction or because it hasn't been implemented for
 the current build.

```

This format allows Application Developers to:

-   Ensure each status code is unique

-   Categorize error codes with sources to simplify identification and translation
