---
title: "Example Module"
description: "This module serves as an example of how to write a module in OpenSIPS. Its primary goal is to simplify the development of new modules for newcomers, providing a clear and accessible starting point."
---

## Admin Guide


### Overview {#overview}


This module serves as an example of how to write a module in OpenSIPS.
		Its primary goal is to simplify the development of new modules for
		newcomers, providing a clear and accessible starting point.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### default_str (string) {#param_default_str}


The default parameter used when the [example str](#func_example_str)
			function is called without any parameter.


*Default value is "" (empty sring).*


```c title="Set 'default_str' parameter"
...
modparam("example", "default_str", "TEST")
...
```


#### default_int (integer) {#param_default_int}


The default parameter used when the [example int](#func_example_int)
			function is called without any parameter.


*Default value is "0".*


```c title="Set 'default_int' parameter"
...
modparam("example", "default_int", -1)
...
```


### Exported Functions {#exported_functions}


#### example() {#func_example}


Function that simply prints a message to log, saying that it has been called.


This function can be used from any route.


```c title="example usage"
...
example();
...
```


#### example_str([string]) {#func_example_str}


Function that simply prints a message to log, saying that it has been called.
			If a parameter is passed, it is printed in the log, otherwise the value of
			[default str](#param_default_str) parameter is used.


Meaning of the parameters is as follows:


- *string (string, optional)* - parameter to be logged


This function can be used from any route.


```c title="example_str() usage"
...
example_str("test");
...
```


#### example_int([int]) {#func_example_int}


Function that simply prints a message to log, saying that it has been called.
			If a parameter is passed, it is printed in the log, otherwise the value of
			[default int](#param_default_int) parameter is used.


Meaning of the parameters is as follows:


- *int (integer, optional)* - parameter to be logged


This function can be used from any route.


```c title="example_int() usage"
...
example_int(10);
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 4 | 1 | 349 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jul 2024 - Jul 2024 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).


*Documentation Copyrights:*


Copyright © 2024 OpenSIPS Solutions;
