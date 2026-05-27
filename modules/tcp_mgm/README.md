---
title: "TCP Management Module (tcp_mgm)"
description: "This module provides optional, SQL-based support for fine-grained management of all TCP connections taking place on OpenSIPS."
---

## Admin Guide


### Overview {#overview}


This module provides optional, SQL-based support for fine-grained
		management of all TCP connections taking place on OpenSIPS.


### Dependencies {#dependencies}


#### OpenSIPS Modules


At least one SQL database module must be loaded (e.g. "db_xxx").


#### External Libraries or Applications


None.


### Exported Parameters {#exported_parameters}


#### db_url (string) {#param_db_url}


Mandatory URL to the SQL database.


```c title="Setting the db_url parameter"
modparam("tcp_mgm", "db_url", "mysql://opensips:opensipsrw@localhost/opensips")
```


#### db_table (string) {#param_db_table}


The name of the table holding the TCP paths (rules).


Default value is *"tcp_mgm"*.


```c title="Setting the db_table parameter"
modparam("tcp_mgm", "db_table", "tcp_mgm")
```


#### [column-name]_col (string) {#param_column_name_col}


Use a different name for column *"column-name"*.


```c title="Setting the [column-name]_col parameter"
modparam("tcp_mgm", "connect_timeout_col", "connect_to")
```


### Exported MI Functions {#exported_mi_functions}


#### tcp_reload {#mi_tcp_reload}


Reload all TCP paths from the *tcp_mgm* table
		without disrupting ongoing traffic.  Note that the reloaded rules will
		NOT immediately apply to existing TCP connections, rather only to
		newly established ones.


Example:


```c
# reload all TCP paths
$ opensips-cli -x mi tcp_reload
$ "OK"
		
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 21 | 8 | 1281 | 62 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 4 | 5 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Apr 2022 - Jul 2022 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)).


*Documentation Copyrights:*


Copyright © 2022 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
