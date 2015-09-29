---
ID: 616
post_title: MongoDB-分片
author: riv
post_date: 2011-08-29 23:35:42
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/08/29/mongodb-sharding-2/
published: true
---
<img alt="" src="http://www.mongodb.org/download/attachments/2097393/sharding.PNG?version=2&modificationDate=1267724627656" title="Sharding" class="alignright" width="572" height="333" />关于MongoDB的分片，网上有许多资料。象<a href="http://www.mongodb.org/display/DOCS/Sharding+Introduction">Sharding Introduction</a><a href="http://www.mongodb.org/display/DOCS/Simple+Initial+Sharding+Architecture">Simple Initial Sharding Architecture</a><a href="http://www.mongodb.org/display/DOCS/Sharding+Administration">Sharding Administration</a><a href="http://www.taobaodba.com/html/525_525.html">配置mongodb分片群集(sharding cluster)</a>。但是这些文档对分片数据服务器mongod、配置服务器（config server）和路由服务器mongos的关系，都是一带而过。让我搜了半天才明白。现把我的理解记下来，要不然过几天我又忘了。

配置服务器实际上保持的是一个路由表。而路由服务器自己没有保存任何配置数据。路由服务器将什么数据在哪个分片（数据服务器）的信息保存到配置服务器中。配置服务器和分片数据服务器之间没有任何的物理连接关系。他们之间的逻辑关系也完全通过路由服务器来建立。不需要手工往配置服务器中添加有哪些分片数据服务器。而是再启动路由服务器时指定配置服务器，再往路由服务器服务器添加分片数据服务器。

试着用mongo连接到数据服务器，往里面插几条记录。那么基本上会发现，这些记录会保持到其中一个分片中。那是因为MongoDB会写完一个数据块才会分区。数据块的默认大小为64M，具体在config.settings中保存，可以直接修改这个值来修改数据块大小。所以平常要测试分区效果还是需要比较多的数据的。也不要试着用mongo连接到其中一个分片上，然后插入数据，试图从路由服务器上能够取到。因为在配置服务器里根本就没有这些数据保持在哪个分片的信息。只有通过路由服务器才会将这些信息保持到配置服务器中。

配置服务器只能是奇数个。可以是一个，也可以3个。我的理解就是一个复制集（replicate set）。

下面是用来建立测试环境的系统配置。有三个分片，每个分片两个数据服务器，一个仲裁服务器。3个配置服务器。一个路由服务器。按照时间情况可以将这些服务器配置到几个物理机器上，不需要美个服务器对应一个物理服务器。在脚本中有一个sleep语句。这个sleep配置在我的2010年的MBA上可以成功，如果机器不一样可以使用不一样的sleep值。<!--more-->
<pre class="brush:bash">
#!/bin/bash
export bindir=/opt/mongodb-osx-x86_64-1.8.2/bin
export datadir=/opt/mongodb-osx-x86_64-1.8.2/data
killall mongod
killall mongos
rm -rf "$datadir"/

echo "*************Create config servers******************"
mkdir -p "$datadir"/cfg/data1 "$datadir"/cfg/data2 "$datadir"/cfg/data3 "$datadir"/cfg/log

"$bindir"/mongod --dbpath "$datadir"/cfg/data1 --port 27041 --configsvr --rest --oplogSize 8 > "$datadir"/cfg/log/cfg1.log&
"$bindir"/mongod --dbpath "$datadir"/cfg/data2 --port 27042 --configsvr --rest --oplogSize 8 > "$datadir"/cfg/log/cfg2.log&
"$bindir"/mongod --dbpath "$datadir"/cfg/data3 --port 27043 --configsvr --rest --oplogSize 8 > "$datadir"/cfg/log/cfg3.log&
sleep 3

echo "*************Create replicate set rsa******************"
mkdir -p "$datadir"/rsa/data1 "$datadir"/rsa/data2 "$datadir"/rsa/arbitor "$datadir"/rsa/cfg "$datadir"/rsa/log

"$bindir"/mongod --dbpath "$datadir"/rsa/data1 --port 27011 --shardsvr --replSet rsa --rest > "$datadir"/rsa/log/mongo1.log &
"$bindir"/mongod --dbpath "$datadir"/rsa/data2 --port 27012 --shardsvr --replSet rsa --rest > "$datadir"/rsa/log/mongo2.log &
"$bindir"/mongod --dbpath "$datadir"/rsa/arbitor --port 27013 --shardsvr --replSet rsa --rest  --oplogSize 8 > "$datadir"/rsa/log/arbitor.log &
"$bindir"/mongod --dbpath "$datadir"/rsa/cfg --port 27010 --configsvr --rest --oplogSize 8 > "$datadir"/rsa/log/cfg.log&

echo "*************Create replicate set rsb******************"
mkdir -p "$datadir"/rsb/data1 "$datadir"/rsb/data2 "$datadir"/rsb/arbitor "$datadir"/rsb/log

"$bindir"/mongod --dbpath "$datadir"/rsb/data1 --port 27021 --shardsvr --replSet rsb --rest > "$datadir"/rsb/log/mongo1.log&
"$bindir"/mongod --dbpath "$datadir"/rsb/data2 --port 27022 --shardsvr --replSet rsb --rest > "$datadir"/rsb/log/mongo2.log &
"$bindir"/mongod --dbpath "$datadir"/rsb/arbitor --port 27023 --shardsvr --replSet rsb --rest  --oplogSize 8 > "$datadir"/rsb/log/arbitor.log &

echo "*************Create replicate set rsc******************"
mkdir -p "$datadir"/rsc/data1 "$datadir"/rsc/data2 "$datadir"/rsc/arbitor "$datadir"/rsc/log

"$bindir"/mongod --dbpath "$datadir"/rsc/data1 --port 27031 --shardsvr --replSet rsc --rest > "$datadir"/rsc/log/mongo1.log&
"$bindir"/mongod --dbpath "$datadir"/rsc/data2 --port 27032 --shardsvr --replSet rsc --rest > "$datadir"/rsc/log/mongo2.log &
"$bindir"/mongod --dbpath "$datadir"/rsc/arbitor --port 27033 --shardsvr --replSet rsc --rest  --oplogSize 8 > "$datadir"/rsc/log/arbitor.log &


echo "*************Create Route Server******************"
mkdir -p "$datadir"/route/log
"$bindir"/mongos --configdb 192.168.0.100:27041,192.168.0.100:27042,192.168.0.100:27043 > "$datadir"/route/log/mongos1.log&

echo "*************Wait data & config and route services created ******************"
sleep 15


echo "*************Initiating rsa******************"
"$bindir"/mongo 192.168.0.100:27011/admin  --eval "rs.initiate({_id : \"rsa\",\
    members : [\
        {_id : 0, host : \"192.168.0.100:27011\"},\
        {_id : 1, host : \"192.168.0.100:27012\"},\
        {_id : 2, host : \"192.168.0.100:27013\",arbiterOnly:true}\
    ]\
})"
echo "*************Initiating rsb******************"
"$bindir"/mongo 192.168.0.100:27021/admin --eval "rs.initiate({_id : \"rsb\",\
    members : [\
        {_id : 0, host : \"192.168.0.100:27021\"},\
        {_id : 1, host : \"192.168.0.100:27022\"},\
        {_id : 2, host : \"192.168.0.100:27023\",arbiterOnly:true}\
    ]\
})"
echo "*************Initiating rsc******************"
"$bindir"/mongo  192.168.0.100:27031/admin --eval "rs.initiate({_id : \"rsc\",\
    members : [\
        {_id : 0, host : \"192.168.0.100:27031\"},\
        {_id : 1, host : \"192.168.0.100:27032\"},\
        {_id : 2, host : \"192.168.0.100:27033\",arbiterOnly:true}\
    ]\
})"
echo "*************Waiting rsa,rsb,rsc iniated******************"
sleep 15

echo "*************Add rsa to route******************"
"$bindir"/mongo 192.168.0.100:27017/admin --eval "db.adminCommand({addShard:\"rsa/192.168.0.100:27011,192.168.0.100:27012\", allowLocal : true})"
echo "*************Add rsa to route******************"
"$bindir"/mongo 192.168.0.100:27017/admin --eval "db.adminCommand({addShard:\"rsb/192.168.0.100:27021,192.168.0.100:27022\", allowLocal : true})"
echo "*************Add rsa to route******************"
"$bindir"/mongo 192.168.0.100:27017/admin --eval "db.adminCommand({addShard:\"rsc/192.168.0.100:27031,192.168.0.100:27032\", allowLocal : true})"
"$bindir"/mongo  192.168.0.100:27017/admin --eval "db.adminCommand( { enablesharding : \"test\" } )"
"$bindir"/mongo  192.168.0.100:27017/admin --eval "db.adminCommand( { shardcollection : \"test.name\", key : {name : 1} } )"
</pre>