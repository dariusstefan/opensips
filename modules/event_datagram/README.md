---
title: "event_datagram Module"
description: "This is a module which provides a UNIX/UDP SOCKET transport layer implementation for the Event Interface."
---

## Admin Guide


### Overview {#overview}


This is a module which provides a UNIX/UDP SOCKET transport layer 
		implementation for the Event Interface.


### DATAGRAM events syntax


The event payload is formated as a JSON-RPC notification, with the event
		name as the *method* field and the event parameters as
		the *params* field.


### DATAGRAM socket syntax


There are two types of sockets used by this module, based on the
		sockets type. An UNIX socket should follow this syntax:
		*['unix:'] unix_socket_path*


An UDP socket should follow this syntax:
		*'udp:' address ':' port*


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *none*


### Exported Parameters {#exported_parameters}


No parameter exported by this module.


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Example


This is an example of an event raised by the pike module
			when it decides an ip should be blocked:


```c title="E_PIKE_BLOCKED event"
{
  "jsonrpc": "2.0",
  "method": "E_PIKE_BLOCKED",
  "params": {
    "ip": "192.168.2.11"
  }
}
```


```c title="UNIX socket"
unix:/tmp/opensips_event.sock
```


```c title="UDP socket"
udp:127.0.0.1:8081
```


## Frequently Asked Questions


**Q: Both UNIX and UDP type of socket can be
				used to notify the events?**


Yes, you can use the both types.


**Q: What is the maximum lenght of a datagram event?**


The maximum length of a datagram event is 65457 bytes.


**Q: Where can I find more about OpenSIPS?**


Take a look at [https://opensips.org/](https://opensips.org/).


**Q: Where can I post a question about this module?**


First at all check if your question was already answered on one of
			our mailing lists:

E-mails regarding any stable OpenSIPS release should be sent to 
			users@lists.opensips.org and e-mails regarding development versions
			should be sent to devel@lists.opensips.org.

If you want to keep the mail private, send it to 
			users@lists.opensips.org.


**Q: How can I report a bug?**


Please follow the guidelines provided at:
			[https://github.com/OpenSIPS/opensips/issues](https://github.com/OpenSIPS/opensips/issues).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 24 | 16 | 854 | 37 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 8 | 6 | 12 | 29 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 8 | 4 | 27 | 140 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 3 | 1 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 4 | 2 | 2 | 2 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Aug 2020 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Jul 2020 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | May 2011 - Sep 2019 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Apr 2019 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Nov 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).
