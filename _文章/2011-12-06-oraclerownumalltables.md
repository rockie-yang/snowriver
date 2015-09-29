---
ID: 627
post_title: >
  Oracle查询数据库中所有表的记录数
author: riv
post_date: 2011-12-06 17:59:45
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/12/06/oraclerownumalltables/
published: true
---
本文转自 http://space.itpub.net/17179887/viewspace-628297
首先建立一个计算函数
<pre class="brush:sql">
create or replace function count_rows(table_name in varchar2,
                              owner in varchar2 default null)
return number
authid current_user
IS
   num_rows number;
   stmt varchar2(2000);
begin
   if owner is null then
      stmt := 'select count(*) from "'||table_name||'"';
   else
      stmt := 'select count(*) from "'||owner||'"."'||table_name||'"';
   end if;
   execute immediate stmt into num_rows;
   return num_rows;
end;
</pre>
然后通过计算函数进行统计
<pre class="brush:sql">
select table_name, count_rows(table_name) nrows from user_tables
</pre>