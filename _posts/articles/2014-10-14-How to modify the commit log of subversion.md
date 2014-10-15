---
layout: post
title: "如何在 Subversion 上修改已經 Commit 的 Log"
categories: articles 
tags: [subversion]
comments: true
share: true
---

人有失手，馬有錯蹄，有時候總是手快地把 commit log 打完，按下 ```OK``` 鍵送出後，才發現內容有誤，但是該怎麼修改 log 呢？
<!-- more -->

#### 開啟 [TortoiseSVN](http://tortoisesvn.net/) 選單，選擇 Show log (顯示記錄)

![Open TortoiseSVN](/images/2014-10-14/screenshot_12.png)

#### 指定要異動的 commit ，並按下滑鼠右鍵
![Select a commit](/images/2014-10-14/screenshot_13.png)

#### 選取 Edit log message (編輯記錄訊息)
![Edit log message](/images/2014-10-14/screenshot_14.png)

#### 產生錯誤訊息
![Edit log fail](/images/2014-10-14/screenshot_15.png)

由於 Subversion 預設不允許修改 commit log ，因此必須去打開 Subversion 上的 hook 才能進行修改的動作。

#### 設定 Hook 允許修改 log

登入至 Subversion 伺服器到該 Repository 的目錄下
{% highlight bash %}

  cd hooks
  cp pre-revprop-change.tmpl pre-revprop-change
  chmod +x pre-revprop-change

{% endhighlight %}

無需將 Subversion 重新啟動，重複上述的修改動作即可。