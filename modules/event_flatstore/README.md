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


```c title="Set max_open_sockets parameter"
...
modparam("event_flatstore", "max_open_sockets", 200)
...
```


#### delimiter (string) {#param_delimiter}


Sets the separator between the parameters of the event in the logging file.


*Default value is ",".*


```c title="Set delimiter parameter"
...
modparam("event_flatstore", "delimiter", ";")
...
```


#### escape_delimiter (string) {#param_escape_delimiter}


Optional replacement sequence that will be written *instead
			of* the [`delimiter`](#param_delimiter)
			whenever this character (or sequence) occurs inside a string
			parameter.
			This allows you to keep the log file parse-friendly even when user
			data itself may contain delimiter symbols.


If set, its length *must be exactly equal* to the
			length of `delimiter`.


*Default value is """" (escaping disabled).*


```c title="Enable escaping of ',' with '|'"
...
modparam("event_flatstore", "delimiter", ",")
modparam("event_flatstore", "escape_delimiter", "|")
...
	
```


#### file_permissions (string) {#param_file_permissions}


Sets the permissions for the newly created logs. It
			expects a string representation of a octal value.


*Default value is "644".*


```c title="Set file_permissions parameter"
...
modparam("event_flatstore", "file_permissions", "664")
...
```


#### suppress_event_name (int) {#param_suppress_event_name}


Suppresses the name of the event in the log file.


*Default value is "0/OFF" (the event's name is printed).*


```c title="Set suppress_event_name parameter"
...
modparam("event_flatstore", "suppress_event_name", 1)
...
```


#### rotate_period (int) {#param_rotate_period}


When used, it triggers a file auto-rotate. The period is matched
			against the absolute time of the machine, can be useful to trigger
			auto-rotate every minute, or every hour.


*Default value is "0/OFF" (the file is never auto-rotated)*


```c title="Set rotate_period parameter"
...
modparam("event_flatstore", "rotate_period", 60) # rotate every minute
modparam("event_flatstore", "rotate_period", 3660) # rotate every hour
...
```


#### rotate_count (int|string) {#param_rotate_count}


Defines after how many written lines the log file is rotated.
			The value may exceed the 32-bit integer limit; in that case pass
			it *as a string*, e.g. "5000000000".


*Default value is "0/OFF".*


```c title="Rotate after five billion lines"
...
modparam("event_flatstore", "rotate_count", "5000000000")
...
		
```


#### rotate_size (int|string) {#param_rotate_size}


Sets the maximum size of a file before it is rotated.  A size
		suffix of "k", "m" or "g"
		(multiples of 1024) may be provided.
		Very large values can be supplied as strings, e.g.
		"8589934592" for 8 GiB.


*Default value is "0/OFF".*


```c title="Rotate at 2 GiB"
...
modparam("event_flatstore", "rotate_size", "2g")
...
```


#### suffix (string) {#param_suffix}


Modifies the file that OpenSIPS writes events into by
			appending a suffix to the the file specified in the flatstore
			*socket*.


The suffix can contain string formats (i.e. variables mixed with
			strings). The path of the resulted file is evaluated when the first
			event is raised/written in the file after a reload happend, or when
			the *rotate_period*, if specified, triggers a rotate.


This parameter does not affect the matching of the event socket -
			the matching will be done exclusively using the flatstore
			*socket* registered.


*Default value is """" (no suffix is added)*


```c title="Set suffix parameter"
...
modparam("event_flatstore", "suffix", "$time(%Y)")
...
```


### Exported Functions {#exported_functions}


No exported functions to be used in the configuration file.


### Exported MI Functions {#exported_mi_functions}


#### event_flatstore:rotate {#mi_rotate}


Replaces obsolete MI command: *evi_flat_rotate*.


It makes the processes reopen the file specified as a parameter to the command in order to be compatible with a logrotate command. If the function is not called after the mv command is executed, the module will continue to write in the renamed file.


Name: *event_flatstore:rotate*


Parameters: *path_to_file*


MI FIFO Command Format:


```c
opensips-cli -x mi event_flatstore:rotate _path_to_log_file_
		
```


### Exported Events {#exported_events}


#### E_FLATSTORE_ROTATION {#event_E_FLATSTORE_ROTATION}


The event is raised every time *event_flatstore*
		opens a new log file (manual `event_flatstore:rotate`,
		auto-rotate by `rotate_period`, or
		thresholds `rotate_count`/`rotate_size`).
		External apps can subscribe to monitor log-rotation activity.


Parameters:


- *timestamp* – Unix epoch (seconds) when the
		rotation was performed.
- *reason* – one of the strings
		*count*, *size*,
		*period* or *mi*.
- *filename* – full path of the new log file.
- *old_filename* – full path of the previous
		log file, or empty string if none existed.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 27 | 16 | 482 | 303 |
| 2. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | 23 | 13 | 770 | 135 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 12 | 10 | 48 | 13 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 12 | 9 | 31 | 62 |
| 5. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 8 | 1 | 612 | 8 |
| 6. | Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)) | 7 | 3 | 254 | 9 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 7 | 4 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 4 | 4 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Mar 2026 |
| 2. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | May 2025 - May 2025 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2017 - Feb 2023 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jan 2016 - Dec 2021 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Jun 2015 - Jul 2020 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2018 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | Jun 2015 - Jul 2015 |
| 9. | Eseanu Marius Cristian ([@eseanucristian](https://github.com/eseanucristian)) | Jun 2015 - Jul 2015 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Nick Altmann ([@nikbyte](https://github.com/nikbyte)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
