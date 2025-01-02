---
title: "Monday Webhooks - Issues with Security Certificate"
date: 2025-01-02
---

Recently, I encountered an issue while setting up a server to consume Monday.com webhooks. Everything was configured correctly: the server was properly set up, the webhook endpoint was in place, and we followed the official documentation to handle the webhook challenge response [Monday Webhook Integration Guide](https://support.monday.com/hc/en-us/articles/360003540679-Webhook-integration).

## The Error
However, despite following all the steps, we kept encountering the following error:

"Failed to communicate with the URL provided"

![image](https://github.com/user-attachments/assets/bc91c4ce-53e6-4682-8b77-a675ee070c70)

## The Cause
After extensive troubleshooting, we discovered that the root cause was an SSL certificate verification issue. Unfortunately, Monday's documentation does not specify the SSL certificate requirements for webhooks, nor does the error message provide any indication that SSL verification might be the problem.

The lack of clarity on Monday webhook endpoint security certificate requirements makes it challenging to pinpoint SSL as the root cause during troubleshooting. We ended up trying a different certificate, and it then worked properly.

## More Resources
- This issue has been encountered by others, as discussed in this [Monday community thread](https://community.monday.com/t/failed-to-communicate-with-url/19672/6).
- Another thread that also got our team pointed in the right direction that this may be the issue: [Fix SSL certificate failures in Zaps](https://help.zapier.com/hc/en-us/articles/8496231874445-Fix-SSL-certificate-failures-in-Zaps#1-check-if-your-certificate-is-self-signed-0-0)
