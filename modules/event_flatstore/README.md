---
title: "event_flatstore Module"
description: "The *event_flatstore* module provides a logging facility for different events, triggered through the OpenSIPS Event Interface, directly from the OpenSIPS script. The module logs the events along with their parameters in plain text files."
---

## Admin Guide


### Overview {#overview}


The *event_flatstore*
		module provides a logging facility for different events,
		triggered through the OpenSIPS Event Interface, directly from the OpenSIPS
		script. The module logs the events along with their parameters in plain
		text files.


### Flatstore socket syntax


*flatstore:path_to_file*


Meanings:


- *flatstore:* - informs the Event Interface that the
					events sent to this subscriber should be handled by the
					*event_flatstore* module.
- *path_to_file* - path to the file where the logged events will be appended to. The file will be created if it does not exist. It must be a valid path and not a directory.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *none*


### Exported Parameters {#exported_parameters}


#### max_open_sockets (integer) {#param_max_open_sockets}


Defines the maximum number of simultaneously opened files by the
			module. If the maximum limit is reached, an error message will be
			thrown, and further subscriptions will only be possible after at
			least one of the current subscriptions will expire.


*Default value is "100".*


**Example: Set max_open_sockets parameter**


```opensips
...
modparam("event_flatstore", "max_open_sockets", 200)
...
```


#### delimiter (string) {#param_delimiter}


Sets the separator between the parameters of the event in the logging file.


*Default value is ",".*


**Example: Set delimiter parameter**


```opensips
...
modparam("event_flatstore", "delimiter", ";")
...
```


#### file_permissions (string) {#param_file_permissions}


Sets the permissions for the newly created logs. It
			expects a string representation of a octal value.


*Default value is "644".*


**Example: Set file_permissions parameter**


```opensips
...
modparam("event_flatstore", "file_permissions", "664")
...
```


#### suppress_event_name (int) {#param_suppress_event_name}


Suppresses the name of the event in the log file.


*Default value is "0/OFF" (the event's name is printed).*


**Example: Set suppress_event_name parameter**


```opensips
...
modparam("event_flatstore", "suppress_event_name", 1)
...
```


### Exported Functions {#exported_functions}


No exported functions to be used in the configuration file.


### Exported MI Functions {#exported_mi_functions}


#### evi_flat_rotate {#mi_evi_flat_rotate}


It makes the processes reopen the file specified as a parameter to the command in order to be compatible with a logrotate command. If the function is not called after the mv command is executed, the module will continue to write in the renamed file.


Name: *evi_flat_rotate*


Parameters: *path_to_file*


MI FIFO Command Format:


```
		opensips-cli -x mi evi_flat_rotate _path_to_log_file_
		
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 25 | 16 | 482 | 303 |
| 2. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | 23 | 13 | 770 | 135 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 12 | 9 | 31 | 62 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 9 | 7 | 36 | 5 |
| 5. | Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)) | 7 | 3 | 254 | 9 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 7 | 4 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 2 | 1 | 1 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jan 2016 - Dec 2021 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Jun 2015 - Jul 2020 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2018 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2017 - Feb 2017 |
| 7. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | Jun 2015 - Jul 2015 |
| 8. | Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)) | Jun 2015 - Jul 2015 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)).


*Documentation Copyrights:*


Copyright © 2015 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
