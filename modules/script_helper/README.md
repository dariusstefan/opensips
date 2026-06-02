---
title: "Script Helper Module"
description: "The purpose of the **Script Helper module** is to simplify the scripting process in OpenSIPS when doing basic scenarios. At the same time, it is useful to script writers as it contains basic SIP routing logic, and thus it allows them to focus more on the particular aspects of their OpenSIPS r..."
---

## Admin Guide


### Overview {#overview}


The purpose of the **Script Helper module**
	is to simplify the scripting process in OpenSIPS when doing basic scenarios.
	At the same time, it is useful to script writers as it contains basic SIP
	routing logic, and thus it allows them to focus more on the particular aspects
	of their OpenSIPS routing code.


### How it works


By simply loading the module, the following
	**default logic** will be embedded:


- for initial SIP requests, the module will perform *record routing*
	before running the main *request* route
- sequential SIP requests will be transparently handled - the module will perform
	*loose routing*, and the request route will not be run at all


Currently, the module may be further configured to embed the following
	**optional logic**:


- *dialog* support (dialog module dependency - must be loaded before this module)
- an additional route to be run before relaying sequential requests


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *dialog* (only if **[use dialog](#param_use_dialog)** is enabled).


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### use_dialog (integer) {#param_use_dialog}


Enables dialog support. Note that the dialog module must be loaded before
		this module when setting this parameter.


Default value is 0 (disabled)


```c title="Setting use_dialog"
...
modparam("script_helper", "use_dialog", 1)
...
```


#### create_dialog_flags (string) {#param_create_dialog_flags}


Flags used when creating dialogs. For details on these flags, please refer
		to the *create_dialog()* function of the dialog module.


Default value is "" (no flags are set)


```c title="Setting create_dialog_flags"
...
modparam("script_helper", "create_dialog_flags", "PpB")
...
```


#### sequential_route (string) {#param_sequential_route}


Optional route to be run just before sequential requests are relayed.
		If the *exit* script statement is used inside this route,
		the module assumes that the relaying logic has been handled.


By default, this parameter is not set


```c title="Setting sequential_route"
...
modparam("script_helper", "sequential_route", "sequential_handling")
...
route [sequential_handling]
{
...
}
...
```


### Known Issues


The Max-Forwards header is currently not handled at all.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 19 | 13 | 499 | 46 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 7 | 5 | 13 | 10 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 7 | 5 | 10 | 8 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 4 | 9 | 13 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 3 | 4 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2023 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - May 2023 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
