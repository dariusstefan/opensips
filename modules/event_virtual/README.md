---
title: "event_virtual Module"
description: "The *event_virtual* module provides the possibility to have multiple external applications, using different transport protocols, subscribed to the OpenSIPS Event Interface as a single virtual subscriber, for a specific event. When an event is triggered, the event_virtual module notifies the spe..."
---

## Admin Guide


### Overview {#overview}


The *event_virtual*
		module provides the possibility to have multiple external applications, using different transport protocols, subscribed to the OpenSIPS Event Interface as a single virtual subscriber, for a specific event. When an event is triggered, the event_virtual module notifies the specified transport modules using one of the following policies:


- *PARALLEL* - all subscribers (applications) are notified at once
- *FAILOVER* - for every event raised, try to
				notify the subscribers, in the order in which they are given,
				until the first successful notification. A failed subscriber is
				skipped for further notifications until the
				[failover timeout](#param_failover_timeout) passes.
- *ROUND-ROBIN* - for every event raised, notify the subscribers alternatively, in the order in which they are given (for each raised event notify a different subscriber)


Only one expire value can be used (for the whole virtual subscription), and not one for each individual subscriber.


### Virtual socket syntax


*virtual:policy subscriber_1 [[subscriber_2] ...]*


Meanings:


- *virtual:* - informs the Event Interface that the
					events sent to this subscriber should be handled by the
					*event_virtual* module
- *policy* - subscriber notification policy, can have one of the following values: 'PARALLEL', 'FAILOVER', 'ROUND-ROBIN' (with the behaviour described above)
				
				
					*!! Important: Policies must always be specified as
							uppercase strings!*
- *subscriber_1* - use the socket syntax for this specific subscriber (eg. "rabbitmq:guest:guest@127.0.0.1:5672/pike")


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:
			*The OpenSIPS event modules which implement the transport protocols used by the subscribers*.


### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *none*


### Exported Parameters {#exported_parameters}


#### failover_timeout (integer) {#param_failover_timeout}


The minimum duration in seconds that a failed subscriber is
			skipped for further notifications. This parameter only affects
			the *FAILOVER* policy.


*Default value is "30".*


```c title="Setting the failover_timeout parameter"
...
modparam("event_virtual", "failover_timeout", 5)
...
	
```


### Exported Functions {#exported_functions}


No exported functions to be used in the configuration file.


### Example


The sockets of the subscribers may be separated by any number of spaces or tabs:


```c title="Virtual socket"
	virtual:PARALLEL rabbitmq:guest:guest@127.0.0.1:5672/pike flatstore:/var/log/opensips_proxy.log
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 22 | 10 | 1057 | 125 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 8 | 6 | 39 | 36 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 6 | 4 | 4 | 2 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 4 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 3 | 1 | 3 | 2 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | May 2016 - Dec 2021 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Jul 2015 - Jul 2020 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Sep 2019 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2019 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
