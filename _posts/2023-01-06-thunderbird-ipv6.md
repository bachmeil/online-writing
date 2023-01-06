---
layout: post
title: Thunderbird and IPv6
---
I've recently (late December 2022/early January 2023) been getting an error message from my Thunderbird client, while attempting to access my employer's Office 365 email account.

> User is authenticated but not connected.

The solution is to disable IPv6. I'm not sure exactly what has changed, but you solve this by opening Settings, then going to > General > Config Editor... button > Search for ipv6 > Change network.dns.disableIPv6 to true.

[Reference](https://answers.microsoft.com/en-us/outlook_com/forum/all/thunderbird-imap-responded-user-is-authenticated/062a82f6-e678-4462-88b7-dd6cc318386f)