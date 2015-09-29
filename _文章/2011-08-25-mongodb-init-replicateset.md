---
ID: 594
post_title: MongoDB-建立复制集ReplicateSet
author: riv
post_date: 2011-08-25 00:45:05
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/08/25/mongodb-init-replicateset/
published: true
---
<img alt="" src="http://regularlyexpressed.com/wp-content/uploads/2011/05/db-replication.gif" title="dbreplicate" class="alignright" width="150" height="150" />虽然MongoDB有相当不错的文档。但是要建立复制集还是要花点时间。所以我就写了一个脚本来建立复制集。复制集建立好了之后可以通过<a href="http://localhost:28018/_replSet">Rest 接口</a>来查看状态。

在建立复制集合的时候有几个疑问。目前没有搞清楚。哪位牛人知道请告知一声。
<ul>
	<li>如何使用localhost来添加节点。在生产环境下使用具体的IP地址非常正常。但是在测试环境下使用localhost就比较方便了。但是当我使用localhost的时候碰到下面问题。Google了一下也没有搜到答案。
<pre>
rs.add("127.0.0.1:27018")
{
	"assertion" : "can't use localhost in repl set member names except when using it for all members",
	"assertionCode" : 13393,
	"errmsg" : "db assertion failure",
	"ok" : 0
}
</pre>用rs.initiate不带参数有时会成功，有时会不成功。原因应该也是在localhost上执行的问题。</li>
	<li>如何同步执行js脚本。MongoDB中一个命令执行直接就返回了，是异步的。象初始化replicateSet这样的操作，需要几秒才能完成。如果用getLastError，那么又需要一个循环。所以我就简单的用了一个sleep。不知道有没有更好的方法来实现。</li>
	<li>有三个节点的ReplicateSet，如果杀掉一个，能够自动将一个副节点变成主节点。但是杀掉两个之后，第三个就没有办法自动变成主节点了。不知道这算不算一个问题</li>	        
</ul>



建立复制集脚本如下。该脚本建立三个等同节点，也就是会有三份拷贝。
<pre class="brush:bash">
#!/bin/bash
export bindir=/opt/mongodb-osx-x86_64-1.8.2/bin
export datadir=/opt/mongodb-osx-x86_64-1.8.2/data

killall mongod

rm -rf "$datadir"/data1 "$datadir"/data2 "$datadir"/data3
mkdir -p "$datadir"/data1 "$datadir"/data2 "$datadir"/data3

echo "*************Create first node of replicate set ******************"
"$bindir"/mongod --dbpath "$datadir"/data1 --port 27018 --replSet testrs --rest &
sleep 3

echo "*************Create second node of replicate set ******************"
"$bindir"/mongod --dbpath "$datadir"/data2 --port 27019 --replSet testrs --rest &
sleep 3

echo "*************Create third node of replicate set ******************"
"$bindir"/mongod --dbpath "$datadir"/data3 --port 27020 --replSet testrs --rest &
sleep 3

echo "*************Initiate replicateSet ******************"
"$bindir"/mongo --port 27018 init.js
 </pre>

init.js脚本如下
<pre class="brush:js">
rs.initiate({
    _id : "testrs",
    members : [
        {_id : 0, host : "192.168.0.100:27018"},
        {_id : 1, host : "192.168.0.100:27019"},
        {_id : 2, host : "192.168.0.100:27020"}
    ]
})
</pre>

如果希望拷贝为两份，那么第三个节点可以作为仲裁器使用。那么可以采用下面脚本。<!--more-->
<pre class="brush:bash">
#!/bin/bash
export bindir=/opt/mongodb-osx-x86_64-1.8.2/bin
export datadir=/opt/mongodb-osx-x86_64-1.8.2/data

killall mongod

rm -rf "$datadir"/data1 "$datadir"/data2 "$datadir"/data3
mkdir -p "$datadir"/data1 "$datadir"/data2 "$datadir"/data3

echo "*************Create first node of replicate set ******************"
"$bindir"/mongod --dbpath "$datadir"/data1 --port 27018 --replSet testrs --rest &
sleep 3

echo "*************Create second node of replicate set ******************"
"$bindir"/mongod --dbpath "$datadir"/data2 --port 27019 --replSet testrs --rest &
sleep 3

# if want limit copy, then the third node can just be an arbiter
# which will consume very limit resource
"$bindir"/mongod --dbpath "$datadir"/data3 --port 27020 --replSet testrs --oplogSize 8 --rest&
sleep 1

echo "*************Add nodes to replicateSet ******************"
"$bindir"/mongo --port 27018 initArbitor.js
</pre>

初始化js脚本为。
<pre class="brush:javascript">
rs.initiate({
    _id : "testrs",
    members : [
        {_id : 0, host : "192.168.0.100:27018"},
        {_id : 1, host : "192.168.0.100:27019"},
        {_id : 2, host : "192.168.0.100:27020",arbiterOnly:true}
    ]
})
</pre>