---
title: "Status/Reports Module"
description: "The Status/Report module is a wrapper over the internal status/report framework, allowing the script writer to dynamically define and use of SR groups."
---

## Admin Guide


### Overview {#overview}


The Status/Report module is a wrapper over the 
		internal status/report framework, allowing the script writer to 
		dynamically define and use of SR groups.


By bringing the Status/Report support into the script, it opens the
		possibility to create custom reports from script, depending on
		the logic you have there.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### script_sr_group (string) {#param_script_sr_group}


Name of a new Status/Report group to be created and later used
		from script level.


This parameter may be defined multiple times, in order to define
		multiple groups.


```c title="script_sr_group example"
modparam("status_report", "script_sr_group", "security")
modparam("status_report", "script_sr_group", "alarms")
```


### Exported Functions {#exported_functions}


#### sr_set_status( group, status, [details]) {#func_sr_set_status}


Sets a new status (and details) for a Status/Report group.


Meaning of the parameters is as follows:


- *group* (string) - the name of the
			SR group; you can change the status only for the groups defined via
			this module (as parameter).
- *status* (int) - the new status value
			( strict positive meaning OK, strict negative meaning NOT OK,
			0 is not accepts, it is converted to 1 automatically).
- *details* (string, optional) - a
			descripting text to detail the status value


This function can be used from any route.


```c title="sr_set_status usage"
...
sr_set_status( "script_caching", 1, "completed");
...
```


#### sr_add_report( group, report) {#func_sr_add_report}


Adds a new report/log to a Status/Report group.This must have been
		defined via this module too.


Meaning of the parameters is as follows:


- *group* (string) - the name of the
			SR group; you can change the status only for the groups defined via
			this module (as parameter).
*report* (string) - the log to be added.


This function can be used from any route.


```c title="sr_add_report usage"
...
sr_add_report("security","IP $si detected as attacker");
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 7 | 3 | 339 | 7 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 5 | 3 | 8 | 6 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 2 | 3 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | May 2024 - May 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Feb 2022 - Feb 2022 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)).
