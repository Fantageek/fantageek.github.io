---
author: onmyway133
comments: true
date: 2013-07-14 13:18:04+00:00
layout: post
slug: wp8-json-net-tutorial
title: WP8 Json.NET tutorial
wordpress_id: 489
categories:
- Windows 8
- Windows Phone
tags:
- .net
- convert
- csharp
- geonames
- json
- json.net
- parse
- pcl
- portable
- windows phone 8
- wp8
---

[Json.NET](http://json.codeplex.com/) maybe the best open source Json library for the .NET world. It is built as a Portable Class Library that is easy to be consumed by Xbox, Windows Store and Windows Phone 8 applications.

**Get Json.NET**
There are different libraries when you download Json.NET
-Net45: .NET latest (4.5)
-Net40: .NET 4.0
-Net35: .NET 3.5
-Net20: .NET 2.0
-WinRT: Windows 8 Store
-Portable: .NET 4.5, Windows Phone 8, Windows 8 Store
-Portable40: .NET 4.0, Windows Phone 7, Windows 8 Store, Silverlight 4

_Portable_ is the folder we need for Windows Phone 8. Add that library as a reference to your WP8 application.

**Sample json data**
We'll use [GeoNames](http://www.geonames.org/) because it contains many good json data. For example, we'll use this link [http://api.geonames.org/postalCodeLookupJSON?postalcode=6600&country;=AT&username;=demo](http://api.geonames.org/postalCodeLookupJSON?postalcode=6600&country=AT&username=demo)
Note: This is the demo link, which will not exist for long. In real application, you need to register for a GeoNames account.

**Generate C# classes according to this json**
For easy, we use an online service to do that. Go to [http://json2csharp.com](http://json2csharp.com) and paste that json. Click generate and you'll receive the C# classes
[code language="csharp"]
public class Postalcode
    {
        public string adminCode3 { get; set; }
        public string adminName2 { get; set; }
        public string adminName3 { get; set; }
        public string adminCode2 { get; set; }
        public string postalcode { get; set; }
        public string adminCode1 { get; set; }
        public string countryCode { get; set; }
        public double lng { get; set; }
        public string placeName { get; set; }
        public double lat { get; set; }
        public string adminName1 { get; set; }
    }

    public class PostalcodeList
    {
        public List<Postalcode> postalcodes { get; set; }
    }
[/code]

**Get that Json online and parse to C# class**
We'll use WebClient to get the Json online and JsonConvert to parse to C# classes
[code language="csharp"]
public static void GetJsonOnline()
        {
            WebClient webClient = new WebClient();
            webClient.DownloadStringCompleted += webClient_DownloadStringCompleted;
            webClient.DownloadStringAsync(new Uri("http://api.geonames.org/postalCodeLookupJSON?postalcode=6600&country=AT&username=demo"));
        }

        static void webClient_DownloadStringCompleted(object sender, DownloadStringCompletedEventArgs e)
        {
            string json = e.Result;
            if (!string.IsNullOrEmpty(json))
            {
                PostalcodeList results = JsonConvert.DeserializeObject<PostalcodeList>(json);
            }
        }
[/code]
