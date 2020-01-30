![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Get DayLastAccessed Across All Sharepoint Sites With SQL
**Post Date:  May 11, 2018**        



## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process


![Find Last Day Of Access For Sharepoint Sites]( https://mikesdatawork.files.wordpress.com/2018/05/image002.png "Find Last Day Accessed In Sharepoint") 
 

<p>Find yourself trying to manage the growth of a busy Sharepoint environment? This should help.
With this you can find the [DaysLastAccessed] under the [Webs] table for every Sharepoint Content database. </p> 

    
## SQL-Logic
```SQL
-- find the number of days last accessed across ALL content sites
use [master];
set nocount on
 
if object_id('tempdb..#last_access_all') is not null
drop table  #last_access_all
create table    #last_access_all 
(
    [database]  varchar(255)
,   [title]     varchar(1000)
,   [site_url]  varchar(max)
,   [created_on]    datetime
,   [last_accessed] datetime
,   [days_ago]  int
)
 
declare @last_access_all    varchar(max)
set @last_access_all    = ''
select  @last_access_all    = @last_access_all + 
'use [' + [name] + '];' + char(10) + 
'select
    ''database''        = db_name()
,   ''title''       = [title]
,   ''site_url''        = [fullurl]
,   ''created_on''      = left([timecreated], 19)
,   ''last_accessed''   = left(dateadd(d, [daylastaccessed] + 65536, convert(datetime, ''1/1/1899'', 101)), 19)
,   ''days_ago''        = datediff(day, dateadd(d, [daylastaccessed] + 65536, convert(datetime, ''1/1/1899'', 101)), getdate())
from [webs] where ([daylastaccessed] <> 0) and [fullurl] like N''sites/%'' order by   [daylastaccessed] asc
'
from    sys.databases where [name] like '%content%' order by [name] asc
 
insert into #last_access_all exec (@last_access_all)
 
select * from #last_access_all
go
```


Alternatively you can check one database at a time with this..



## SQL-Logic
```SQL
use [my_content_database]
set nocount on
 
-- find number of days since content site was last used
select
    'database'      = db_name()
,   'title'         = [title]
,   'site_url'      = [fullurl]
,   'created_on'    = left([timecreated], 19)
,   'last_accessed' = left(dateadd(d, [daylastaccessed] + 65536, convert(datetime, '1/1/1899', 101)), 19)
,   'days_ago'      = datediff(day, dateadd(d, [daylastaccessed] + 65536, convert(datetime, '1/1/1899', 101)), getdate())
from [webs] where ([daylastaccessed] <> 0) and [fullurl] like N'sites/%' order by [daylastaccessed] asc
go
```

Hope you find it helpful. 


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

   
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

