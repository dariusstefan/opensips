---
title: "presence_reginfo Module"
description: "The module enables the handling of \"Event: reg\" (as defined in RFC 3680) inside of the presence module. This can be used distribute the registration-info status to the subscribed watchers."
---

## Admin Guide


### Overview


The module enables the handling of "Event: reg" (as defined 
	      in RFC 3680) inside of the presence module. This can be used
	      distribute the registration-info status to the subscribed watchers.


The module does not currently implement any authorization
	      rules.  It assumes that publish requests are only issued by
	      an authorized application and subscribe requests only by
	      authorized users.  Authorization can thus be easily done in 
	      OpenSIPS configuration file before calling handle_publish() 
	      and handle_subscribe() functions.


Note: This module only activates the processing of the "reg" 
	      in the presence module. To send dialog-info to watchers you also 
	      need a source which PUBLISH the reg info to the presence module.
	      For example you can use the pua_reginfo module or any external
	      component. This approach allows to have the presence server and the
	      reg-info aware publisher (e.g. the main proxy) on different 
	      OpenSIPS instances.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *presence*.


#### External Libraries or Applications


None.


### Parameters


#### `default_expires` (int)


The default expires value used when missing from SUBSCRIBE
               message (in seconds).


*Default value is "3600".*


**Example: Set `default_expires` parameter**


```opensips
        ...
        modparam("presence_reginfo", "default_expires", 3600)
        ...
        
```


#### `aggregate_presentities` (int)


Whether to aggregate in a single notify body all registration 
							presentities. Useful to have all registrations on first NOTIFY
							following initial SUBSCRIBE.


*Default value is "0" (disabled).*


**Example: Set `aggregate_presentities` parameter**


```opensips
					...
					modparam("presence_reginfo", "aggregate_presentities", 1)
					...
					
```


### Functions


None to be used in configuration file.


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Carsten Bock | 8 | 1 | 771 | 0 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 14 | 17 |
| 3. | Ken Rice | 3 | 1 | 2 | 2 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 2 | 1 | 0 | 2 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | May 2026 - May 2026 |
| 2. | Ken Rice | Sep 2025 - Sep 2025 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2024 - Apr 2024 |
| 4. | Carsten Bock | Mar 2024 - Mar 2024 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Carsten Bock.


*Documentation Copyrights:*


Copyright © 2011-2023 Carsten Bock, carsten@ng-voice.com, http://www.ng-voice.com
