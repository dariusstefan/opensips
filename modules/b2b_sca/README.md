---
title: "b2b_sca Module"
description: "This module provides core SCA (Shared Call Appearance) functionality for OpenSIPS. It is designed to work in tandem with the presence_callinfo module."
---

## Admin Guide


### Overview


This module provides core SCA (Shared Call Appearance) functionality
		for OpenSIPS.
		It is designed to work in tandem with the presence_callinfo module.


The module handles the basic SIP signalling for call controll while
		publishing callinfo events to a presence server.
		It is built on top of the b2b_logic module and it is using the
		'top hiding' scenario to control SIP signalling.


A typical usage example is provided below, where Alice makes a
		call to Bob.  The call leg between Alice and the b2b_sca server
		is an "appearance" call of the "shared" call between the b2b_sca server
		and Bob.


```
   caller         caller      b2b_sca     callee   presence server
alice1@example alice2@example  server   bob@example watcher@example
     |              |             |           |           |
     |--INV bob------------------>|           |           |
     |              |             |--INV bob->|           |
     |              |             |--PUBLISH(alerting)--->|
     |              |             |<-----200 OK-----------|
     |              |             |           |           |
     |              |             |<-180 ring-|           |
     |<-180 ring------------------|           |           |
     |              |             |           |           |
     |              |             |           |           |
     |              |             |<-200 OK---|           |
     |<-200 OK--------------------|--ACK----->|           |
     |--ACK---------------------->|--PUBLISH(active)----->|
     |              |             |<-----200 OK-----------|
     |              |             |           |           |
     |--INV bob (hold)----------->|           |           |
     |              |             |--INV bob->|           |
     |              |             |--PUBLISH(held)------->|
     |              |             |<-----200 OK-----------|
     |              |             |<-200 OK---|           |
     |<--200 OK-------------------|           |           |
     |              |             |           |           |
     |              |--INV------->|           |           |
     |              |             |--INV bob->|           |
     |<-BYE-----------------------|--PUBLISH(active)----->|
     |--200 OK------------------->|<-----200 OK-----------|
     |              |             |<-200 OK---|           |
     |              |<-200 OK-----|           |

	
```


- Alice calls Bob from her desk IP phone (alice1).
- Bob answers the call.
- Alice decide to carry the conversation from a meeting room
	and she put's BOB on hold.
- Alice arrives to the meeting room and retrieves the call on the
	conference room IP phone (alice2).


### To-do


Features to be added in the future:


- possibility to handle unlimited number of appearances.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *tm* module.
				*pua* module.
				*b2b_logic* module.


### Exported Parameters


#### `hash_size`(integer)


The size of the hash table internally used to keep the shared calls.
		A larger table means faster acces at the expense of memory.
		The hash size is a power of number two.


*The default value is "10".*


**Example: Set `hash_size` parameter**


```opensips
...
modparam("b2b_sca", "hash_size", "5")
...
```


#### `presence_server`(string)


The address of the presence server, where the PUBLISH
		messages should be sent (not compulsory).
		If not set, the PUBLISH requests will be routed based
		on watcher's URI.


*The default value is "NULL".*


**Example: Set `presence_server` parameter**


```opensips
...
modparam("b2b_sca", "presence_server", "sip:opensips.org")
...
```


#### `watchers_avp_spec`(string)


AVP that will hold one or more watcher URI(s).
		If not set, no PUBLISH requests will be sent out.
		The watchers_avp_spec MUST be set before calling sca_init_request();


*The default value is "NULL".*


**Example: Set `watchers_avp_spec` parameter**


```opensips
...
modparam("b2b_sca", "watchers_avp_spec", "$avp(watchers_avp_spec)")
...
route {
	...
	$avp(watchers_avp_spec) = "sip:first_watcher@opensip.org";
	$avp(watchers_avp_spec) = "sip:second_watcher@opensip.org";
	...
}
```


#### `shared_line_spec_param`(string)


Mandatory parameter.
		Opaque string identifing the shared line/call.
		The shared_line_spec_param MUST be set before calling sca_init_request();


*The default value is "NULL".*


**Example: Set `shared_line_spec_param` parameter**


```opensips
...
modparam("b2b_sca", "shared_line_spec_param", "$var(shared_line)")
...
```


#### `appearance_name_addr_spec_param`(string)


Mandatory parameter.
		It must be a valid SIP URI.
		It will populate the *appearance-uri* SIP parameter
		inside the *Call-Info* SIP header.
		The appearance_name_addr_spec_param MUST be set before calling sca_init_request();


*The default value is "NULL".*


**Example: Set `appearance_name_addr_spec_param` parameter**


```opensips
...
modparam("b2b_sca", "appearance_name_addr_spec_param", "")
...
```


#### `db_url`(string)


This is URL of the database to be used.


*The default value is "NULL".*


**Example: Set `db_url` parameter**


```opensips
...
modparam("b2b_sca", "db_url", "[dbdriver]://[[username]:[password]]@[dbhost]/[dbname]")
...
```


#### `db_mode`(integer)


The b2b_sca module can utilize database for persistent call appearance storage.
		Using a database ensure that active call appearances will survive
		machine restarts or SW crashes.
		The following databse accessing modes are available for b2b_sca module:


- 
- 


*The default value is 0 (NO DB STORAGE).*


**Example: Set `db_mode` parameter**


```opensips
...
modparam("b2b_sca", "db_mode", 1)
...
```


#### `table_name`(string)


Identifies the table name from the defined database.


*The default value is "b2b_sca".*


**Example: Set `table_name` parameter**


```opensips
...
modparam("b2b_sca", "table_name", "sla")
...
```


#### `shared_line_column`(string)


The column's name in the database storing the shared call/line id.
		See "shared_line_spec_param" parameter.


*The default value is "shared_line".*


**Example: Set `shared_line_column` parameter**


```opensips
...
modparam("b2b_sca", "shared_line_column", "")
...
```


#### `watchers_column`(string)


The column's name in the database storing the list of watchers.
		See "watchers_avp_spec" parameter.


*The default value is "watchers".*


**Example: Set `watchers_column` parameter**


```opensips
...
modparam("b2b_sca", "watchers_column", "")
...
```


#### `app[index]_shared_entity_column`(string)


The column's name in the database storing the shared entity of a
		particular appearance.
		See "sca_init_request" for more info.


*The default value is "app[index]_shared_entity".*
		Index is an integer between 1 and 10.


**Example: Set `app[index]_shared_entity_column` parameter**


```opensips
...
modparam("b2b_sca", "app1_shared_entity_column", "first_shared_entity")
modparam("b2b_sca", "app2_shared_entity_column", "second_shared_entity")
...
```


#### `app[index]_call_state_column`(string)


The column's name in the database storing the call state of a
		particular appearance.  The following states are stored:


- 
- 
- 
- 


*The default value is "app[index]_call_state".*
		Index is an integer between 1 and 10.


**Example: Set `app[index]_call_state_column` parameter**


```opensips
...
modparam("b2b_sca", "app1_call_state_column", "first_call_state")
modparam("b2b_sca", "app2_call_state_column", "second_call_state")
...
```


#### `app[index]_call_info_uri_column`(string)


The column's name in the database storing the call info URI of a
		particular appearance.


*The default value is "app[index]_call_info_uri".*
		Index is an integer between 1 and 10.


**Example: Set `app[index]_call_info_uri_column` parameter**


```opensips
...
modparam("b2b_sca", "app1_call_info_uri_column", "first_call_info_uri")
modparam("b2b_sca", "app2_call_info_uri_column", "second_call_info_uri")
...
```


#### `app[index]_call_info_appearance_uri_column`(string)


The column's name in the database storing the call info appearance URI
		of a particular appearance.
		For each appearance, the value is extracted from the
		"appearance_name_addr_spec_param" parameter.


*The default value is "app[index]_call_info_appearance_uri".*
		Index is an integer between 1 and 10.


**Example: Set `app[index]_call_info_appearance_uri_column` parameter**


```opensips
...
modparam("b2b_sca", "app1_call_info_appearance_uri_column", "first_call_info_appearance_uri")
modparam("b2b_sca", "app2_call_info_appearance_uri_column", "second_call_info_appearance_uri")
...
```


#### `appindex_b2bl_key_column`(string)


The column's name in the database storing the b2b_logic key of a
		particular appearance.


*The default value is "app[index]_b2bl_key".*
		Index is an integer between 1 and 10.


**Example: Set `app[index]_b2bl_key_column` parameter**


```opensips
...
modparam("b2b_sca", "app1_b2bl_key_column", "first_b2bl_key")
modparam("b2b_sca", "app2_b2bl_key_column", "second_b2bl_key")
...
```


### Exported Functions


#### `sca_init_request(shared_line)`


This is the function that must be called by the script writer
		on an initial INVITE for which an SCA call must be instantiated
		(see the call from alice1 in the above diagram).


Meaning of the parameters:


- *shared_line* (int) - an integer
		identifying the call leg as being an "appearnace" call or a "shared" call:
			
			0: "shared" call
			1: "appearance" call


**Example: `sca_init_request()` usage**


```opensips
...
modparam("b2b_sca",
	"shared_line_spec_param","$var(shared_line)")
modparam("b2b_sca",
	"appearance_name_addr_spec_param","$var(appearance_name_addr)")
modparam("b2b_sca",
	"watchers_avp_spec","$avp(watchers_avp_spec)")

...

	# Setting the shared call identifier
	$var(shared_line) = "alice";

	# Setting the watchers
	$avp(watchers_avp_spec) = "sip:alice1@example.com";
	$avp(watchers_avp_spec) = "sip:alice2@example.com";

	if (INCOMING_SHARED_CALL) {
		# The incoming call is a 'shared' call
		$var(shared_line_entity) = 0;
		# Setting the appearance name address
		$var(appearance_name_addr) = $fu;
	}
	else {
		# The incoming call is an 'appearance' call
		# - see Alice's initial call leg in the given example
		$var(shared_line_entity) = 1;
		# Setting the appearance name address
		$var(appearance_name_addr) = $tu;
	}

	# Initiate the call
	if (!sca_init_request($var(shared_line_entity))) {
		send_reply(403, "Internal Server Error (SLA)");
		exit;
	}
...
```


#### `sca_bridge_request(shared_line_to bridge)`


This is the function that must be called by the script writer on an initial
		"appearance" INVITE for an existing shared call.  It will bridge the current
		"appearance" call with the existing "shared" call and the old "appearance"
		call will be disconnected (see the call from alice2 in the above diagram).


Meaning of the parameters:


- *shared_line_to_bridge* (string) - a string identifying
		the shared line/call that was previously set by sca_init_request().


```opensips
...
	if ($rU==NULL && is_method("INVITE") &&
		$fU==$tU && is_present_hf("Call-Info")) {
		# The incoming call is an 'appearance' call
		# - see Alice's call from alice2 in the given example
		$var(shared_line_to_bridge) = "alice";
		if (!sca_bridge_request($var(shared_line_to_bridge)))
			send_reply(403, "Internal SLA Error");
			exit;
		}
	}
...
```


### Exported MI Functions


#### `sca_list`


It lists the appearances belonging to a shared line/call.


Name: *sca_list*


Parameters: *none*


MI FIFO Command Format:


```
	opensips-cli -x mi sca_list
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 33 | 2 | 3536 | 2 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 13 | 10 | 63 | 67 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 12 | 10 | 32 | 28 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 9 | 7 | 13 | 13 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 8 | 5 | 82 | 90 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 5 | 7 |
| 7. | Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)) | 3 | 1 | 1 | 1 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Oct 2022 - Feb 2023 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Nov 2022 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2015 - Feb 2022 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | May 2014 - Mar 2020 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Dec 2013 - Feb 2016 |
| 8. | Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)) | Oct 2014 - Oct 2014 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)).


*Documentation Copyrights:*


Copyright © 2011-2013 [VoIP Embedded, Inc.](http://www.voipembedded.com)
