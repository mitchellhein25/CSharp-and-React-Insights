---
title: "Middleware in ASP.NET Core with C#"
date: 2023-07-23
---

Middleware plays a crucial role in the request and response processing pipeline. It allows developers to handle and modify HTTP requests and responses as they flow through the application. Middleware sits between the client and the server, intercepting requests and responses, enabling you to perform various tasks such as logging, authentication, exception handling, compression, and more.

Middleware can serve many purposes, as hinted at above. If an action is going to be necessary for every request or response (or a large majority of them), then it may be a situation where a middleware will be the best option.

## Implementation

When it comes to implementing middleware in ASP.NET Core, I have read a couple very useful articles that should get you started in the right direction:

### Official Microsoft Documentation: 
[Write custom ASP.NET Core middleware](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/middleware/write?view=aspnetcore-7.0)

### Another helpful source
[C# Tip: 2 ways to define ASP.NET Core custom Middleware](https://www.code4it.dev/csharptips/custom-middleware/?utm_source=csharpdigest&utm_medium&utm_campaign=1682)
