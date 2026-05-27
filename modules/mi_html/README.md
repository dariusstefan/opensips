---
title: "mi_html Module"
description: "This module provides a minimal web user interface for the OpenSIPS's Management Interface."
---

## Admin Guide


### Overview {#overview}


This module provides a minimal web user interface for the OpenSIPS's
		Management Interface.


Parameters for mi commands must be given in a json array format.
		For example, to get all statistics, the param is to be given as [["all"]].
		To get only dialog and tm statistics, the param is to be given as [["dialog:","tm:"]].


### To-do


Features to be added in the future:


- possibility to authenticate connections.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *httpd* module.


### Exported Parameters {#exported_parameters}


#### root(string) {#param_root}


Specifies the root path for the HTTP requests.
		The link to the mi web interface must be constructed
		using the following patern:
		http://[opensips_IP]:[opensips_mi_port]/[root]


*The default value is "mi".*


```opensips title="Set root parameter"
...
modparam("mi_html", "root", "opensips_mi")
...
```


#### http_method(integer) {#param_http_method}


Specifies the HTTP request method to be used:


- 0 - use GET HTTP request
- 1 - use POST HTTP request


*The default value is 0.*


```opensips title="Set http_method parameter"
...
modparam("mi_html", "http_method", 1)
...
```


#### trace_destination (string) {#param_trace_destination}


Trace destination as defined in the tracing module. Currently
		the only tracing module is **proto_hep**.
		This is where traced mi messages will go.


**WARNING:**A tracing module must be
			loaded in order for this parameter to work. (for example
			**proto_hep**).


*Default value is none(not defined).*


```opensips title="Set trace_destination parameter"
...
modparam("proto_hep", "trace_destination", "[hep_dest]10.0.0.2;transport=tcp;version=3")

modparam("mi_html", "trace_destination", "hep_dest")
...
```


#### trace_bwlist (string) {#param_trace_bwlist}


Filter traced mi commands based on a blacklist or a whitelist.
		**trace_destination** must be defined for
		this parameter to have any purpose. Whitelists can be defined using
		'w' or 'W', blacklists using 'b' or 'B'. The type is separate by the
		actual blacklist by ':'. The mi commands in the list must be separated
		by ','.


Defining a blacklists means all the commands that are not blacklisted
			will be traced. Defining a whitelist means all the commands that are
			not whitelisted will not be traced.
			**WARNING:** One can't define both
			a whitelist and a blacklist. Only one of them is allowed. Defining
			the parameter a second time will just overwrite the first one.


**WARNING:**A tracing module must be
			loaded in order for this parameter to work. (for example
			**proto_hep)**.


*Default value is none(not defined).*


```opensips title="Set trace_destination parameter"
...
## blacklist ps and which mi commands
## all the other commands shall be traced
modparam("mi_html", "trace_bwlist", "b: ps, which")
...
## allow only sip_trace mi command
## all the other commands will not be traced
modparam("mi_html", "trace_bwlist", "w: sip_trace")
...
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Known issues


Commands with large responses (like ul_dump) will fail if the
		configured size of the httpd buffer is to small (or if there isn't
		enough pkg memory configured).


Future realeases of the httpd module will address this issue.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 63 | 32 | 2249 | 697 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 14 | 3 | 167 | 493 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 10 | 8 | 31 | 44 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 10 | 8 | 27 | 18 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 8 | 6 | 109 | 44 |
| 6. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 8 | 5 | 217 | 10 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 3 | 4 |
| 8. | Zero King ([@l2dy](https://github.com/l2dy)) | 3 | 1 | 2 | 2 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 2 | 1 | 0 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jul 2014 - Mar 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Oct 2020 - Feb 2023 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2015 - Jul 2020 |
| 4. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Oct 2011 - Mar 2020 |
| 5. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Dec 2011 - Apr 2019 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 9. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Jan 2017 - Feb 2017 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jan 2016 - Jan 2016 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)).


*Documentation Copyrights:*


Copyright © 2011-2013 [VoIP Embedded, Inc.](http://www.voipembedded.com)
