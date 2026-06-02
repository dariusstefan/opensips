---
title: "Dynamic Sockets Management Module"
description: "This module provides the means to provision and manage dynamic sockets for OpenSIPS at runtime. The definition of the sockets is stored in an SQL database and can be dynamically changed at runtime."
---

## Admin Guide


### Overview {#overview}


This module provides the means to provision and manage dynamic sockets
		for OpenSIPS at runtime. The definition of the sockets is stored in
		an SQL database and can be dynamically changed at runtime.


The module caches the entire table sockets and only adjusts the
		dynamic socket list after a reload using the
		[mi sockets reload](#mi_sockets_reload) MI command.


The [mi sockets list](#mi_sockets_list) MI command.
		can be used to show all the dynamic sockets OpenSIPS is listening on.


### Sockets {#sockets}


The module exclusively handles sockets used for SIP traffic (e.g.,
		UDP, TCP, TLS, WSS). It does not support BIN or HEP listeners, as
		these cannot be dynamically utilized or enforced in the script.


The management of dynamic sockets is divided into two behaviors,
		depending on whether the traffic is UDP-based or TCP-based. Based on
		the nature of your traffic, ensure that your settings are
		properly tuned to accommodate any sockets you may provision
		dynamically.


#### UDP handling {#section_udp}


All dynamically added UDP sockets are assigned to a group of dedicated
		extra processes. The number of these processes can be adjusted using
		the [processes](#param_processes) parameter. These processes handle
		UDP-based socket traffic evenly by balancing requests across the less
		loaded processes. The difference, however, is that static sockets are
		bound to designated processes, while dynamic sockets share the pool of
		extra processes.


#### TCP handling {#section_tcp}


In contrast to UDP traffic handling, TCP traffic is processed in the
		same way as all other TCP traffic: requests are dispatched to one of
		the existing static TCP processes.


### Limitations {#limitations}


Although traffic processing by dynamic workers closely resembles that
		of static ones, there are certain limitations associated with using
		dynamic sockets:


- UDP socket handling does not currently benefit from the
			autoscaling feature for the designated extra
			processes. This means that the number of
			[processes](#param_processes) defined at startup will
			always be forked, and only these processes will handle all
			traffic associated with dynamically added UDP sockets.
- As stated earlier, the module only supports SIP based dynamic
			listener, no HEP or BIN.
- Sockets defined in the database cannot be expanded to more than
			one listener. This means you cannot use an interface name or an
			alias that resolves to multiple IPs as a host. Only a single
			IP:port socket will be created, so provisioning should ideally be
			done with an explicit IP.
- Due to some internal limitations, the dynamic sockets need to be
			pre-allocated at startup. This means that the number of dynamic
			sockets used at runtime have to be limited by a static value,
			defined at startup. This is why it is recommended to use a fairly
			high value for the sockets in the [max sockets](#param_max_sockets)
			parameter - we're defaulting a confortable 100 sockets.
- The sockets defined in the  [max sockets](#param_max_sockets) are
			being rotated in a FIFO manner - this way we are trying to avoid
			overlapping sockets in a short period of time.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *A database module is needed for fetching the sockets*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### db_url (string) {#param_db_url}


The database URL where the sockets are fetched from.


*Default value is "mysql://opensips:opensipsrw@localhost/opensips".*


```c title="Set 'db_url' parameter"
...
modparam("sockets_mgm", "db_url", "dbdriver://username:password@dbhost/dbname")
...
```


#### table_name (string) {#param_table_name}


The database table name where the sockets are stored.


*Default value is "sockets".*


```c title="Set 'table_name' parameter"
...
modparam("sockets_mgm", "table_name", "sockets_def")
...
```


#### socket_column (string) {#param_socket_column}


The database table column where the socket definition is stored.


*Default value is "socket".*


```c title="Set 'socket_column' parameter"
...
modparam("sockets_mgm", "socket_column", "sock")
...
```


#### advertised_column (string) {#param_advertised_column}


The database table column where the advertised definition is stored.


*Default value is "advertised".*


```c title="Set 'advertised_column' parameter"
...
modparam("advertiseds_mgm", "advertised_column", "adv")
...
```


#### tag_column (string) {#param_tag_column}


The database table column where the tag definition is stored.


*Default value is "tag".*


```c title="Set 'tag_column' parameter"
...
modparam("tags_mgm", "tag_column", "sock")
...
```


#### flags_column (string) {#param_flags_column}


The database table column where the flags definition is stored.


*Default value is "flags".*


```c title="Set 'flags_column' parameter"
...
modparam("flagss_mgm", "flags_column", "sock")
...
```


#### tos_column (string) {#param_tos_column}


The database table column where the tos definition is stored.


*Default value is "tos".*


```c title="Set 'tos_column' parameter"
...
modparam("toss_mgm", "tos_column", "sock")
...
```


#### processes (integer) {#param_processes}


The number of processes designated to handle UDP sockets.


*Default value is "8".*


```c title="Set 'processes' parameter"
...
modparam("sockets_mgm", "processes", 32)
...
```


#### max_sockets (integer) {#param_max_sockets}


The maximum number of sockets that can be defined dynamically.
			See the [limitations](#limitations) section for more information.


*Default value is "100".*


```c title="Set 'max_sockets' parameter"
...
modparam("sockets_mgm", "max_sockets", 2000)
...
```


### Exported MI Functions {#exported_mi_functions}


#### sockets_reload {#mi_sockets_reload}


MI command used to reload the sockets from the database.


MI FIFO Command Format:


```c
		## reload sockets from the database
		opensips-mi sockets_reload
		opensips-cli -x mi sockets_reload
		
```


#### sockets_list {#mi_sockets_list}


MI command to list all the currently used dynamic sockets.


MI FIFO Command Format:


```c
		## reload sockets from the database
		opensips-mi sockets_list
		opensips-cli -x mi sockets_list
		
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 33 | 4 | 2207 | 572 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2025 - Jun 2025 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2025 - May 2025 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
