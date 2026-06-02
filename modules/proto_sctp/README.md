---
title: "proto_sctp Module"
description: "The **proto_sctp** module is an optional transport module (shared library) which exports the required logic in order to handle SCTP-based communication. (socket initialization and send/recv primitives to be used by higher-level network layers)"
---

## Admin Guide


### Overview {#overview}


The **proto_sctp** module is an optional transport module (shared library) which
	exports the required logic in order to handle SCTP-based communication. (socket initialization
	and send/recv primitives to be used by higher-level network layers)


Once loaded, you will be able to define *"sctp:"* listeners in your script.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *None*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### sctp_port (integer) {#param_sctp_port}


The default port to be used for all SCTP related operation. Be careful
		as the default port impacts both the SIP listening part (if no port is
		defined in the SCTP listeners) and the SIP sending part (if the 
		destination SCTP URI has no explicit port).


If you want to change only the listening port for STP, use the port
		option in the SIP listener defintion.


*Default value is 5060.*


```c title="Set sctp_port parameter"
...
modparam("proto_sctp", "sctp_port", 5070)
...
```


## Frequently Asked Questions


**Q: After switching to OpenSIPS 2.1, I'm getting this error:
				"listeners found for protocol sctp, but no module can handle it"**


You need to load the "proto_sctp" module. In your script, make sure
			you do a **loadmodule "proto_sctp.so"**
			after setting the **[mpath](https://opensips.org/Documentation/Script-CoreParameters-2-1#toc74)**.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 11 | 5 | 448 | 73 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 10 | 8 | 11 | 18 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 7 | 4 | 153 | 25 |
| 4. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 3 | 1 | 1 | 1 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 6. | Zero King ([@l2dy](https://github.com/l2dy)) | 3 | 1 | 1 | 1 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 2 | 1 | 1 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Feb 2015 - Apr 2021 |
| 2. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 4. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2015 - Jun 2018 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2017 |
| 7. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Feb 2016 - Feb 2016 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Zero King ([@l2dy](https://github.com/l2dy)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
