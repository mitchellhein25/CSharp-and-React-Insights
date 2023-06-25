---
title: "Processing Monday Webhooks in C#"
date: 2023-06-24
---

In Monday, an integration can be set up to send a webhook when an action occurs. In our example, a webhook is sent when an item is moved into a particular group called GroupTitle.

![image](https://github.com/mitchellhein25/CSharp-and-React-Insights/assets/58825307/3b06cc13-63b6-4ab9-985c-b82a3596ca00)

The webhook is sent with the following json body:

```json
{
  "event" : { 
    "userId": 1212121212,
    "originalTriggerUuid": null,
    "boardId": 1212121212,
    "pulseId": 1212121212,
    "sourceGroupId": "new_group",
    "destGroupId": "new_group1234",
    "destGroup": {
      "id": "new_group1234",
      "title": "GroupTitle",
      "color":"#00c875",
      "is_top_group": false
    },
    "app": "monday",
    "type": "move_pulse_into_group",
    "triggerTime": "2023-06-19T01:54:20.991Z",
    "subscriptionId": 12121212,
    "triggerUuid": "1212121212121212"
  }
}
```

An http endpoint must be set up to consume that webhook. There are several ways to do this, but my current setup is with Azure Functions. Here is an article on how to get started with Azure Functions: [Create your first C# function in Azure using Visual Studio](https://learn.microsoft.com/en-us/azure/azure-functions/functions-create-your-first-function-visual-studio).

Here is my function for consuming the webhook and extracting the pulseId (I want the pulseId so I can query Monday for the item and extract some specific information about the item to perform other operations).

```csharp
 public class WebhookConsumerExample
  {

      [Function("WebhookConsumerExample")]
      public async static Task<HttpResponseData> Run(
          [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = null)]
          HttpRequestData req, FunctionContext executionContext)
      {
          ILogger logger = executionContext.GetLogger("WebhookConsumerExample");
          string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
          string pulseId = MondayService.GetMondayPulseIdFromWebhook(requestBody);
          // Perform other operations with the pulseId
      }
  }

  public class MondayService
    {
        public static string GetMondayPulseIdFromWebhook(string requestBody)
        {
            string result = string.Empty;
            try
            {
                if (!string.IsNullOrEmpty(requestBody))
                {
                    string eventStr = JObject.Parse(requestBody).SelectToken("event").ToString();
                    string pulseId = JObject.Parse(eventStr).SelectToken("pulseId").ToString();
                    if (!string.IsNullOrEmpty(pulseId))
                        result = pulseId;
                }
            }
            catch
            {
                result = null;
            }
            return result;
        }
    }
    
```

In this case, I am going to use the pulseId to query Monday for the full item information, and that is the only information I need from this webhook. That is why I did not create a model to fully destructure this json into an object. Since I am only retrieving the pulseId, I didn't feel the need to build out an object with the other fields.

More articles will follow on the process of querying Monday for the item information and other Monday API work!
