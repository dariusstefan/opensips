---
title: "cachedb_sql Module"
description: "This module is an implementation of a cache system designed to work with a regular SQL-based server. It uses the internal DB interface to connect to the back-end, and also implements the Key-Value interface exported from the core."
---

## Admin Guide


### Overview {#overview}


This module is an implementation of a cache system designed to work with a 
		regular SQL-based server. It uses the internal DB interface to connect
		to the back-end, and also implements the Key-Value interface exported from the core.


### Advantages


- *memory costs are no longer on the server*
- *the cache is 100% persistent. A restart
					of OpenSIPS server will not affect the DB. The DB is also
				persistent so it can also be restarted without loss of information.*
- *Multiple OpenSIPS instances can easily share key-value information
				via a regular SQL-based database*


### Limitations


- *The module's counter operations ( ADD and SUB ) are currently only 
				supported by MySQL*


### Dependencies {#dependencies}


#### OpenSIPS Modules


None.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *none:*


### Exported Parameters {#exported_parameters}


#### cachedb_url (string) {#param_cachedb_url}


The url of the Database  that OpenSIPS will connect to in order
			to use the from script cache_store,cache_fetch, etc operations.


The format to follow is : sql:[conn_id]-dburl


The parameter can be set multiple times to create multiple connections accessible from the OpenSIPS script.


```c title="Set db_url parameter"
...
modparam("cachedb_sql", "cachedb_url", "sql:1st-mysql://root:vlad@localhost/opensips_sql")
...
	
```


```c title="Usage example"
...
modparam("cachedb_sql", "cachedb_url", "sql:1st-mysql://root:vlad@localhost/opensips_sql")
modparam("cachedb_sql", "cachedb_url", "sql:2nd-postgres://root:vlad@localhost/opensips_pg")
...
...
cache_store("sql:1st-mysql","key","$ru value");
cache_store("sql:2nd-postgres","counter","10");
...
	
```


#### db_table (string) {#param_db_table}


The table of the Database  that OpenSIPS will connect to in order
			to use the from script cache_store,cache_fetch, etc operations.


```c title="Set db_url parameter"
...
modparam("cachedb_sql", "db_table","my_table");
...
	
```


#### key_column (string) {#param_key_column}


The column where the key will be stored


```c title="Set key_column parameter"
...
modparam("cachedb_sql", "key_column","some_name");
...
	
```


#### value_column (string) {#param_value_column}


The column where the value will be stored


```c title="Set value_column parameter"
...
modparam("cachedb_sql", "value_column","some_name");
...
	
```


#### counter_column (string) {#param_counter_column}


The column where the counter value will be stored


```c title="Set counter_column parameter"
...
modparam("cachedb_sql", "counter_column","some_name");
...
	
```


#### expires_column (string) {#param_expires_column}


The column where the expires will be stored


```c title="Set expires_column parameter"
...
modparam("cachedb_sql", "expires_column","some_name");
...
	
```


#### cache_clean_period (int) {#param_cache_clean_period}


The interval in seconds at which the expired keys will be removed from
			the database. Default value is 60 ( seconds )


```c title="Set cache_clean_period parameter"
...
modparam("cachedb_sql", "cache_clean_period",10);
...
	
```


#### Exported Functions {#exported_functions}


The module does not export functions to be used
		in configuration script.


## Frequently Asked Questions


**Q: What happened with the old "db_url" module parameter?**


It was replaced with the "cachedb_url" parameter.
			See the documentation for the usage of the "cachedb_url" parameter.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 16 | 5 | 1001 | 87 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 11 | 9 | 42 | 59 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 7 | 5 | 4 | 2 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 5 | 2 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 1 | 2 |
| 6. | Dusan Klinec ([@ph4r05](https://github.com/ph4r05)) | 3 | 1 | 2 | 2 |
| 7. | Julián Moreno Patiño | 3 | 1 | 2 | 2 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 2 | 1 | 1 | 0 |
| 10. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 1 | 1 | 0 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 2. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Apr 2022 - Apr 2022 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2021 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2017 |
| 8. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 9. | Dusan Klinec ([@ph4r05](https://github.com/ph4r05)) | Dec 2015 - Dec 2015 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jan 2013 - May 2014 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Julián Moreno Patiño, Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
