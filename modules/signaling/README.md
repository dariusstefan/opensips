---
title: "signaling Module"
description: "The SIGNALING module comes as a wrapper over tm and sl modules and offers one function to be called by the modules that want to send a reply."
---

## Admin Guide


### Overview {#overview}


The SIGNALING module comes as a wrapper over 
		tm and sl modules and offers one function to be called by the modules
		that want to send a reply.


The logic behind the module is to first search if a transaction is
		created and if so, send a state full reply, using tm module, otherwise
		send a stateless reply with the function exported by sl.
		In this way, the script writer still has the call on how the transaction 
		should be handled, state full or stateless and the reply is send 
		accordingly to his choice.


For example, if you do a t_newtran() in the script before doing save() 
		(for registration), the function will automatically send the reply in 
		stateful mode as a transaction is available. If no transaction is done, 
		the reply will be sent in stateless way (as now).


By doing this, we have the possibility to have same module sending 
		either stateful either stateless replies, by just controlling this from 
		the script (if we create or not a transaction).
		So, the signalling will be more coherent as the replies will be sent 
		according to the transaction presence (or not).


Moreover, this module offers the possibility of loading only one 
	of the module, sl or tm, and send reply using only the module that is 
	loaded. This is useful as not in all cases a user desires to send 
	stateful or stateless replies and he should not be forced to load the 
	module only because the send reply interface requires it.


### Dependencies {#dependencies}


#### OpenSIPS Modules


At least one of the following modules must be loaded before this module:


- *sl*.
- *tm*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


- *None*.


### Exported Functions {#exported_functions}


#### send_reply(code, reason) {#func_send_reply}


For the current request, a reply is sent back having the given code 
		and text reason. The reply is sent stateless or statefull depending 
		on which module is loaded and if a transaction was created, as 
		explained above.


Meaning of the parameters is as follows:


- *code (int)* - Return code.
- *reason (string)* - Reason phrase.


This function can be used from REQUEST_ROUTE, ERROR_ROUTE.


```c title="sl_send_reply usage"
...
send_reply(404, "Not found");
...
send_reply($err.rcode, $err.rreason);
...
		
```


### Exported Variables {#exported_pseudo_variables}


#### $sig_local_totag {#pv_sig_local_totag}


This variable returns the local To-tag that will be used
		by OpenSIPS for locally sending replies to the current SIP request.
		Yes, this variable should be used only in the context of a SIP
		request and it should be used only in conjunction with the 
		using [send reply](#func_send_reply).


Whenever you use it, be sure that the function is used in the same
		stateful / stateless SIP mode as the following replying function.
		Otherwise you may get different values for the To-tag!!


NOTE: the variable returns the To-Tag that will be used by OpenSIPS
		in the locally generated reply. This may be completly different from
		the To-tag in the replies received and forwarded by OpenSIPS.


```opensips title="Usage of $sig_local_totag variable"
...
# stateful handling
t_newtran();
xlog("the To-tag to be used is $sig_local_totag \n");
send_reply();  # or t_reply();
...
# stateless handling
xlog("the To-tag to be used is $sig_local_totag \n");
send_reply(); # or sl_send_reply();
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 12 | 9 | 131 | 23 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 11 | 9 | 28 | 35 |
| 3. | Anca Vamanu | 9 | 3 | 524 | 2 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 7 | 5 | 6 | 4 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 3 | 23 | 76 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 7 | 5 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 8. | zhangst | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Dec 2020 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Oct 2020 - Oct 2020 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Nov 2008 - May 2020 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jun 2018 |
| 7. | zhangst | Jul 2014 - Jul 2014 |
| 8. | Anca Vamanu | Nov 2008 - Mar 2010 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), zhangst, Anca Vamanu.


*Documentation Copyrights:*


Copyright © 2008 FhG FOKUS
