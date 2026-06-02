---
title: "freeswitch Module"
description: "The *\"freeswitch\"* module is a C driver for the FreeSWITCH Event Socket Layer interface. It can interact with one or more FreeSWITCH servers either by issuing commands to them, or by receiving events from them."
---

## Admin Guide {#sec-admin-guide}


### Overview {#overview}


The *"freeswitch"* module is a C driver for the
	FreeSWITCH Event Socket Layer interface. It can interact with one or more
	FreeSWITCH servers either by issuing commands to them, or by receiving
	events from them.


This driver can be seen as a centralized FreeSWITCH ESL connection manager.
	OpenSIPS modules may use its API in order to easily establish, reference
	and reuse ESL connections.


A FreeSWITCH ESL URL is of the form:
	**fs://[username]:password@host[:port]**.
	The default ESL port is 8021.


### External Libraries or Applications {#sec-external-dependencies}


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*


### Exported Parameters {#exported_parameters}


#### event_heartbeat_interval (integer) {#param_event_heartbeat_interval}


The expected interval between FreeSWITCH HEARTBEAT event arrivals.


*Default value is "1" (second).*


```c title="Setting the event_heartbeat_interval parameter"
...
modparam("freeswitch", "event_heartbeat_interval", 20)
...
```


#### esl_connect_timeout (integer) {#param_esl_connect_timeout}


The maximally allowed duration for the establishment of an ESL connection.


*Default value is "5000" (milliseconds).*


```c title="Setting the esl_connect_timeout parameter"
...
modparam("freeswitch", "esl_connect_timeout", 3000)
...
```


#### esl_cmd_timeout (integer) {#param_esl_cmd_timeout}


The maximally allowed duration for the execution of an ESL command.
		This interval does not include the connect duration.


*Default value is "5000" (milliseconds).*


```c title="Setting the esl_cmd_timeout parameter"
...
modparam("freeswitch", "esl_cmd_timeout", 3000)
...
```


#### esl_cmd_polling_itv (integer) {#param_esl_cmd_polling_itv}


The sleep interval used when polling for an ESL command response. Since the
		value of this parameter imposes a minimal duration for any ESL command,
		you should run OpenSIPS in debug mode in order to first determine an expected
		response time for an arbitrary ESL command, then tune this parameter accordingly.


*Default value is "1000" (microseconds).*


```c title="Setting the esl_cmd_polling_itv parameter"
...
modparam("freeswitch", "esl_cmd_polling_itv", 3000)
...
```


### Exported Functions {#exported_functions}


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 110 | 51 | 4134 | 1528 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 12 | 10 | 30 | 21 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 5 | 3 | 21 | 41 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 8 | 5 |
| 5. | rance | 3 | 2 | 3 | 0 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 8 | 8 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 2 | 2 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jan 2017 - May 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2017 - Jul 2021 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jan 2018 - Apr 2021 |
| 5. | rance | Oct 2020 - Mar 2021 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
