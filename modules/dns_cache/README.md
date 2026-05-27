---
title: "dns_cache Module"
description: "This module is an implementation of a cache system designed for DNS records. For successful DNS queries of all types, the module will store in a cache/db backend the mappings, for TTL number of seconds received in the DNS answer. Failed DNS queries will also be stored in the back-end, with ..."
---

## Admin Guide


### Overview {#overview}


This module is an implementation of a cache system designed for DNS records.
		For successful DNS queries of all types, the module will store in a cache/db
		backend the mappings, for TTL number of seconds received in the DNS answer.
		Failed DNS queries will also be stored in the back-end, with a TTL that can be
		specified by the user.
		The module uses the Key-Value interface exported from the core.


### Dependencies {#dependencies}


#### OpenSIPS Modules


A cachedb_* type module must be loaded before loading
		the dns_cache module.


### Exported Parameters {#exported_parameters}


#### cachedb_url (string) {#param_cachedb_url}


The url of the key-value back-end that will be used
			for storing the DNS records.


```c title="Set cachedb_url parameter"
...
#use internal cachedb_local module
modparam("dns_cache", "cachedb_url","local://")
#use cachedb_memcached module with memcached server at 192.168.2.130
modparam("dns_cache", "cachedb_url","memcached://192.168.2.130:8888/")
...
		
```


#### blacklist_timeout (int) {#param_blacklist_timeout}


The number of seconds that a failed DNS query will be kept in cache.
			Default is 3600.


```c title="Set blacklist_timeout parameter"
...
modparam("dns_cache", "blacklist_timeout",7200) # 2 hours
...
		
```


### Exported Functions {#exported_functions}


The module does not export functions to be used
		in configuration script.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 15 | 5 | 1006 | 3 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 12 | 10 | 46 | 44 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 11 | 9 | 12 | 14 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 8 | 6 | 16 | 7 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 2 | 1 | 1 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2012 - Mar 2020 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Sep 2019 |
| 3. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jun 2018 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2017 |
| 6. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Feb 2012 - Oct 2012 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).


*Documentation Copyrights:*


Copyright © 2012 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
