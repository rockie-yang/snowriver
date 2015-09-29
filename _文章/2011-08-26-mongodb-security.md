---
ID: 605
post_title: MongoDB-安全性
author: riv
post_date: 2011-08-26 01:33:02
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/08/26/mongodb-security/
published: true
---
<img alt="security" src="http://images.mylot.com/userImages/images/postphotos/1127556.jpg" title="security" class="alignright" width="150" height="210" />在企业环境下开发应用，安全性往往是非常看重的。MongoDB的安全性设计理念就是依赖操作系统。这种设计理念可以使系统非常简单。而这种设计理念客户是否赞同就不得而知了。如果客户对此有所担心，如何给客户解释？如何提供更高一个层次的安全性？

关于怎么配置安全验证网上有不少人已经有翻译了。MongoDB本身的<a href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">安全验证</a>文档也写的很详细。本来打算用一个js脚本来设置安全验证。但是发现无法使用。从交互时shell可以使用use admin，use test之类的命令，但是使用js脚本却不行。在js里用下面这样的命令会报没有认证。
<pre class="brush:javascript">
admin=connect("192.168.0.100:27018/admin")
admin.addUser("admin", "admin");
admin.auth("admin", "admin")

test=admin.getDB("test")
test.addUser("test", "test");
</pre>

MongoDB本身提供了验证机制Authentication。假设应用部署在应用服务器A上，MongoDB部署在数据服务器S上。启动MongoDB时可以通过--bind_ip A来绑定只有A能访问。所有最终用户都要通过这个应用A来访问MongoDB。这种安全验证机制只有在两种情景下有用。
<ul>
	<li>如果攻破应用服务器A和数据服务器S的难度不一样。攻破数据服务器要难一些</li>	
        <li>有多个系统用户（是系统用户，不是最终用户）。需要在系统用户之间隔离</li>
</ul>
否则就没有价值配置安全验证。因为MongoDB是否使用验证可以通过命令行来设置。服务器S被攻破，只要再次启动一下MongoDB把安全验证关掉，就可以完全控制了。如此我们只能说操作系统的安全性是牢不可破的。

那么如果客户对此还是不放心怎么办。那么只有从应用层入手了。在驱动层上加密，部分或者所有数据存到MongoDB时加密，读取时再解密。