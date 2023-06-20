---
title: "HttpRequestData in Azure Functions"
date: 2023-06-19
---

## Intro 

Recently I have been working on building some Webhooks servers for the purpose of building integrations with Monday.com. Monday.com will send out webhooks when certain actions are performed, and I want to stand up some processes that will consume those webhooks and kick off some other automations.

## HttpRequestData

I decided to set up these Webhook servers with Azure Functions. I set them up using the .NET 6 Isolated Worker Process, as described by the [Microsoft documentation](https://learn.microsoft.com/en-us/azure/azure-functions/functions-create-your-first-function-visual-studio). I discovered and was a bit thrown off by the usage of HttpRequestData as opposed to HttpRequest in the Function signature:

```csharp
[Function("HttpExample")]
public static HttpResponseData Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequestData req,
    FunctionContext executionContext)
```

It turns out that the latest Microsoft.Azure.Functions.Worker package utilizes the HttpRequestData and HttpResponseData classes rather than the conventional HttpRequest and HttpResponse classes. The isolated Azure functions will only work with the newer "Data" version of the classes.

This new class provides a built in method to create and return an HttpResponseData object:

```csharp
response = req.CreateResponse(HttpStatusCode.OK);
```

## Conclusion

This isn't a huge change, but definitely something to be aware of! It can be a bit confusing at first, but just knowing the difference is a great start to developing these Azure Functions with the isolated worker processes.



