---
title: "Monday Item Query in C#"
date: 2023-06-25
---

The following example can be used to retrieve a Monday item via the Monday v2 API. The classes are all put here in one place for easy reading:

```csharp
public static MondayItem GetMondayItemDetails(string mondayApiKey, string itemId)
{
    string mondayItemsQuery =
        $@"{{% raw %}}{{
            ""query"": ""{{items (ids: [{itemId}]) {{name, column_values {{ title, value}} }} }}""
        }}{{% endraw %}}";
    string itemJson = HttpRequestMethods.HttpRequest(
        "POST", Constants.MondayApiUrl, mondayItemsQuery, mondayApiKey);

    MondayQuery query = JsonConvert.DeserializeObject<MondayQuery>(itemJson);
    MondayItem mondayItem = query.data.items.First();
    return mondayItem;
}

public class MondayQuery
{
    public MondayData data { get; set; }
}

public class MondayData
{
    public List<MondayItem> items { get; set; }
}
public class MondayItem
{
    public string name { get; set; }
    public List<MondayColumn> column_values { get; set; }
}

public class MondayColumn
{
    public string id { get; set; }
    public string title { get; set; }
    public string value { get; set; }
}

public static class Constants
{
    public const string MondayApiUrl = "https://api.monday.com/v2/";
}

```

This example is for retrieving 1 specific item when you already have the itemId. In my case, I am processing a Monday webhook with an Azure Function and want to retrieve the item information.

This query can be edited to retrieve multiple items as well.
