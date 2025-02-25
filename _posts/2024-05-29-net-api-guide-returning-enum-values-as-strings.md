---
layout: post
title: ".NET API Guide: Returning Enum Values as Strings"
tags: csharp swagger dotnet guide
category: csharp
---

I was recently asked to return an enum as a string instead of an int from an API call. At first, I considered how to override the framework's behavior, but after some research, I found the solution was easier than I expected.
The easiest way to resolve this is by adding a JsonStringEnumConverter() in the .AddJsonOptions() method of the Startup class:

{% highlight csharp linenos %}
.AddNewtonsoftJson(options =>
     {
         options.SerializerSettings.Converters = new List<Newtonsoft.Json.JsonConverter> { new StringEnumConverter() };
     })
     .AddJsonOptions(options =>
     {
         options.JsonSerializerOptions.Converters.Add(new JsonStringEnumConverter());
     });
{% endhighlight %}

The first part adds a StringEnumConverter to the SerializerSettings.Converters list for Newtonsoft.Json. The second part adds a JsonStringEnumConverter to the JsonSerializerOptions.Converters list for the built-in JSON serializer. This ensures that enums are serialized as strings instead of integers.
