---
title: "MSRP UA Module"
description: "This module implements an User Agent capable of establishing messaging sessions using the MSRP(RFC 4976) protocol."
---

## Admin Guide


### Overview {#overview}


This module implements an User Agent capable of establishing messaging
		sessions using the MSRP(RFC 4976) protocol.


Through the exported script and MI functions, the module allows OpenSIPS
    	to set up MSRP sessions via SIP and exchange messages as an MSRP endpoint.


The module makes use of the *proto_msrp* module for
    	the MSRP protocol stack and the *b2b_entities* module 
    	for the SIP UAC/UAS functionalities.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *proto_msrp*
- *b2b_entities*


#### External Libraries or Applications


The following libraries or applications must be installed 
			before running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### hash_size (int) {#param_hash_size}


The size of the hash table that stores the MSRP session
				information. It is the 2 logarithmic value of the real size.


*Default value is "10"*
			 (1024 records).


**Example: Set hash_size parameter**


```opensips
...
modparam("msrp_ua", "hash_size", 16)
...
		
```


#### cleanup_interval (int) {#param_cleanup_interval}


The interval between full iterations of the sessions table
			in order to clean up expired MSRP sessions.


*Default value is "60".*


**Example: Set cleanup_interval parameter**


```opensips
...
modparam("msrp_ua", "cleanup_interval", 30)
...
		
```


#### max_duration (integer) {#param_max_duration}


The maximum duration of a call. If set to 0, there will be no limitation.


The default value is 12 * 3600 seconds (12 hours).


**Example: max_duration parameter example**


```opensips
...
modparam("msrp_ua", "max_duration", 7200)
...
```


#### my_uri (string) {#param_my_uri}


The MSRP URI of the OpenSIPS endpoint. This URI will be advertised in the SDP
		offer provided to peers when setting up a session and should match one
		of the MSRP listeners defined in the script.


The *session-id* part of the URI should be ommited.


If the port is not set explicitly, the default value of 2855 wil
		be assumed


**Example: my_uri parameter usage**


```opensips
...
modparam("msrp_ua", "my_uri", "msrp://opensips.org:2855;tcp")
...
```


#### advertised_contact (string) {#param_advertised_contact}


Contact to be used in the generated SIP requests. For sessions answered
		by OpenSIPS, if it is not set, it is constructed dynamically from the
		socket where the initiating request was received.


This parameter is mandatory when using the
		[mi msrp ua start session](#mi_msrp_ua_start_session) MI function.


**Example: advertised_contact parameter usage**


```opensips
...
modparam("msrp_ua", "advertised_contact", "sip:oss@opensips.org")
...
```


#### relay_uri (string) {#param_relay_uri}


URI of an MSRP relay to use for both accepted and initiated
		sessions.


Credentials for the MSRP client are provided via the
		*uac_auth* module by setting the
		*credential* module parameter.


If not set, no relay will be used.


**Example: relay_uri parameter usage**


```opensips
...
modparam("msrp_ua", "relay_uri", "msrp://opensips.org:2856;tcp")
...
```


### Exported Functions {#exported_functions}


#### msrp_ua_answer(content_types) {#func_msrp_ua_answer}


This functions answers an initial INVITE offering a new MSRP
			messaging session. After this function is used to initialize the
			session, the call will be completely handled by the B2B engine.


Parameters:


- *content_types* (string) - content types
				adevertised in the *accept-types* SDP
				attribute. At least one of the content types in this list must
				match the types offered by the peer in its SDP offer.


This function can be used only from a request route.


**Example: msrp_ua_answer() usage**


```opensips
...
if (!has_totag() && is_method("INVITE")) {
	msrp_ua_answer("text/plain");
	exit;
}
...
```


### Exported MI Functions {#exported_mi_functions}


#### msrp_ua_send_message {#mi_msrp_ua_send_message}


Sends a new MSRP message to the peer.


Name: *msrp_ua_send_message*


Parameters


- *session_id* (string) - the MSRP session
				identifier ("session-id" part of the MSRP URI).
- *mime* (string, optional) - MIME content
				type of this message. If missing, an empty message will be sent.
- *body* (string, optional) - actual message
				body. If missing, an empty message will be sent.


MI FIFO Command Format:


```
opensips-cli -x mi msrp_ua_send_message \
	5addd9e7b74fa44fbace68a4fc562293 \
	text/plain \
	Hello
		
```


#### msrp_ua_start_session {#mi_msrp_ua_start_session}


Starts a MSRP session.


The [advertised contact](#param_advertised_contact) is mandatory if this
		function is used.


Name: *msrp_ua_start_session*


Parameters


- *content_types* (string) - content types
				adevertised in the *accept-types* SDP
				attribute.
- *from_uri* (string) - From URI to be used
				in the INVITE.
- *to_uri* (string) - To URI to be used
				in the INVITE.
- *ruri* (string) - Request URI and destination
				of the INVITE.


MI FIFO Command Format:


```
opensips-cli -x mi msrp_ua_start_session \
	text/plain sip:oss@opensips.org \
	sip:alice@opensips.org sip:alice@opensips.org
		
```


#### msrp_ua_list_sessions {#mi_msrp_ua_list_sessions}


Lists information about ongoing MSRP sessions.


Name: *msrp_ua_list_sessions*


Parameters


- *None*.


MI FIFO Command Format:


```
opensips-cli -x mi msrp_ua_list_sessions
		
```


#### msrp_ua_end_session {#mi_msrp_ua_end_session}


Terminate an ongoing MSRP session.


Name: *msrp_ua_end_session*


Parameters


- *session_id* (string) - the MSRP session
				identifier ("session-id" part of the MSRP URI).


MI FIFO Command Format:


```
opensips-cli -x mi msrp_ua_end_session \
	5addd9e7b74fa44fbace68a4fc562293
		
```


### Exported Events {#exported_events}


#### E_MSRP_SESSION_NEW {#event_E_MSRP_SESSION_NEW}


This event is triggered when a new MSRP session is successfully
			established(ACK sent/received).


Parameters:


- *from_uri* - The URI in the SIP From header
				of the answered INVITE.
- *to_uri* - The URI in the SIP To header
				of the answered INVITE.
- *ruri* - The SIP Request URI of the answered
				INVITE.
- *session_id* - The MSRP session identifier
				("session-id" part of the MSRP URI).
- *content_types* - The content types offered
				by the peer in the *accept-types* SDP attribute.


#### E_MSRP_SESSION_END {#event_E_MSRP_SESSION_END}


This event is triggered when an ongoing MSRP session is terminted (session
			expires or BYE is received; terminating a session via the
			*msrp_ua_end_session* MI function is not included).


Parameters:


- *session_id* - The MSRP session identifier
				("session-id" part of the MSRP URI).


#### E_MSRP_MSG_RECEIVED {#event_E_MSRP_MSG_RECEIVED}


This event is triggered when receiving a new, non-empty MSRP message
			from the peer.


Parameters:


- *session_id* - The MSRP session identifier
				("session-id" part of the MSRP URI).
- *content_type* - The content type of this message.
- *body* - The actual message body.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 44 | 12 | 3398 | 233 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 3 | 1 | 11 | 1 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 3 | 1 | 8 | 8 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2022 - Jan 2023 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2022 - Aug 2022 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Jul 2022 - Jul 2022 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | May 2022 - May 2022 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*Documentation Copyrights:*


Copyright © 2022 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
