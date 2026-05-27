---
title: "cachedb_redis Module"
description: "This module is an implementation of a cache system designed to work with a Redis server. It uses hiredis client library to connect to either a single Redis server instance, or to a Redis Server inside a Redis Cluster. It uses the Key-Value interface exported from the core."
---

## Admin Guide


### Overview {#overview}


This module is an implementation of a cache system designed to work with a
		Redis server. It uses hiredis client library to connect to either a single Redis
		server instance, or to a Redis Server inside a Redis Cluster.
		It uses the Key-Value interface exported from the core.


### Advantages


- *memory costs are no longer on the server*
- *many servers can be used inside a cluster, so the memory
				is virtually unlimited*
- *the cache is 100% persistent. A restart
					of OpenSIPS server will not affect the DB. The Redis DB is also
				persistent so it can also be restarted without loss of information.*
- *redis is an open-source project so
				it can be used to exchange data
				 with various other applications*
- *By creating a Redis Cluster, multiple OpenSIPS
				instances can easily share key-value information*


### Redis Stack Support


Starting with OpenSIPS **3.6**, the *cachedb_redis*
		module implements the column-oriented cacheDB API functions.  This makes it a suitable
		cacheDB storage in scenarios such as user location *federation*
		and *full-sharing*, which require this API to be available.


The implementation makes use of *RedisJSON* and *RediSearch* --
		these relatively new features are available in Redis Stack Server, instead of the usual Redis Server
		(Redis OSS project).  More documentation is available on the Redis website.


OpenSIPS will auto-detect availability of the RedisJSON support when necessary and log
		the appropriate messages.


### Limitations


- *keys (in key:value pairs) may not contain spaces or control characters*


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *If a [use tls](#param_use_tls) is defined, the **tls_mgm** module will need to be loaded as well*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *hiredis:*
On the latest Debian based distributions, hiredis can be installed
				by running 'apt-get install libhiredis-dev'

				Alternatively, if hiredis is not available on your OS repos,
				hiredis can be downloaded from: https://github.com/antirez/hiredis .
				Download the archive, extract sources, run make,sudo make install.
If TLS connections are enabled via the [use tls](#param_use_tls) modparam,
				*hiredis* needs to be compiled with TLS support.


### Exported Parameters {#exported_parameters}


#### cachedb_url (string) {#param_cachedb_url}


The URLs of the server groups that OpenSIPS will connect to in order
			to use, from script, the cache_store(), cache_fetch(), etc. operations.
			It may be set more than once.  The prefix part of the URL will be
			the identifier that will be used from the script.


```opensips title="Set cachedb_url parameter"
...
# single-instance URLs (Redis Server or Redis Cluster)
modparam("cachedb_redis", "cachedb_url", "redis:group1://localhost:6379/")
modparam("cachedb_redis", "cachedb_url", "redis:cluster1://random_url:8888/")

# multi-instance URL (will perform circular 
```


```c title="Use Redis servers"
...
cache_store("redis:group1", "key", "$ru value");
cache_fetch("redis:cluster1", "key", $avp(10));
cache_remove("redis:cluster1", "key");
...
		
```


#### connect_timeout (integer) {#param_connect_timeout}


This parameter specifies how many milliseconds OpenSIPS should wait
			for connecting to a Redis node.


*Default value is "5000 ms".*


```opensips title="Set connect_timeout parameter"
...
# wait 1 seconds for Redis to connect
modparam("cachedb_redis", "connect_timeout",1000)
...
		
```


#### query_timeout (integer) {#param_query_timeout}


This parameter specifies how many milliseconds OpenSIPS should wait
			for a query response from a Redis node.


*Default value is "5000 ms".*


```opensips title="Set connect_timeout parameter"
...
# wait 1 seconds for Redis queries
modparam("cachedb_redis", "query_timeout",1000)
...
		
```


#### shutdown_on_error (integer) {#param_shutdown_on_error}


By setting this parameter to 1, OpenSIPS will abort startup if
		the initial connection to Redis is not possible. Runtime reconnect
		behavior is unaffected by this parameter, and is always enabled.


*Default value is "0" (disabled).*


```opensips title="Set the shutdown_on_error parameter"
...
# abort OpenSIPS startup if Redis is down
modparam("cachedb_redis", "shutdown_on_error", 1)
...
		
```


#### use_tls (integer) {#param_use_tls}


Setting this parameter will allow you to use TLS for Redis connections.
		In order to enable TLS for a specific connection, you can use the
		"tls_domain=*dom_name*" URL parameter in the cachedb_url
		of this module (or other modules that use the CacheDB interface). This should
		be placed at the end of the URL after the '?' character.


When using this parameter, you must also ensure that
		*tls_mgm* is loaded and properly configured. Refer to
		the the module for additional info regarding TLS client domains.


Note that TLS is supported by Redis starting with version 6.0. Also, it is
		an optional feature enabled at compile time and might not be included in the
		standard Redis packages available for your OS.


*Default value is **0** (not enabled)*


```opensips title="Set the use_tls parameter"
...
modparam("tls_mgm", "client_domain", "redis")
modparam("tls_mgm", "certificate", "[redis]/etc/pki/tls/certs/redis.pem")
modparam("tls_mgm", "private_key", "[redis]/etc/pki/tls/private/redis.key")
modparam("tls_mgm", "ca_list",     "[redis]/etc/pki/tls/certs/ca.pem")
...
modparam("cachedb_redis", "use_tls", 1)
modparam("cachedb_redis", "cachedb_url","redis://localhost:6379/?tls_domain=redis")
...
```


#### ftsearch_index_name (string) {#param_ftsearch_index_name}


Only relevant with *RedisJSON* and
			*RediSearch* server-side support.


A global index name to be used for all internal JSON full-text search operations.
		Future extensions may add, e.g., a connection-level index name setting.


Default value is **"idx:usrloc"**.


```opensips title="Set the ftsearch_index_name parameter"
modparam("cachedb_redis", "ftsearch_index_name", "ix::usrloc")
```


#### ftsearch_json_prefix (string) {#param_ftsearch_json_prefix}


Only relevant with *RedisJSON* and
			*RediSearch* server-side support.


A key naming prefix for all internally-created Redis JSON objects (e.g.
		created with JSON.SET or JSON.MSET).


Default value is **"usrloc:"**.


```opensips title="Set the ftsearch_json_prefix parameter"
modparam("cachedb_redis", "ftsearch_json_prefix", "userlocation:")
```


#### ftsearch_max_results (integer) {#param_ftsearch_max_results}


Only relevant with *RedisJSON* and
			*RediSearch* server-side support.


The maximum number of results returned by each internally-triggered
		FT.SEARCH JSON lookup query.


Default value is **10000** max results.


```opensips title="Set the ftsearch_max_results parameter"
modparam("cachedb_redis", "ftsearch_max_results", 100)
```


#### ftsearch_json_mset_expire (integer) {#param_ftsearch_json_mset_expire}


Only relevant with *RedisJSON* and
			*RediSearch* server-side support.


A Redis EXPIRE timer to set/refresh on the JSON key after each JSON.MSET operation
		(create the JSON or add/remove subkeys), in seconds.  A value of **0**
		disables the EXPIRE queries completely.


Default value is **3600** seconds.


```opensips title="Set the ftsearch_json_mset_expire parameter"
modparam("cachedb_redis", "ftsearch_json_mset_expire", 7200)
```


### Exported Functions {#exported_functions}


The module does not export functions to be used
		in configuration script.


### Raw Query Syntax


The cachedb_redis module allows to run RAW queries, thus taking full advantage of the capabilities of the back-end.

			The query syntax is the typical REDIS one.


Here are a couple examples of running some Redis queries :


```opensips title="Redis Raw Query Examples"
...
	$var(my_hash) = "my_hash_name";
	$var(my_key) = "my_key_name";
	$var(my_value) = "my_key_value";
	cache_raw_query("redis","HSET $var(my_hash) $var(my_key) $var(my_value)");
	cache_raw_query("redis","HGET $var(my_hash) $var(my_key)","$avp(result)");
	xlog("We have fetched $avp(result) \n");
...
	$var(my_hash) = "my_hash_name";
	$var(my_key1) = "my_key1_name";
	$var(my_key2) = "my_key2_name";
	$var(my_value1) = "my_key1_value";
	$var(my_value2) = "my_key2_value";
	cache_raw_query("redis","HSET $var(my_hash) $var(my_key1) $var(my_value1)");
	cache_raw_query("redis","HSET $var(my_hash) $var(my_key2) $var(my_value2)");
	cache_raw_query("redis","HGETALL $var(my_hash)","$avp(result)");

	$var(it) = 0;
	while ($(avp(result_final)[$var(it)]) != NULL) {
		xlog("Multiple key reply: - we have fetched $(avp(result_final)[$var(it)]) \n");
		$var(it) = $var(it) + 1;
	}
...
			
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 43 | 23 | 1720 | 288 |
| 2. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 33 | 19 | 1446 | 50 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 17 | 14 | 150 | 48 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 13 | 6 | 595 | 38 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 8 | 6 | 14 | 6 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 6 | 4 | 7 | 7 |
| 7. | jalung | 5 | 1 | 144 | 61 |
| 8. | Norm Brandinger | 4 | 1 | 207 | 5 |
| 9. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 3 | 1 | 15 | 15 |
| 10. | Ezequiel Lovelle | 3 | 1 | 11 | 4 |


**All remaining contributors**: John Burke ([@john08burke](https://github.com/john08burke)), tcresson, Eddie Fiorentine, Julián Moreno Patiño, Ken Rice, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), zhengsh, Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)), Kristian Høgh.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Mar 2026 |
| 2. | Ken Rice | Sep 2025 - Sep 2025 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - May 2025 |
| 4. | Norm Brandinger | May 2025 - May 2025 |
| 5. | Eddie Fiorentine | Mar 2025 - Mar 2025 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jan 2025 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Oct 2011 - Nov 2024 |
| 8. | tcresson | Oct 2023 - Oct 2023 |
| 9. | zhengsh | Aug 2023 - Aug 2023 |
| 10. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |


**All remaining contributors**: John Burke ([@john08burke](https://github.com/john08burke)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Dan Pascu ([@danpascu](https://github.com/danpascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Kristian Høgh, Julián Moreno Patiño, Jarrod Baumann ([@jarrodb](https://github.com/jarrodb)), jalung, Ezequiel Lovelle.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Julián Moreno Patiño, Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).


## Frequently Asked Questions


**Q: My OpenSIPS is occasionally crashing in libhiredis, what to do?**


Make sure you've upgraded the Redis "libhiredis" client library to at
		least version 0.14.1.  There was at least one significant vulnerability
		reported in library versions prior to that one ([CVE-2020-7105](https://bugzilla.redhat.com/show_bug.cgi?id=CVE-2020-7105)),
		so upgrading to latest stable may very well fix the crash!


*Documentation Copyrights:*


Copyright © 2011 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
