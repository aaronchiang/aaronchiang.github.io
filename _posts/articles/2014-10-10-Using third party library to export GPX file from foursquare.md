---
layout: post
title: "使用第三方函式庫匯出 Forsquare Check-in 資料"
description: "利用 SharpSquare 匯出 GPX 格式"
tags: [c# forsquare]
comments: true
share: true
image:
  feature: 2014-10-10/foursquare-check-in-800.jpg
  creditlink: http://www.dontfeartheforward.com
---

### 申請 Token

連結到 [https://developer.foursquare.com/docs/explore](https://developer.foursquare.com/docs/explore) 登入 Fouraquare 帳號後，按下 ```Allow``` 按鈕之後就會顯示下列畫面，將紅線圈顯處的值複製下來備用。

![Foursuqare Token](/images/2014-10-10/foursquare_token.png)

### 下載 SharpSquare 程式碼

開啟 [SharpSquare](https://github.com/TICLAB/SharpSquare) 並下載原始檔案。

由於函式庫的建構子並不支援僅丟入上述步驟的 Token，因此開啟 ```SharpSquare.cs``` ，將下述增加的建構子部分複製貼上後重新編譯即可。

{% highlight csharp %}
//增加的建構子
public SharpSquare(string accessToken)
{
    this.accessToken = accessToken;
}

{% endhighlight %}

### 操作 [LINQPad](http://www.linqpad.net/) 匯出 GPX 格式

1. 開啟 **LINQPad**

2. 按下 ```F4``` 加入參考編譯好的 ```SharpSquare``` 函式庫
![LINQPad Add References](/images/2014-10-10/linqpad_setting1.png)

3. 輸入紅線圈顯處的值
![LINQPad Add Namespace](/images/2014-10-10/linqpad_setting2.png)

4. 貼上下列程式碼，並替換掉所申請的 **Token** ，按下 ```F5``` 執行，則下方結果區會出現你在 [Foursquare](http://foursquare.com) 所有的 **Check-in** 記錄。

{% highlight csharp %}
void Main()
{
  var square = new SharpSquare("YOUR TOKEN");
  
  var tmp = "";
  var checkinData = "";
  var counter = 0;
  do {
    var parameters = new Dictionary<string, string>();
    parameters.Add("limit", "250");
    parameters.Add("offset", counter.ToString());
    var items = square.GetUserCheckins("self", parameters);
  
    var sb = new StringBuilder();
    foreach(var item in items)
    {
      sb.Append("\t<trk>\n");
      sb.AppendFormat("\t\t<name>{0}</name>\n", item.venue.name);
      sb.Append("\t\t<trkseg>\n");
      sb.AppendFormat("\t\t\t<trkpt lat=\"{0}\" lon=\"{1}\">\n", item.venue.location.lat, item.venue.location.lng);
      sb.AppendFormat("\t\t\t<time>{0}</time>\n", item.createdAt);
      sb.Append("\t\t</trkpt>\n");
      sb.Append("\t\t</trkseg>\n");
      sb.Append("\t</trk>\n");
    }
    tmp = sb.ToString();
    checkinData += tmp;
    counter += 250;
  } while(!string.IsNullOrEmpty(tmp)); 
  
  var result = "<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"no\" ?>\n";
  result += "<gpx xmlns=\"http://www.topografix.com/GPX/1/1\" xmlns:gpxx=\"http://www.garmin.com/xmlschemas/GpxExtensions/v3\" xmlns:gpxtpx=\"http://www.garmin.com/xmlschemas/TrackPointExtension/v1\" creator=\"Oregon 400t\" version=\"1.1\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" xsi:schemaLocation=\"http://www.topografix.com/GPX/1/1 http://www.topografix.com/GPX/1/1/gpx.xsd http://www.garmin.com/xmlschemas/GpxExtensions/v3 http://www.garmin.com/xmlschemas/GpxExtensionsv3.xsd http://www.garmin.com/xmlschemas/TrackPointExtension/v1 http://www.garmin.com/xmlschemas/TrackPointExtensionv1.xsd\">\n";
    result += "\t<metadata>\n";
    result += "\t\t<link href=\"http://www.foursquare.com\">\n";
    result += "\t\t\t<text>Foursquare Checkins</text>\n";
    result += "\t\t</link>\n";
    result += "\t\t<time>" + DateTime.Now.ToString("s") + "</time>\n";
    result += "\t</metadata>\n";
  result += checkinData;
  result += "</gpx>\n";
  result.Dump();
}
{% endhighlight %}