---
title: "Serializing Private Fields With .NET 7"
date: 2023-06-10
---

## Introduction

I read an article today that I found worth sharing and giving a quick recap on: [.NET 7–Serialize private fields and properties](https://bartwullems.blogspot.com/2023/05/net-7serialize-private-fields-and.html?utm_source=csharpdigest&utm_medium&utm_campaign=1654).

## Overview

In this article, Wullems discusses a new feature introduced in .NET 7 that allows for the serialization of private fields and properties.

## Code Example

The main focus of the article is a code snippet that demonstrates how to utilize the new feature in .NET 7 to achieve serialization of private fields and properties. The code snippet showcases a method called `AddInternalPropertiesModifier` that modifies a `JsonTypeInfo` object by adding information about internal properties of an object.

``` csharp
void AddInternalPropertiesModifier(JsonTypeInfo jsonTypeInfo)
{
     if (jsonTypeInfo.Kind != JsonTypeInfoKind.Object)
         return;

     foreach (PropertyInfo property in jsonTypeInfo.Type.GetProperties(BindingFlags.Instance | BindingFlags.NonPublic))
    {
        JsonPropertyInfo jsonPropertyInfo = jsonTypeInfo.CreateJsonPropertyInfo(property.PropertyType, property.Name);
        jsonPropertyInfo.Get = property.GetValue;
        jsonPropertyInfo.Set = property.SetValue;

        jsonTypeInfo.Properties.Add(jsonPropertyInfo);
    }
}
```

## Explanation

- The method iterates through all the non-public properties of jsonTypeInfo using reflection. 

- For each property, a JsonPropertyInfo object is created. This object provides JSON serialization-related metadata about a property or field.

- The Get property of the JsonPropertyInfo object is assigned the GetValue method of the property obtained from reflection. This means that when the JSON serializer needs to retrieve the value of this property during serialization, it will use the GetValue method to obtain the value.

- The Set property is set in a similar fashion.

- Finally, the created JsonPropertyInfo object is added to the Properties collection of the jsonTypeInfo object.

## Conclusion

By utilizing this approach, objects with private fields and properties can now be serialized and deserialized without having to expose them publicly.

These were the main points I wanted to recap from the article [.NET 7–Serialize private fields and properties](https://bartwullems.blogspot.com/2023/05/net-7serialize-private-fields-and.html?utm_source=csharpdigest&utm_medium&utm_campaign=1654).
