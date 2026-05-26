---
title: "Dynamic Sockets Management Module"
description: "This module provides the means to provision and manage dynamic sockets for OpenSIPS at runtime. The definition of the sockets is stored in an SQL database and can be dynamically changed at runtime."
---

## Admin Guide


### Overview


This module provides the means to provision and manage dynamic sockets
		for OpenSIPS at runtime. The definition of the sockets is stored in
		an SQL database and can be dynamically changed at runtime.


The module caches the entire table sockets and only adjusts the
		dynamic socket list after a reload using the
		[mi reload](#mi_reload) MI command.


The [mi list](#mi_list) MI command.
		can be used to show all the dynamic sockets OpenSIPS is listening on.


### Sockets


The module exclusively handles sockets used for SIP traffic (e.g.,
		UDP, TCP, TLS, WSS). It does not support BIN or HEP listeners, as
		these cannot be dynamically utilized or enforced in the script.


The management of dynamic sockets is divided into two behaviors,
		depending on whether the traffic is UDP-based or TCP-based. Based on
		the nature of your traffic, ensure that your settings are
		properly tuned to accommodate any sockets you may provision
		dynamically.


#### UDP handling


All dynamically added UDP sockets are assigned to a group of dedicated
		extra processes. The number of these processes can be adjusted using
		the [processes](#param_processes) parameter. These processes handle
		UDP-based socket traffic evenly by balancing requests across the less
		loaded processes. The difference, however, is that static sockets are
		bound to designated processes, while dynamic sockets share the pool of
		extra processes.


#### TCP handling


In contrast to UDP traffic handling, TCP traffic is processed in the
		same way as all other TCP traffic: requests are dispatched to one of
		the existing static TCP processes.


### Limitations


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


### Dependencies


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *A database module is needed for fetching the sockets*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters


#### db_url (string)


The database URL where the sockets are fetched from.


*Default value is "mysql://opensips:opensipsrw@localhost/opensips".*


**Example: Set "db_url" parameter**


```opensips
...
modparam("sockets_mgm", "db_url", "dbdriver://username:password@dbhost/dbname")
...
```


#### table_name (string)


The database table name where the sockets are stored.


*Default value is "sockets".*


**Example: Set "table_name" parameter**


```opensips
...
modparam("sockets_mgm", "table_name", "sockets_def")
...
```


#### socket_column (string)


The database table column where the socket definition is stored.


*Default value is "socket".*


**Example: Set "socket_column" parameter**


```opensips
...
modparam("sockets_mgm", "socket_column", "sock")
...
```


#### advertised_column (string)


The database table column where the advertised definition is stored.


*Default value is "advertised".*


**Example: Set "advertised_column" parameter**


```opensips
...
modparam("sockets_mgm", "advertised_column", "adv")
...
```


#### tag_column (string)


The database table column where the tag definition is stored.


*Default value is "tag".*


**Example: Set "tag_column" parameter**


```opensips
...
modparam("sockets_mgm", "tag_column", "sock")
...
```


#### flags_column (string)


The database table column where the flags definition is stored.


*Default value is "flags".*


**Example: Set "flags_column" parameter**


```opensips
...
modparam("sockets_mgm", "flags_column", "sock")
...
```


#### tos_column (string)


The database table column where the tos definition is stored.


*Default value is "tos".*


**Example: Set "tos_column" parameter**


```opensips
...
modparam("sockets_mgm", "tos_column", "sock")
...
```


#### processes (integer)


The number of processes designated to handle UDP sockets.


*Default value is "8".*


**Example: Set "processes" parameter**


```opensips
...
modparam("sockets_mgm", "processes", 32)
...
```


#### max_sockets (integer)


The maximum number of sockets that can be defined dynamically.
			See the [limitations](#limitations) section for more information.


*Default value is "100".*


**Example: Set "max_sockets" parameter**


```opensips
...
modparam("sockets_mgm", "max_sockets", 2000)
...
```


### Exported MI Functions


#### sockets_mgm:reload


Replaces obsolete MI command: *sockets_reload*.


MI command used to reload the sockets from the database.


MI FIFO Command Format:


```
		## reload sockets from the database
		opensips-mi sockets_mgm:reload
		opensips-cli -x mi sockets_mgm:reload
		
```


#### sockets_mgm:list


Replaces obsolete MI command: *sockets_list*.


MI command to list all the currently used dynamic sockets.


MI FIFO Command Format:


```
		## reload sockets from the database
		opensips-mi sockets_mgm:list
		opensips-cli -x mi sockets_mgm:list
		
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 36 | 7 | 2229 | 586 |
| 2. | Norm Brandinger | 4 | 2 | 5 | 5 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2025 - Mar 2026 |
| 2. | Norm Brandinger | Jun 2025 - Jun 2025 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Norm Brandinger.


*Documentation Copyrights:*


Copyright © 2025 OpenSIPS Solutions;
