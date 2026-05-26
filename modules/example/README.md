---
title: "Example Module"
description: "This module serves as an example of how to write a module in OpenSIPS. Its primary goal is to simplify the development of new modules for newcomers, providing a clear and accessible starting point."
---

## Admin Guide


### Overview


This module serves as an example of how to write a module in OpenSIPS.
		Its primary goal is to simplify the development of new modules for
		newcomers, providing a clear and accessible starting point.


### Dependencies


#### OpenSIPS Modules


The following  modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters


#### `default_str` (string)


The default parameter used when the [example str](#func_example_str)
			function is called without any parameter.


*Default value is "" (empty sring).*


**Example: Set "default_str" parameter**


```opensips
...
modparam("example", "default_str", "TEST")
...
```


#### `default_int` (integer)


The default parameter used when the [example int](#func_example_int)
			function is called without any parameter.


*Default value is "0".*


**Example: Set "default_int" parameter**


```opensips
...
modparam("example", "default_int", -1)
...
```


### Exported Functions


#### `example()`


Function that simply prints a message to log, saying that it has been called.


This function can be used from any route.


**Example: `example` usage**


```
...
example();
...
```


#### `example_str([string])`


Function that simply prints a message to log, saying that it has been called.
			If a parameter is passed, it is printed in the log, otherwise the value of
			[default str](#param_default_str) parameter is used.


Meaning of the parameters is as follows:


- *string (string, optional)* - parameter to be logged


This function can be used from any route.


**Example: `example_str()` usage**


```
...
example_str("test");
...
```


#### `example_int([int])`


Function that simply prints a message to log, saying that it has been called.
			If a parameter is passed, it is printed in the log, otherwise the value of
			[default int](#param_default_int) parameter is used.


Meaning of the parameters is as follows:


- *int (integer, optional)* - parameter to be logged


This function can be used from any route.


**Example: `example_int()` usage**


```
...
example_int(10);
...
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 4 | 1 | 349 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jul 2024 - Jul 2024 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).


*Documentation Copyrights:*


Copyright © 2024 OpenSIPS Solutions;
