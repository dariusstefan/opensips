---
title: "Trie Module"
---

## Admin Guide


### Overview {#overview}


#### Introduction


Trie is a module for efficiently caching and lookup of a set of prefixes ( stored in a trie data structure )


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *a database module*.


#### External Libraries or Applications


- *none*.


### Exported Parameters {#exported_parameters}


#### trie_table(str) {#trie_table}


The name of the db table storing prefix rules.


*Default value is "trie_table".*


```c title="Set trie_table parameter"
...
modparam("trie", "trie_table", "my_prefix_table")
...
```


#### no_concurrent_reload (int) {#param_no_concurrent_reload}


If enabled, the module will not allow do run multiple trie:reload
			MI commands in parallel (with overlapping)  Any new reload will
			be rejected (and discarded) while an existing reload is in
			progress.


If you have a large routing set (millions of rules/prefixes), you
			should consider disabling concurrent reload as they will exhaust
			the shared memory (by reloading into memory, in the same time,
			multiple instances of routing data).


*Default value is "0 (disabled)".*


```c title="Set no_concurrent_reload parameter"
...
# do not allow parallel reload operations
modparam("trie", "no_concurrent_reload", 1)
...
```


#### use_partitions (int) {#param_use_partitions}


Flag to configure whether to use partitions for tries. If this
		flag is set then the `db_partitions_url` and
		`db_partitions_table`
		variables become mandatory.


*Default value is "0".*


```c title="Set use_partitions parameter"
...
modparam("trie", "use_partitions", 1)
...
```


#### db_partitions_url (str) {#param_db_partitions_url}


The url to the database containing partition-specific
		information.The `use_partitions` parameter
	    must be set to 1.


*Default value is ""NULL"".*


```c title="Set db_partitions_url parameter"
...
modparam("trie", "db_partitions_url", "mysql://user:password@localhost/opensips_partitions")
...
```


#### db_partitions_table (str) {#param_db_partitions_table}


The name of the table containing partition definitions. To be
		used with `use_partitions` and `db_partitions_url`.


*Default value is "trie_partitions".*


```c title="Set db_partitions_table parameter"
...
modparam("trie", "db_partitions_table", "trie_partition_defs")
...
```


#### extra_prefix_chars (str) {#param_extra_prefix_chars}


List of ASCII (0-127) characters to be additionally accepted in
			the prefixes. By default only '0' - '9' chars (digits) are
			accepted.


*Default value is "NULL".*


```c title="Set extra_prefix_chars parameter"
...
modparam("trie", "extra_prefix_chars", "#-%")
...
```


### Exported Functions {#exported_functions}


#### trie_search(number, [flags], [trie_attrs_pvar], [match_prefix_pvar], [partition]) {#func_trie_search}


Function to search for an entry ( number ) in a trie.


This function can be used from all routes.


If you set `use_partitions` to 1 the 
		**partition** last parameter becomes 
		mandatory.


All parameters are optional. Any of them may be ignored, provided
		the necessary separation marks "," are properly placed.


- **number** (str) - number to be searched in the trie
- **flags** (string, optional) - a list
			of letter-like flags for controlling the routing behavior.
			Possible flags are:

  - **L** - Do strict length matching
				over the prefix - actually the trie engine will do full number 
				matching and not prefix matching anymore.
- **trie_attrs_pvar** (var, optional) - a
			writable variable which will be  populated with the attributes of the
			matched trie rule.
- **match_prefix_pvar** (var, optional) - a
			writable variable which will be the actual prefix matched in the trie.
- **partition** (string, optional) - the name
			of the trie partition to be used. This parameter is to be defined
			ONLY if the "use_partition" module parameter is turned on.


```c title="trie_search usage"
...
if (trie_search("$rU","L",$avp(code_attrs),,"my_partition")) {
    # we found it in the trie, it's a match
    xlog("We found $rU in the trie with attrs $avp(code_attrs) \n");
}
```


### Exported MI Functions {#exported_mi_functions}


#### trie:reload {#mi_reload}


Replaces obsolete MI command: *trie_reload*.


Command to reload trie rules from database.


- if `use_partition` is set to 0 - all routing rules will be reloaded.
- if `use_partition` is set to 1, the parameters are:
					
						*partition_name* (optional) - if not provided
							all the partitions will be reloaded, otherwise just the partition given as parameter will be reloaded.


MI FIFO Command Format:


```c
		opensips-cli -x mi trie:reload part_1
		
```


#### trie:reload_status {#mi_reload_status}


Replaces obsolete MI command: *trie_reload_status*.


Gets the time of the last reload for any partition.


- if `use_partition` is set to 0 - the function
					doesn't receive any parameter. It will list the date of the
					last reload for the default (and only) partition.
- if `use_partition` is set to 1, the parameters are:
					
						*partition_name* (optional) - if not provided
							the function will list the time of the last update for every
							partition. Otherwise, the function will list the time of the last
							reload for the given partition.


```c title="trie:reload_status usage when use_partitions is 0"
$ opensips-cli -x mi trie:reload_status
Date:: Tue Aug 12 12:26:00 2014
```


#### trie:search {#mi_search}


Replaces obsolete MI command: *trie_search*.


Tries to match a number in the existing tries loaded from the database.


- if `use_partition` is set to 1 the function
					will have 2 parameters:
					
						
							*partition_name*
						
						
							*number* - the number to test against
- if `use_partition` is set to 0 the function will have 1 parameter:
					
						*number* - the number to test against


MI FIFO Command Format:


```c
		opensips-cli -x mi trie:search partition_name=part1 number=012340987
		
```


#### trie:number_delete {#mi_number_delete}


Replaces obsolete MI command: *trie_number_delete*.


Deletes individual entries in the trie, without reloading all of the data


- if `use_partition` is set to 1 the function
					will have 2 parameters:
					
						
							*partition_name*
						
						
							*number* - the array of numbers to delete


MI FIFO Command Format:


```c
		opensips-cli -x mi trie:number_delete partition_name=part1 number=["012340987","4858345"]
		
```


#### trie:number_upsert {#mi_number_upsert}


Replaces obsolete MI command: *trie_number_upsert*.


Upserts ( insert if not found, update is found ) an array of numbers in the trie, without reloading all of the data


- if `use_partition` is set to 1 the function
					will have 3 parameters:
					
						
							*partition_name*
						
						
							*number* - the array of numbers to update
						
						
							*attrs* - the array of new attributes for the numbers


MI FIFO Command Format:


```c
		opensips-cli -x mi trie:number_upsert partition_name=part1 number=["012340987"] attrs=["my_attrs"]
		
```


### Installation


The module requires some tables in the OpenSIPS database.
	You can also find the complete database documentation on the project webpage, [https://opensips.org/docs/db/db-schema-devel.html](https://opensips.org/docs/db/db-schema-devel.html).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 28 | 4 | 2652 | 4 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 7 | 5 | 49 | 42 |
| 3. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jan 2025 - Mar 2026 |
| 2. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jan 2026 - Jan 2026 |
| 3. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Dec 2024 - May 2025 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).


*Documentation Copyrights:*


Copyright © 2024 OpenSIPS Project
