![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 查询最后访问数据库的时间
#### Find When Databases Were Last Accessed
**发布-日期: 2016年9月22日 (评论)**


## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
每隔一段时间你就需要查看上次访问数据库的时间。 如果不直接设置审核解决方案，可以通过查看sys.dm_db_index_usage_stats找出上次访问的时间点。 这里有一些逻辑来获取访问它的记录并将其做成一个整洁的小临时表。

搜索遍及每个数据库，返回以下结果：

- 服务器名称
- 数据库名称
- 上次搜索
- 上次扫描
- 上次查找
- 上次更新



## English
Every now and then you’ll need to see when a database was last accessed. Without setting up an auditing solution directly you can find out when the point in time was that the last access occurred by looking at the sys.dm_db_index_usage_stats. Here’s some logic to take the results of using this and packaging it into a tidy little temp table.

This runs across every database, and reports back the following:

- Server Name
- Database Name
- Last Seek
- Last Scan
- Last Lookup
- Last Update

---
## Logic
```SQL
use master;
 set nocount on
 if object_id('tempdb..#last_accessed_times') is not null
 drop table #last_accessed_times
 
create table #last_accessed_times
 (
 [server_name] varchar(255)
 , [database_name] varchar(255)
 , [last_user_seek] datetime
 , [last_user_scan] datetime
 , [last_user_lookup] datetime
 , [last_user_update] datetime
 )
 
declare @get_last_accessed_time varchar(max)
 set @get_last_accessed_time = ''
 select @get_last_accessed_time = @get_last_accessed_time +
 
'use [' +  sd.name + '];' + char(10) +
 'select ' + char(10) + '
 ''Server Name'' = @@servername ' + char(10) + '
 , ''Database Name'' = upper(db_name(db_id())) ' + char(10) + '
 , ''Last User Seek'' = max(sddius.last_user_seek) ' + char(10) + '
 , ''Last User Scan'' = max(sddius.last_user_scan) ' + char(10) + '
 , ''Last User Lookup'' = max(sddius.last_user_lookup) '+ char(10) + '
 , ''Last User Update'' = max(sddius.last_user_update) '+ char(10) + '
 
from
 sys.dm_db_index_usage_stats sddius
 
where
 sddius.database_id = db_id()' + char(10)
 
from master.sys.databases sd
 where sd.database_id > 4
 order by sd.name asc
 insert into #last_accessed_times
 
exec (@get_last_accessed_time)
 select
 [server_name] = upper([server_name])
 , [database_name] = upper([database_name])
 , [last_user_seek] = left([last_user_seek], 19)
 , [last_user_scan] = left([last_user_scan], 19)
 , [last_user_lookup] = left([last_user_lookup], 19)
 , [last_user_update] = left([last_user_update], 19)
 
from
 #last_accessed_times
 
order by
 [server_name]
 , [database_name] asc
 
drop table #last_accessed_times


```
这是结果的示例。 只需查看Last_User_Seek列即可。 本例中的日期已使用更容易识别的日期进行转换（人类可读）

Here’s a sample of the results. Just look at the Last_User_Seek column. The dates in this case have been converted using a more readily identified date (Human Readable)

![#](images/find-when-sql-databases-were-last-accessed-a.png?raw=true "#")

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

