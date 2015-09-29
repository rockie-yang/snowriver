---
ID: 752
post_title: Mac FaceTime没有声音解决办法
author: riv
post_date: 2012-08-26 21:32:51
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2012/08/26/mac-facetime%e6%b2%a1%e6%9c%89%e5%a3%b0%e9%9f%b3%e8%a7%a3%e5%86%b3%e5%8a%9e%e6%b3%95/
published: true
---
<!--:en-->Today when I facetime from Macbook Air to iPad. Always no sounds on ipad.

go to System Preference->Sounds->Input check. it is normal

choose internal mic phone from Facetime->Video

google and find this topic https://discussions.apple.com/thread/3273433?start=0&tstart=0。
Finder > Go Menu > Go to Folder and type ~/Library/Preferences
delete com.apple.facetime.plist and com.apple.facetime.plist.lockfile.
restart Face Time

It works.<!--:--><!--:zh-->今天用Macbook Air给ipad facetime。在ipad是始终没有声音。

到System Preference->Sounds->Input检查正常

在Facetime->Video->Internal micphone

根据这篇https://discussions.apple.com/thread/3273433?start=0&tstart=0。
Finder > Go Menu > Go to Folder and 输入 ~/Library/Preferences
删除com.apple.facetime.plist and com.apple.facetime.plist.lockfile.
重启 Face Time

工作了<!--:-->