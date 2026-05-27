---
title: "event_route Module"
description: "This module provides a simple way for capturing and handling directly in the OpenSIPS script of different events triggered through the OpenSIPS Event Interface"
---

## Admin Guide


### Overview {#overview}


This module provides a simple way for capturing and handling 
		directly in the OpenSIPS script of different events triggered through
		the OpenSIPS Event Interface


If you want to capture and handle a certian event, you need to
		define a dedicated route (*event_route*) into the
		OpenSIPS script, route having as name the name/code of the
		desired event. The route is triggered (and executed) by
		the module when the corresponding event is raised by the OpenSIPS


NOTE that the triggered *event_route* is
		run asyncronus (and in a different process) in regards to the code or 
		process that generated the actual event.


NOTE that inside the *event_route* you should
		NOT rely on anything more than the content provide by the event itself
		(see below variable). DO NOT assume to have access to any other
		variable or context, not even to a SIP message.


### ROUTE events parameters {#event-route-parameters}


In order to retrieve the parameters of an event, the
		*$param(name)* variable has to be used. It's
			name can be the parameter's name, or, if an integer is specified, its
			index inside the parameter's list.

		Example:


```opensips
xlog("first parameters is $param(1)\n");
xlog("Pike Blocking IP is $param(ip)\n");
```


*NOTE:* An event may be triggered within a different event, leading
		to nested processing. This function will retrieve the parameters of the currently processed
		event.


The event name can contain any non-quoted string character, but
		it is recommended to follow the syntax:
		E_*MODULE_NAME*_*EXTRA_NAME*


### EVENT_ROUTE usage


In order to handle the *E_PIKE_BLOCKED* event,
			the following snippet can be used:

			EVENT_ROUTE usage
					
```c

	event_route[E_PIKE_BLOCKED] {
		xlog("IP $param(ip) has been blocked\n");
	}
```


### EVENT_ROUTE socket syntax


As the OpenSIPS Event Interface requires, the *event_route*
		module uses a specific socket syntax:
		*'route:' event_name*


Example:
		*route:E_PIKE_BLOCKED*


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *none*


### Exported Parameters {#exported_parameters}


The module does not export parameters to be used in configuration script.


### Exported Functions {#exported_functions}


The function does not export any function.


## Frequently Asked Questions


**Q: Can I declare more routes for handling the same event?**


No, only a single *event_route* can be used for a
			particular event.


**Q: What happened with the "fetch_event_params()" function?**


This function has been dropped starting with OpenSIPS 3.0. Its functionality
			has been replaced by the "$param(name)" variable.


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
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 39 | 24 | 992 | 316 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 16 | 8 | 106 | 376 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 13 | 11 | 27 | 47 |
| 4. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 8 | 5 | 138 | 51 |
| 5. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 7 | 2 | 384 | 9 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 5 | 3 | 5 | 2 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 15 | 12 |
| 8. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | 3 | 1 | 5 | 6 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 2 | 1 | 1 | 0 |


**All remaining contributors**: Walter Doekes ([@wdoekes](https://github.com/wdoekes)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Mar 2023 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Dec 2012 - Mar 2023 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Nov 2021 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2021 - Feb 2021 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Dec 2020 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jun 2016 - Jun 2016 |
| 8. | Ionel Cerghit ([@ionel-cerghit](https://github.com/ionel-cerghit)) | Jun 2015 - Jun 2015 |
| 9. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Oct 2014 - Oct 2014 |
| 10. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jul 2014 - Jul 2014 |


**All remaining contributors**: Walter Doekes ([@wdoekes](https://github.com/wdoekes)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)).


*Documentation Copyrights:*


Copyright © 2012 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
