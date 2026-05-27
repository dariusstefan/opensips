---
title: "MSRP Gateway Module"
description: "This module implements a Gateway for translating between Page Mode (SIP MESSAGE method) and Session Mode (MSRP) Instant Messaging."
---

## Admin Guide


### Overview {#overview}


This module implements a Gateway for translating between Page Mode
		(SIP MESSAGE method) and Session Mode (MSRP) Instant Messaging.


The module makes use of the *msrp_ua* module's API for
    	the MSRP UAC/UAS functionalities.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *tm*
- *msrp_ua*


#### External Libraries or Applications


The following libraries or applications must be installed 
			before running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### hash_size (int) {#param_hash_size}


The size of the hash table that stores the gateway session
				information. It is the 2 logarithmic value of the real size.


*Default value is "10"*
			 (1024 records).


```opensips title="Set hash_size parameter"
...
modparam("msrp_gateway", "hash_size", 16)
...
		
```


#### cleanup_interval (int) {#param_cleanup_interval}


The interval between full iterations of the sessions table
			in order to clean up lingering sessions.


*Default value is "60". (seconds)*


```opensips title="Set cleanup_interval parameter"
...
modparam("msrp_gateway", "cleanup_interval", 60)
...
		
```


#### session_timeout (int) {#param_session_timeout}


Amount of time (in seconds) since last message has been received
			from either side, after which a session should be terminated.


*The default value is 12 * 3600 seconds (12 hours).*


```opensips title="Set session_timeout parameter"
...
modparam("msrp_gateway", "session_timeout", 7200)
...
		
```


#### message_timeout (int) {#param_message_timeout}


Amount of time (in seconds) since last MESSAGE has been received
			after which a session should be terminated.


*The default value is 2 * 3600 seconds (2 hours).*


```opensips title="Set message_timeout parameter"
...
modparam("msrp_gateway", "message_timeout", 3600)
...
		
```


### Exported Functions {#exported_functions}


#### msrp_gw_answer(key, content_types, from, to, ruri) {#func_msrp_gw_answer}


This functions initializes a new gateway session by answering an initial
			INVITE from the MSRP side SIP session. After running this function the
			call will be completely handled by the MSRP UA engine and MSRP SEND
			requests will be automatically translated to SIP MESSAGE requests.


The SIP From, To, and RURI coordinates for building MESSAGE requests
			are passed as parameters to the function.


Parameters:


- *key* (string) - gateway session key to be used
				to correlate the MESSAGE requests with the MSRP side SIP session.
				A simple example would be to build this key based on the From and To
				URIs from both sides(from the initial MSRP leg INVITE and SIP MESSAGE
				requests respectively).
- *content_types* (string) - content types
				adevertised in the SDP offer on the MSRP side SIP session.
- *from* (string) - From URI to be used for building
				SIP MESSAGE requests.
- *to* (string) - To URI to be used for building
				SIP MESSAGE requests.
- *ruri* (string) - Request-URI to be used for building
				SIP MESSAGE requests.


This function can be used only from a request route.


```opensips title="msrp_gw_answer() usage"
...
if (!has_totag() && is_method("INVITE")) {
	msrp_gw_answer($var(corr_key), "text/plain", $fu, $tu, $ru);
	exit;
}
...
```


#### msg_to_msrp(key, content_types) {#func_msg_to_msrp}


This functions translates a SIP MESSAGE request into a MSRP SEND request.
			The function will initialize a new gateway session and establish the MSRP
			side SIP session if it is not done so already by a previous call.


The SIP From, To, and RURI coordinates for the new MSRP side session are
			taken from the MESSAGE request and mirrored back when translating a MSRP
			SEND to SIP MESSAGE with *msrp_gw_answer*.


Parameters:


- *key* (string) - gateway session key to be used
				to correlate the MESSAGE requests with the MSRP side SIP session.
				A simple example would be to build this key based on the From and To
				URIs from both sides(from the initial MSRP leg INVITE and SIP MESSAGE
				requests respectively).
- *content_types* (string) - content types
				adevertised in the SDP offer on the MSRP side SIP session.


This function can be used only from a request route.


```opensips title="msg_to_msrp() usage"
...
if (is_method("MESSAGE")) {
	msg_to_msrp($var(corr_key), "text/plain");
	exit;
}
...
```


### Exported MI Functions {#exported_mi_functions}


#### msrp_gateway:list_sessions {#mi_list_sessions}


Replaces obsolete MI command: *msrp_gw_list_sessions*.


Lists information about ongoing sessions.


Name: *msrp_gateway:list_sessions*


Parameters


- *None*.


MI FIFO Command Format:


```c
opensips-cli -x mi msrp_gateway:list_sessions
		
```


#### msrp_gateway:end_session {#mi_end_session}


Replaces obsolete MI command: *msrp_gw_end_session*.


Terminate an ongoing session.


Name: *msrp_gateway:end_session*


Parameters


- *key* (string) - session key


MI FIFO Command Format:


```c
opensips-cli -x mi msrp_gateway:end_session alice@opensips.org-bob@opensips.org
		
```


### Exported Events {#exported_events}


#### E_MSRP_GW_SETUP_FAILED {#event_E_MSRP_GW_SETUP_FAILED}


This event is triggered when the MSRP side SIP session fails to set up,
			when using the *msg_to_msrp()* function.


The event can be used to generate a message with the failure description,
			back on the MESSAGE side.


Parameters:


- *key* - The session key.
- *from_uri* - The URI in the SIP From header
				to use on the MESSAGE side.
- *to_uri* - The URI in the SIP To header
				to use on the MESSAGE side.
- *ruri* - The SIP Request URI to use on the
				MESSAGE side.
- *code* - The SIP error code in the negative reply
				received on the MSRP side. Might be NULL if the MSRP UA session expired
				before receiving a negative reply.
- *reason* - The SIP reason string in the negative reply
				received on the MSRP side. Might be NULL if the MSRP UA session expired
				before receiving a negative reply.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 21 | 9 | 1219 | 19 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 5 | 3 | 22 | 14 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 4 | 4 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 3 | 1 | 1 | 1 |
| 5. | Alexandra Titoc | 2 | 1 | 1 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2026 - Apr 2026 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2026 - Mar 2026 |
| 3. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2022 - Jan 2023 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*Documentation Copyrights:*


Copyright © 2022 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
