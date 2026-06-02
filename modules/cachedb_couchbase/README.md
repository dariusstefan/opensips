---
title: "cachedb_couchbase Module"
description: "This module is an implementation of a cache system designed to work with a Couchbase server. It uses the libcouchbase client library to connect to the server instance, It uses the Key-Value interface exported from the core."
---

## Admin Guide


### Overview {#overview}


This module is an implementation of a cache system designed to work with a 
		Couchbase server. It uses the libcouchbase client library to connect to the
		server instance,
		It uses the Key-Value interface exported from the core.


### Advantages


- *memory costs are no longer on the server*
- *many servers can be used inside a cluster, so the memory
				is virtually unlimited*
- *the cache is 100% persistent. A restart
					of OpenSIPS server will not affect the DB. The CouchBase DB is also
				persistent so it can also be restarted without loss of information.*
- *CouchBase is an open-source project so
				it can be used to exchange data
				 with various other applications*
- *By creating a CouchBase Cluster, multiple OpenSIPS
				instances can easily share key-value information*


### Limitations


- *keys (in key:value pairs) may not contain spaces or control characters*


### Dependencies {#dependencies}


#### OpenSIPS Modules


None.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libcouchbase >= 3.0:*
libcoucbase can be downloaded from http://www.couchbase.com/develop/c/current


### Exported Parameters {#exported_parameters}


#### cachedb_url (string) {#param_cachedb_url}


The urls of the server groups that OpenSIPS will connect to in order
			to use the from script cache_store,cache_fetch, etc operations.
			It can be set more than one time.
			The prefix part of the URL will be the identifier that will be used
			from the script.
			The format of the URL is
			couchbase[:identifier]://[username:password@]IP:Port/bucket_name


```c title="Set cachedb_url parameter"
...
modparam("cachedb_couchbase", "cachedb_url","couchbase:group1://localhost:6379/default")
modparam("cachedb_couchbase", "cachedb_url","couchbase:cluster1://random_url:8888/my_bucket")
# Multiple hosts
modparam("cachedb_couchbase", "cachedb_url","couchbase:cluster1://random_url1:8888,random_url2:8888,random_url3:8888/my_bucket")
...
	
```


#### timeout (int) {#param_timeout}


The max duration in microseconds that a couchbase op is expected to last.
			Default is 3000000 ( 3 seconds )


```c title="Set timeout parameter"
...
modparam("cachedb_couchbase", "timeout",5000000);
...
	
```


#### exec_threshold (int) {#param_exec_threshold}


The maximum number of microseconds that a couchbase query can last.
			Anything above the threshold will trigger a warning message to the log


*Default value is "0 ( unlimited - no warnings )".*


```c title="Set exec_threshold parameter"
...
modparam("cachedb_couchbase", "exec_threshold", 100000)
...
	
```


#### lazy_connect (int) {#param_lazy_connect}


Delay connecting to a bucket until the first time it is used.
			Connecting to many buckets at startup can be time consuming. This option allows for
			faster startup by delaying connections until they are needed.
			This option can be dangerous for untested bucket configurations/settings. Always test
			first without lazy_connect.
			This option will show errors in the log during the first access made to a bucket.
			Default is 0 ( Connect to all buckets on startup )


```c title="Set lazy_connect parameter"
...
modparam("cachedb_couchbase", "lazy_connect", 1);
...
	
```


```c title="Use CouchBase servers"
...
cache_store("couchbase:group1","key","$ru value");
cache_fetch("couchbase:cluster1","key",$avp(10));
cache_remove("couchbase:cluster1","key");
...
	
```


#### Exported Functions {#exported_functions}


The module does not export functions to be used
		in configuration script.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 21 | 8 | 1146 | 151 |
| 2. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 17 | 11 | 206 | 238 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 11 | 9 | 101 | 20 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 10 | 7 | 96 | 89 |
| 5. | Ryan Bullock ([@rrb3942](https://github.com/rrb3942)) | 6 | 2 | 230 | 87 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 3 | 5 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 3 |
| 8. | Julián Moreno Patiño | 3 | 1 | 1 | 1 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 2 | 1 | 1 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Mar 2025 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jan 2021 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Mar 2020 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2019 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2017 |
| 7. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 8. | Ryan Bullock ([@rrb3942](https://github.com/rrb3942)) | Oct 2013 - Jun 2015 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jan 2013 - May 2014 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Julián Moreno Patiño, Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Ryan Bullock ([@rrb3942](https://github.com/rrb3942)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
