---
title: "event_xmlrpc Module"
description: "This module is an implementation of an XMLRPC client used to notify XMLRPC servers whenever certain notifications are raised by OpenSIPS. It acts as a transport layer for the Event Notification Interface."
---

## Admin Guide


### Overview


This module is an implementation of an XMLRPC client used to notify
		XMLRPC servers whenever certain notifications are raised by OpenSIPS. It
		acts as a transport layer for the Event Notification Interface.


Basicly, the module executes a remote procedure call when an event is
		raised from OpenSIPS's script, core or modules using the Event
		Interface.


In order to be notified, an XMLRPC server has to subscribe for a certain
		event provided by OpenSIPS. This can be done using the generic MI
		Interface (*event_subscribe* function) or from
		OpenSIPS script (*subscribe_event* core function).


### XMLRPC socket syntax


*'xmlrpc:' host ':' port ':' method*


Meanings:


- *'xmlrpc:'* - informs the Event Interface
					that the events sent to this subscriber should be handled
					by the *event_xmlrpc* module.
- *host* - host name of the XMLRPC server.
- *port* - port of the XMLRPC server.
- *method* - method called remotely by the
					XMLRPC client.
					NOTE: the client does not wait for a response from the
						XMLRPC server.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *none*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *none*


### Exported Parameters


#### `use_struct_param` (integer)


When raising an event, pack the name and value of the
			parameters in a XMLRPC structure. This provides an easier
			way for some XMLRPC server implementations to interpret
			the parameters.
			Set it to zero to disable or to non-zero to enable it.


*Default value is "0 (disabled)".*


**Example: Set `use_struct_param` parameter**


```opensips
...
modparam("event_xmlrpc", "use_struct_param", 1)
...
```


### Exported Functions


No function exported to be used from configuration file.


### Example


This is an example of an event raised by the pike module
			when it decides an ip should be blocked:


**Example: E_PIKE_BLOCKED event**


```
POST /RPC2 HTTP/1.1.
Host: 127.0.0.1:8081.
Connection: close.
User-Agent: OpenSIPS XMLRPC Notifier.
Content-type: text/xml.
Content-length: 240.
		.
<?xml version="1.0"?>
<methodCall>
	<methodName>e_dummy_h</methodName>
	<params>
		<param>
			<value><string>E_MY_EVENT</string></value>
		</param>
		<param>
			<name>ip</name>
			<value><string>192.168.2.11</string></value>
		</param>
	</params>
</methodCall>
```


**Example: XMLRPC socket**


```
	# calls the 'block_ip' function
	xmlrpc:127.0.0.1:8080:block_ip
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 37 | 23 | 1239 | 131 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 10 | 8 | 63 | 39 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 10 | 6 | 208 | 65 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 5 | 3 | 8 | 9 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 4 | 4 |
| 6. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 3 | 1 | 103 | 28 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 8. | Ryan Bullock ([@rrb3942](https://github.com/rrb3942)) | 2 | 1 | 8 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Oct 2013 - Jun 2023 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Jul 2015 - Jun 2023 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2017 - Feb 2023 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | May 2012 - Jan 2020 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Jan 2016 - Jan 2016 |
| 8. | Ryan Bullock ([@rrb3942](https://github.com/rrb3942)) | Jan 2013 - Jan 2013 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).


*Documentation Copyrights:*


Copyright © 2012 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
