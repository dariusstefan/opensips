---
title: "sl Module"
description: "The SL module allows OpenSIPS to act as a stateless UA server and generate replies to SIP requests without keeping state. That is beneficial in many scenarios, in which you wish not to burden server's memory and scale well."
---

## Admin Guide


### Overview {#overview}


The SL module allows OpenSIPS to act as a stateless 
		UA server and generate replies to SIP requests without keeping 
		state. That is beneficial in many scenarios, in which you wish not to 
		burden server's memory and scale well.


The SL module needs to filter ACKs sent after a 
		local stateless reply to an INVITE was generated. To recognize such 
		ACKs, OpenSIPS adds a special "signature" in to-tags. This signature is 
		sought for in incoming ACKs, and if included, the ACKs are absorbed.


To speed up the filtering process, the module uses a timeout 
		mechanism. When a reply is sent, a timer is set. As time as the timeout 
		didn't hit, the incoming ACK requests will be checked using TO tag 
		value. Once the timer expires, all the ACK are let through - a long
		time passed till it sent a reply, so it does not expect any ACK that 
		have to be blocked.


The ACK filtering may fail in some rare cases. If you think these 
		matter to you, better use stateful processing (tm module) for INVITE 
		processing. Particularly, the problem happens when a UA sends an 
		INVITE which already has a to-tag in it (e.g., a re-INVITE)
		and OpenSIPS want to reply to it. Than, it will keep the current to-tag, 
		which will be mirrored in ACK. OpenSIPS will not see its signature and 
		forward the ACK downstream. Caused harm is not bad--just a useless 
		ACK is forwarded.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### enable_stats (integer) {#param_enable_stats}


If the module should generate and export statistics to the core
		manager. A zero value means disabled.


SL module provides statistics about how many replies were sent (
		splitted per code classes) and how many local ACKs were filtered out.


Default value is 1 (enabled).


```c title="enable_stats example"
modparam("sl", "enable_stats", 0)
```


### Exported Functions {#exported_functions}


#### sl_send_reply(code, reason) {#func_sl_send_reply}


For the current request, a reply is sent back having the given code 
		and text reason. The reply is sent stateless, totally independent of 
		the Transaction module and with no retransmission for the INVITE's 
		replies. 'code' and 'reason' can contain pseudo-variables that are
		replaced at runtime.


Meaning of the parameters is as follows:


- *code (int)* - Return code.
- *reason (string)* - Reason phrase.


This function can be used from REQUEST_ROUTE, ERROR_ROUTE.


```c title="sl_send_reply usage"
...
sl_send_reply(404, "Not found");
...
sl_send_reply($err.rcode, $err.rreason);
...
```


#### sl_reply_error() {#func_sl_reply_error}


Sends back an error reply describing the nature of the last internal 
		error.  Usually this function should be used after a script function 
		that returned an error code.


This function can be used from REQUEST_ROUTE.


```c title="sl_reply_error usage"
...
sl_reply_error();
...
```


### Exported Statistics


#### 1xx_replies {#stat_1xx_replies}


The number of 1xx_replies.


#### 2xx_replies {#stat_2xx_replies}


The number of 2xx_replies.


#### 3xx_replies {#stat_3xx_replies}


The number of 3xx_replies.


#### 4xx_replies {#stat_4xx_replies}


The number of 4xx_replies.


#### 5xx_replies {#stat_5xx_replies}


The number of 5xx_replies.


#### 6xx_replies {#stat_6xx_replies}


The number of 6xx_replies.


#### sent_replies {#stat_sent_replies}


The number of sent_replies.


#### sent_err_replies {#stat_sent_err_replies}


The number of sent_err_replies.


#### received_ACKs {#stat_received_ACKs}


The number of received_ACKs.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 74 | 54 | 876 | 711 |
| 2. | Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)) | 42 | 32 | 665 | 232 |
| 3. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 22 | 16 | 295 | 172 |
| 4. | Andrei Pelinescu-Onciul | 16 | 14 | 50 | 50 |
| 5. | Jan Janak ([@janakj](https://github.com/janakj)) | 12 | 8 | 355 | 21 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 11 | 9 | 28 | 60 |
| 7. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 7 | 5 | 12 | 12 |
| 8. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 7 | 5 | 12 | 11 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 4 | 30 | 83 |
| 10. | Elena-Ramona Modroiu | 3 | 2 | 65 | 0 |


**All remaining contributors**: Jeffrey Magder, Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Andreas Heise, Konstantin Bokarius, Anca Vamanu, Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Oct 2020 - Oct 2020 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Feb 2002 - Nov 2019 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Sep 2019 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jun 2018 |
| 7. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Nov 2014 - Nov 2014 |
| 8. | Anca Vamanu | Nov 2010 - Nov 2010 |
| 9. | Henning Westerholt ([@henningw](https://github.com/henningw)) | Aug 2007 - Jun 2008 |
| 10. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | Apr 2006 - Mar 2008 |


**All remaining contributors**: Konstantin Bokarius, Edson Gellert Schubert, Andreas Heise, Elena-Ramona Modroiu, Jeffrey Magder, Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)), Jan Janak ([@janakj](https://github.com/janakj)), Andrei Pelinescu-Onciul.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Elena-Ramona Modroiu, Jan Janak ([@janakj](https://github.com/janakj)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
