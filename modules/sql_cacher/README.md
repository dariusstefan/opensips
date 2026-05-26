---
title: "SQL Cacher Module"
description: "The sql_cacher module introduces the possibility to cache data from a SQL-based database (using different OpenSIPS modules which implement the DB API) into a cache system implemented in OpenSIPS through the CacheDB Interface. This is done by specifying the databases URLs, SQL table to be used,..."
---

## Admin Guide


### Overview {#overview}


The sql_cacher module introduces the possibility to cache data from a
	SQL-based database (using different OpenSIPS modules which implement the DB API)
	into a cache system implemented in OpenSIPS through the CacheDB Interface.
	This is done by specifying the databases URLs, SQL table to be used, desired
	columns to be cached and other details in the OpenSIPS configuration script.


The cached data is available in the script through the read-only pseudovariable
	"$sql_cached_value" similar to a Key-Value system. A specified
	column from the SQL table has the role of "key" therefore the value
	of this column along with the name of a required column are provided as
	"parameters" to the pseudovariable returning the appropriate value of the column.


There are two types of caching available:


- *full caching* - the entire SQL table (all the rows) is loaded
		into the cache at OpenSIPS startup;
- *on demand* - the rows of the SQL table are loaded at runtime
		when appropriate keys are requested.


For on demand caching, the stored values have a configurable expire period after
	which they are permanently removed unless an MI reload function is called for a
	specific key. In the case of full caching the data is automatically reloaded at
	a configurable interval. Consequently if the data in the SQL database changes
	and a MI reload function is  called, the old data remains in cache only
	until it expires.


### Dependencies {#dependencies}


The following modules must be loaded before this module:


- *The OpenSIPS modules that offer actual database back-end
		    	connection*


### Exported Parameters {#exported_parameters}


#### cache_table (string) {#param_cache_table}


This parameter can be set multiple times in order to cache multiple SQL
		tables or even the same table but with a different configuration. The module
		distinguishes those different entries by an "id" string.


The caching entry is specified via this parameter that has it's own
		subparameters. Each of those parameters are separated by a
		delimiter configured by [spec delimiter](#param_spec_delimiter)
		and have the following format:
		*param_name=param_value*
		The parameters are:


- *id* : cache entry id
- *db_url* : the URL of the SQL database
- *cachedb_url* : the URL of the CacheDB database
- *table* : SQL database table name
- *key* : SQL database column name of the "key" column
- *key_type* : data type for the SQL "key" column:
			
				string
				int
			
			If not present, default value is "string"
- *columns* : names of the columns to be cached from the
			SQL database, separated by a delimiter configured by
			[columns delimiter](#param_columns_delimiter).
			If not present, all the columns from the table will be cached
- *on_demand* : specifies the type of caching:
			
				0 : full caching
				1 : on demand
			
			If not present, default value is "0"
- *expire* : expire period for the values stored
			in the cache for the on demand caching type in seconds
			If not present, default value is "1 hour"


The parameters must be given in the exact order specified above.


Overall, the parameter does not have a default value, it must be set
		at least once in order to cache any table.


**Example: cache_table parameter usage**


```opensips
   
modparam("sql_cacher", "cache_table",
"id=caching_name
db_url=mysql://root:opensips@localhost/opensips_2_2
cachedb_url=mongodb:mycluster://127.0.0.1:27017/db.col
table=table_name
key=column_name_0
columns=column_name_1 column_name_2 column_name_3
on_demand=0")
   
```


#### spec_delimiter (string) {#param_spec_delimiter}


The delimiter to be used in the caching entry specification provided in the
		*cache_table* parameter to separate the subparameters. It
		must be a single character.


The default value is newline.


**Example: spec_delimiter parameter usage**


```opensips
   
modparam("sql_cacher", "spec_delimiter", "\n")
   
```


#### pvar_delimiter (string) {#param_pvar_delimiter}


The delimiter to be used in the "$sql_cached_value"
		pseudovariable to separate the caching id, the desired column name
		and the value of the key. It must be a single character.


The default value is ":".


**Example: pvar_delimiter parameter usage**


```opensips
   
modparam("sql_cacher", "pvar_delimiter", " ")
   
```


#### columns_delimiter (string) {#param_columns_delimiter}


The delimiter to be used in the *columns* subparameter of
		the caching entry specification provided in the *cache_table*
		parameter to separate the desired columns names. It must be a single character.


The default value is " "(space).


**Example: columns_delimiter parameter usage**


```opensips
   
modparam("sql_cacher", "columns_delimiter", ",")
   
```


#### sql_fetch_nr_rows (integer) {#param_sql_fetch_nr_rows}


The number of rows to be fetched into OpenSIPS private memory in one chunk from
		the SQL database driver. When querying large tables, adjust this parameter
		accordingly to avoid the filling of OpenSIPS private memory.


The default value is "100".


**Example: sql_fetch_nr_rows parameter usage**


```opensips
   
modparam("sql_cacher", "sql_fetch_nr_rows", 1000)
   
```


#### full_caching_expire (integer) {#param_full_caching_expire}


Expire period for the values stored in cache for the full caching type
		in seconds. This is the longest time that deleted or modified data remains
		in cache.


The default value is "24 hours".


**Example: full_caching_expire parameter usage**


```opensips
   
modparam("sql_cacher", "full_caching_expire", 3600)
   
```


#### reload_interval (integer) {#param_reload_interval}


This parameter represents how many seconds before the data expires (for full caching) the
		automatic reloading is triggered.


The default value is "60 s".


**Example: reload_interval parameter usage**


```opensips
   
modparam("sql_cacher", "reload_interval", 5)
   
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Exported MI Functions {#exported_mi_functions}


#### sql_cacher_reload {#mi_sql_cacher_reload}


Reloads the entire SQL table in cache or the single key (if key provided) in
			*full caching* mode.


Reloads the given key or invalidates all the keys in cache in *on demand* mode.


Parameters:


- *id* - the caching entry's id
- *key* (optional) - the specific key to be reloaded.


**Example: sql_cacher_reload usage**


```
...
$ opensips-cli -x mi sql_cacher_reload subs_caching
...
$ opensips-cli -x mi sql_cacher_reload subs_caching alice@domain.com
...
```


### Exported Pseudo-Variables {#exported_pseudo_variables}


#### $sql_cached_value(id{sep}col{sep}key) {#pv_sql_cached_value}


The cached data is available through this read-only PV.The format
				is the following:


- *sep* : separator configured by
					[pvar delimiter](#param_pvar_delimiter)
- *id*  : cache entry id
- *col* : name of the required column
- *key* : value of the "key" column


**Example: sql_cached_value(id{sep}col{sep}key) pseudo-variable usage**


```
...
$avp(a) = $sql_cached_value(caching_name:column_name_1:key1);
...
				 
```


### Usage Example


This section provides an usage example for the caching of an SQL table.


Suppose one in interested in caching the columns: "host_name", 
	"reply_code", "flags" and "next_domain"
	 from the "carrierfailureroute" table of the OpenSIPS database.


**Example: Example database content - carrierfailureroute table**


```
...
+----+---------+-----------+------------+--------+-----+-------------+
| id | domain  | host_name | reply_code | flags | mask | next_domain |
+----+---------+-----------+------------+-------+------+-------------+
|  1 |      99 |           | 408        |    16 |   16 |             |
|  2 |      99 | gw1       | 404        |     0 |    0 | 100         |
|  3 |      99 | gw2       | 50.        |     0 |    0 | 100         |
|  4 |      99 |           | 404        |  2048 | 2112 | asterisk-1  |
+----+---------+-----------+------------+-------+------+-------------+
...
		
```


In the first place, the details of the caching must be provided by setting 
		the module parameter "cache_table" in the OpenSIPS configuration script.


**Example: Setting the cache_table parameter**


```opensips
modparam("sql_cacher", "cache_table",
"id=carrier_fr_caching
db_url=mysql://root:opensips@localhost/opensips
cachedb_url=mongodb:mycluster://127.0.0.1:27017/my_db.col
table=carrierfailureroute
key=id
columns=host_name reply_code flags next_domain")
		
```


Next, the values of the cached columns ca be accessed through the "$sql_cached_value" PV.


**Example: Accessing cached values**


```opensips
...
$avp(rc1) = $sql_cached_value(carrier_fr_caching:reply_code:1);
$avp(rc2) = $sql_cached_value(carrier_fr_caching:reply_code:2);
...
var(some_id)=4;
$avp(nd) = $sql_cached_value(carrier_fr_caching:next_domain:$var(some_id));
...
xlog("host name is: $sql_cached_value(carrier_fr_caching:host_name:2)");
...
		
```


### Exported Status/Report Identifiers {#sr_identifiers}


The module provides the "sql_cacher" Status/Report group, where each
	full cache is defined as a separate SR identifier. NOTE that there
	are no identifiers created for the on-demand caches.


#### [cache_entry_id] {#sr_id_cache_entry_id}


The status of these identifiers reflects the readiness/status of the 
	cached data (if available or not when being loaded from DB):


- *-2* - no data at all (initial status)
- *-1* - no data, initial loading in progress
- *1* - data loaded, partition ready
- *2* - data available, a reload in progress


In terms of reports/logs, the following events will be reported:


- starting DB data loading
- DB data loading failed, discarding
- DB data loading successfully completed
- N records loaded)


For how to access and use the Status/Report information, please see
	[https://www.opensips.org/Documentation/Interface-StatusReport-3-3](>https://www.opensips.org/Documentation/Interface-StatusReport-3-3).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 93 | 44 | 3640 | 1114 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 22 | 18 | 166 | 115 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 15 | 13 | 25 | 14 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 7 | 5 | 116 | 21 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 3 |
| 6. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | 4 | 1 | 50 | 92 |
| 7. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 3 | 1 | 1 | 1 |
| 8. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 3 | 1 | 1 | 1 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 10. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | 3 | 1 | 1 | 1 |


**All remaining contributors**: Gang Zhuo.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Aug 2015 - Jul 2022 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | May 2017 - Feb 2022 |
| 3. | Gang Zhuo | Nov 2021 - Nov 2021 |
| 4. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2021 - Apr 2021 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Oct 2020 - Jan 2021 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2016 - Jan 2021 |
| 7. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2016 - Sep 2019 |
| 8. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | May 2019 - May 2019 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 10. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Mar 2017 - Mar 2017 |


**All remaining contributors**: Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).


*Documentation Copyrights:*


Copyright © 2015 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
