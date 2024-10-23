---
title: "Monday Webhooks Endpoint With ASP.NET Core Web API - Hosted on IIS"
date: 2024-10-23
---

In this post, we'll walk through how to set up an ASP.NET Core Web API to handle Monday.com webhooks and verify the webhook challenge. We'll also cover how to host the API on IIS, so it can be publicly accessible. This guide is particularly useful if you're looking to integrate Monday.com automations into your workflow using ASP.NET Core.

## Step 1: Create your ASP.NET Core Web API 

First, you'll need to create an ASP.NET Core Web API project. Follow [this guide](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-8.0&tabs=visual-studio) from Microsoft to quickly scaffold your Web API project.

## Step 2: Write the initial controller to handle the Monday challenge

Monday.com sends a challenge when setting up a webhook, which your API must verify to complete the setup. To learn more about how Monday.com webhooks work, check out [this documentation](https://support.monday.com/hc/en-us/articles/360003540679-Webhook-integration).

Here’s an example of a simple controller that will handle the webhook verification:

```csharp
using Microsoft.AspNetCore.Mvc;
using Newtonsoft.Json.Linq;
using System.Text.Json.Nodes;

namespace MondayAutomationsServer.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class MondayController : ControllerBase
    {
        [HttpPost()]
        public async Task<IActionResult> Post([FromBody] JsonObject request)
        {
            string jsonString = request.ToJsonString();
            JObject jObjectRequest = JObject.Parse(jsonString);
            if (jObjectRequest == null)
                return BadRequest("Request cannot be null.");

            if (request.ContainsKey("challenge"))
                return Ok(request);

            // Do other stuff you want to do with the webhook

            return Ok();
        }
    }
}
```

This controller checks for the "challenge" field in the request and returns it as required for Monday.com webhook verification. After that check for the challenge (which only has to be verified once in Monday when setting up the automation) you can include the customized code that you want to perform in your API.

## Step 3: Publish the API

Once your Web API is set up and tested, it’s time to publish it. Here’s how:

1. In Visual Studio, go to Build -> Publish {PROJECT_NAME}.
2. Choose Folder as the target and publish the project to a local directory.
   
This will generate the necessary files for deploying your API on IIS.
![image](https://github.com/user-attachments/assets/2cd25ffe-2a3b-4aa5-9aa3-7909e02eb7fb)

## Step 4: Host the API on IIS

IIS is one of the most common ways to host a .NET Core API on a Windows server. If you already have a publicly accessible server, hosting on IIS is straightforward.

Here’s how to deploy your API on IIS:

1. Copy the folder created during the publish step (Step 3) to C:\inetpub\wwwroot.
2. Open Internet Information Services (IIS) Manager.
3. Expand Sites -> Default Web Site.
4. Right-click on your API folder and choose Convert to Application.
   
You should now be able to browse your API endpoint and complete the webhook verification with Monday.com.

## Enabling IIS on Windows

To enable Internet Information Services (IIS) on Windows, you can do the following:
- Open Control Panel
- Click Programs and Features
- Select Turn Windows features on or off
- Check the Internet Information Services checkbox
- Click OK

With these steps, you'll have a fully functional API capable of receiving Monday.com webhooks, hosted on IIS for production use. Whether you're processing automations or handling data from Monday.com, this setup provides a solid foundation for your integration.
