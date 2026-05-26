---
title: "cachedb_local Module"
description: "This module is an implementation of a local cache system designed as a hash table. It uses the Key-Value interface exported by OpenSIPS core. Starting with version 2.3, the module can have multiple hash tables, called collections. Each url for cachedb_local module points to one collection..."
---

## Admin Guide


### Overview


This module is an implementation of a local cache system designed as
		a hash table. It uses the Key-Value interface exported by OpenSIPS core.
		Starting with version 2.3, the module can have multiple hash tables,
		called collections. Each url for cachedb_local module points to one
		collection. One collection can be shared between multiple urls.


### Clustering


Cachedb_local clustering is a mechanism used to mirror local cache changes
			taking place in one OpenSIPS instance to one or multiple other instances without
			the need of third party dependencies.
			The process is simplified by using the clusterer module which facilitates the
			management of a cluster of OpenSIPS noeds and the sending of replication-related
			BIN packets (binary-encoded, using proto_bin). This might be usefull for implementing
			a hot stand-by system, where the stand-by instance can take over without the need
			of filling the cache by its own.


The following cache operations will be distributet within the cluster:


- cache_store
- cache_remove
- cache_add
- cache_sub


In addition to the event-driven replication, an OpenSIPS instance will first
			try to learn all the local cache information from antoher node in the cluster at startup.
			The data synchronization mechanism requires defining one of the nodes in the cluster
			as a "**seed**" node.
			See the [clusterer](https://opensips.org/docs/modules/3.0.x/clusterer.html#capabilities)
			module for details on how to do this and why is it needed.


*Note:* You have to explicitly specify which collections
			you want to replicate when you set [cache collections](#param_cache_collections).


**Limitations:** The clustering operations are not atomic
			and constistency over the cluster nodes is not guaranteed.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *clusterer, if [cluster id](#param_cluster_id)
					is set.*


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *none*


### Exported Parameters


#### `cachedb_url` (string)


URLs of local cache groups to be used used for the script and MI cacheDB
		operations. The parameter can be set multiple times.


One collection can belong to multiple URLs, but one URL can have only one collection.
		Redefining an URL with the same schema and group name will result in overwriting
		that URL. Each collection used in URL definition must be defined using
		*cachedb_collection* parameter. The collection shall be defined
		as a normal database, at the end of the URL as in the examples. In the script the
		collection shall be identified using the schema and, if exists, the group name.


*"If no URL defined, the url with no group name and collection "default"
					will be used.".*


**Example: Set `cachedb_url` parameter**


```opensips
...
### for this example, if no collection is defined, the default collection named
### "default" shall be used
modparam("cachedb_local", "cachedb_url", "local://")
### this URL will use the collection named collection1; it will overwrite the
### previous url definition which was using the "default" collection
modparam("cachedb_local", "cachedb_url", "local:///collection1")
### this URL will use collection2; it will be referenced from the script
### with "local:group2"
modparam("cachedb_local", "cachedb_url", "local:group2:///collection2")

## how to use the URLs from the script
## as defined above, this call will use collection1
cache_store("local", ...)
## as defined above, this call will use collection2
cache_store("local:group2", ...)
...
	
```


#### `cache_collections` (string)


Using this parameter, collections(hash tables) and their sizes can be defined. Each
			collection definition must be separated one from another using ';'. Default size
			for a hash is 512. The size must be separated from the name of the collection using
			'='.


If clustering is enabled you have to specify which collections you want to replicate
			with the */r* suffix to the collection name.


The *"default"* collection always gets created, even when
				not included in this list of collections.


**Example: Set `cache_collections` parameter**


```opensips
...
## creating collection1 with default size (512) and collection2 with custom size
## 2^5 (32); we also changed the size of the default collection, which would have been
## created anyway from 2^9 - 512 (default value) to 2^4 - 16
## also, collection1 and collection2 will be replicated in the cluster, while the
## default collection will be local to this node
modparam("cachedb_local", "cache_collections", "collection1/r; collection2/r = 5; default = 4")
...
	
```


#### `cache_clean_period` (int)


The time interval in seconds at which to go through all the
			records and delete the expired ones.


*Default value is "600 (10 minutes)".*


**Example: Set `cache_clean_period` parameter**


```opensips
...
modparam("cachedb_local", "cache_clean_period", 1200)
...
	
```


#### `cluster_id` (int)


Specifies the cluster ID which this instance will send to and receive
		cache data.


This OpenSIPS cluster exposes the **"cachedb-local-repl"**
capability in order to mark nodes as eligible for becoming data donors during an
arbitrary sync request. Consequently, the cluster must have *at least
one node* marked with the **"seed"** value
as the *clusterer.flags* column/property in order to be fully functional.
Consult the [clusterer - Capabilities](clusterer#capabilities)
chapter for more details.


Default value is 0 (replication disabled).


**Example: Setting the `cluster_id` parameter**


```opensips
...
modparam("cachedb_local", "cluster_id", 1)
...
		
```


#### `cluster_persistency` (string)


Controls the behavior of the OpenSIPS local cachedb clustering following a restart.


This parameter may take the following values:


- *"none"* - no explicit data
				synchronization following a restart. The node starts empty.
- *"sync-from-cluster"* - enable
				cluster-based restart persistency. Following a restart,
				an OpenSIPS cluster node will search for a healthy "donor" node
				from which to mirror the entire user location dataset via
				direct cluster sync (TCP-based, binary-encoded data transfer).
				This will require the configuration of one or multiple "seed"
				nodes in the cluster.


*Default value is
				*"sync-from-cluster"*.*


**Example: Set `cluster_persistency` parameter**


```opensips
...
modparam("cachedb_local", "cluster_persistency", "sync-from-cluster")
...
		
```


#### `enable_restart_persistency` (int)


Enable restart persistency using the persistent memory mechanism. Data is
			stored in a cache file that is mapped against OpenSIPS memory.


Note that you have to keep the same collection definitions from a previous
			run in order to use the cached data for the respective collections.


If cluster persistency is enabled as well, keys loaded from the persistent
			cache will be discarded if they are not received in the cluster sync data.


*Default value is "0 (disabled)".*


**Example: Set `enable_restart_persistency` parameter**


```opensips
...
modparam("cachedb_local", "enable_restart_persistency", yes)
...
```


### Exported Functions


#### `cache_remove_chunk([collection,] glob)`


Remove all keys from local cache that match the *glob* pattern
			corresponding to a certain *collection* or the 'default' collection
			if none defined. Keep in mind that collection name is different than group name,
			which identifies the engine in cachedb operations.


Parameters:


- *collection* (string, optional)
- *glob* (string)


This function can be used from all routes


**Example: `cache_remove_chunk` usage**


```
	...
	cache_remove_chunk("myinfo_*");
	cache_remove_chunk("collection1", "myinfo_*");
	...
	
```


### Exported MI Functions


#### `cachedb_local:remove_chunk`


Replaces obsolete MI command: *cache_remove_chunk*.


Removes all local cache entries that match the provided glob param.


Parameters :


- *glob* - keys that match glob will be removed
- *collection(optional)* - collection from which the keys shall
					be removed; if no collection set, the default collection will be used;


MI FIFO Command Format:


```
opensips-cli -x mi cachedb_local:remove_chunk "keyprefix*" collection
		
```


#### `cachedb_local:fetch_chunk`


Replaces obsolete MI command: *cache_fetch_chunk*.


Fetches all local cache entries that match the provided glob param.


Parameters :


- *glob* - keys that match glob will be returned
- *collection(optional)* - collection from which the keys shall
					be retrieved; if no collection set, the default collection will be used;


MI FIFO Command Format:


```
opensips-cli -x mi cachedb_local:fetch_chunk "keyprefix*" collection
{
    "keys": [
        {
            "name": "keyprefix_1",
            "value": "key 1 data here"
        },
        {
            "name": "keyprefix_2",
            "value": "key 2 data here"
        }
    ]
}
		
```


## Frequently Asked Questions


**Q: What happened with old cache_table_size parameter?**


The parameter was removed because it was redundant. Since the
			addition of collections, the old hash now belongs to the
			default collection. This collection is created every time and
			it has a default size of 512. The size can be changed by
			setting the default collection size using cache_collections paramter.


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 35 | 11 | 1335 | 693 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 25 | 16 | 509 | 226 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 24 | 18 | 165 | 191 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 16 | 14 | 46 | 50 |
| 5. | Anca Vamanu | 13 | 5 | 739 | 54 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 10 | 8 | 26 | 16 |
| 7. | Andrei Dragus | 10 | 4 | 182 | 181 |
| 8. | Fabian Gast ([@fgast](https://github.com/fgast)) | 9 | 3 | 517 | 48 |
| 9. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 9 | 3 | 513 | 55 |
| 10. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 9 | 10 |


**All remaining contributors**: Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Ryan Bullock ([@rrb3942](https://github.com/rrb3942)), Julián Moreno Patiño, Zero King ([@l2dy](https://github.com/l2dy)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Mar 2026 |
| 2. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Oct 2011 - Feb 2024 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Jan 2021 - Feb 2023 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Jan 2017 - Oct 2022 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2021 |
| 6. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Feb 2020 |
| 8. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jan 2009 - Apr 2019 |
| 9. | Fabian Gast ([@fgast](https://github.com/fgast)) | Dec 2018 - Dec 2018 |
| 10. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Jan 2017 - Jan 2017 |


**All remaining contributors**: Julián Moreno Patiño, Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Ryan Bullock ([@rrb3942](https://github.com/rrb3942)), Andrei Dragus, Anca Vamanu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Zero King ([@l2dy](https://github.com/l2dy)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Fabian Gast ([@fgast](https://github.com/fgast)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Andrei Dragus, Anca Vamanu.


*Documentation Copyrights:*


Copyright © 2009 Anca-Maria Vamanu
