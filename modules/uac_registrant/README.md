---
title: "UAC Registrant Module"
description: "The module enable OpenSIPS to register itself on a remote SIP registrar."
---

## Admin Guide


### Overview


The module enable OpenSIPS to register itself on a remote SIP registrar.


At startup, the registrant records are loaded into
		a hash table in memory and a timer is started.
		The hash index is computed over the AOR field.


The timer interval for checking records in a hash bucket is computed
		by dividing the timer_interval module param by the number of hash buckets.
		When the timer fires for the first time, the first hash bucket will be checked and
		REGISTERs will be sent out for each record that is found.
		On the next timeout fire, the second hash bucket will be checked and so on.
		If the configured timer_interval module param is lower then the number of buckets,
		the module will fail to start.


Example: setting the timer_interval module to 8 with a hash_size of 2, will result
	    in having 4 hash buckets (2^2=4) and buckets will be checked one by one every 2s (8/4=2).


Each registrant has it's own state.
	    Registrant's status can be inspected via "reg_list" MI comand.


UAC registrant states:


- *0*
				- NOT_REGISTERED_STATE -
				the initial state (no REGISTER has been sent out yet);
- *1*
				- REGISTERING_STATE - waiting for a reply from the registrar
				after a REGISTER without authentication header was sent;
- *2*
				- AUTHENTICATING_STATE - waiting for a reply from the registrar
			 	after a REGISTER with authentication header was sent;
- *3*
				- REGISTERED_STATE - the uac is successfully registered;
- *4*
				- REGISTER_TIMEOUT_STATE :
				no reply received from the registrar;
- *5*
				- INTERNAL_ERROR_STATE -
				some errors were found/encountered during the
				processing of a reply;
- *6*
				- WRONG_CREDENTIALS_STATE -
				credentials rejected by the registrar;
- *7*
				- REGISTRAR_ERROR_STATE -
				error reply received from the registrar;
- *8*
				- UNREGISTERING_STATE - waiting for a reply from the registrar
				after an unREGISTER without authentication header was sent;
- *9*
				- AUTHENTICATING_UNREGISTER_STATE - waiting for a reply from the registrar
				after an unREGISTER with authentication header was sent;


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *uac_auth - UAC authentication module*


#### External Libraries or Applications


None.


### Exported Parameters


#### `hash_size` (integer)


The size of the hash table internally used to keep the registrants.
		A larger table distributes better the registration load in time but consumes more memory.
		The hash size is a power of number two.


*Default value is 1.*


**Example: Set `hash_size` parameter**


```opensips
...
modparam("uac_registrant", "hash_size", 2)
...
```


#### `timer_interval` (integer)


Defines the periodic timer for checking the registrations status.


*Default value is 100.*


**Example: Set `timer_interval` parameter**


```opensips
...
modparam("uac_registrant", "timer_interval", 120)
...
```


#### `failure_retry_interval` (integer)


Defines a custom interval to retry the registration upon error/failure.
		Normally, after any kind of failure (timeout, credentials, internal 
		error), the registration is re-taken after "expires" seconds. The 
		parameter here, if set, overrides that value.


*Default value is 0 (not set).*


**Example: Set `failure_retry_interval` parameter**


```opensips
...
modparam("uac_registrant", "failure_retry_interval", 3600)
...
```


#### `enable_clustering` (integer)


This parameter enables the clustering support in the module. This is
		used to share this registration between all the nodes in the cluster.
		When using this option, you should define (for each registrant record)
		a sharing tag - this sharing tag will control at the cluster level
		which node is entitled to perform the registation (only the node having
		that tag as active will do the registation, the onther nodes being
		idle).


*Default value is 0 / off.*


**Example: Set `enable_clustering` parameter**


```opensips
...
modparam("uac_registrant", "enable_clustering", 1)
...
```


#### `db_url` (string)


Database where to load the registrants from.


*Default value is "NULL" (use default DB URL from core).*


**Example: Set "db_url" parameter**


```opensips
...
modparam("uac_registrant", "db_url", "mysql://user:passw@localhost/database")
...
```


#### `table_name` (string)


The database table that holds the registrant records.


*Default value is "registrant".*


**Example: Set "table_name" parameter**


```opensips
...
modparam("uac_registrant", "table_name", "my_registrant")
...
```


#### `registrar_column` (string)


The column's name in the database storing the
		URI pointing to the remote registrar (mandatory field).
		OpenSIPS expects a valid URI.


*Default value is "registrar".*


**Example: Set "registrar_column" parameter**


```opensips
...
modparam("uac_registrant", "registrar_column", "registrant_uri")
...
```


#### `proxy_column` (string)


The column's name in the database storing the
		URI pointing to the outbond proxy (not mandatory field).
		An empty or NULL value means no outbound proxy,
		otherwise OpenSIPS expects a valid URI.


*Default value is "proxy".*


**Example: Set "proxy_column" parameter**


```opensips
...
modparam("uac_registrant", "proxy_column", "proxy_uri")
...
```


#### `aor_column` (string)


The column's name in the database storing the
		URI defining the address of record (mandatory field).
		The URI stored here will be used in the To URI of the REGISTER.
		OpenSIPS expects a valid URI.


*Default value is "aor".*


**Example: Set "aor_column" parameter**


```opensips
...
modparam("uac_registrant", "aor_column", "to_uri")
...
```


#### `third_party_registrant_column` (string)


The column's name in the database storing the
		URI defining the third party registrant (not mandatory field).
		The URI stored here will be used in the From URI of the REGISTER.
		An empty or NULL value means no third party registration
		(the From URI will be identical to To URI),
		otherwise OpenSIPS expects a valid URI.


*Default value is "third_party_registrant".*


**Example: Set "third_party_registrant_column" parameter**


```opensips
...
modparam("uac_registrant", "third_party_registrant_column", "from_uri")
...
```


#### `username_column` (string)


The column's name in the database storing the
		username for authentication (mandatory if the registrar requires authentication).


*Default value is "username".*


**Example: Set "username_column" parameter**


```opensips
...
modparam("uac_registrant", "username_column", "auth_username")
...
```


#### `password_column` (string)


The column's name in the database storing the
		password for authentication (mandatory if the registrar requires authntication).


*Default value is "password".*


**Example: Set "password_column" parameter**


```opensips
...
modparam("uac_registrant", "password_column", "auth_passowrd")
...
```


#### `binding_URI_column` (string)


The column's name in the database storing the
		binding URI in REGISTER (mandatory field).
		The URI stored here will be used in the Contact URI of the REGISTER.
		OpenSIPS expects a valid URI.


*Default value is "binding_URI".*


**Example: Set "binding_URI_column" parameter**


```opensips
...
modparam("uac_registrant", "binding_URI_column", "contact_uri")
...
```


#### `binding_params_column` (string)


The column's name in the database storing the
		binding params in REGISTER (not mandatory field).
		If not NULL or not empty, the string stored here will be added
		as params to the Contact URI in REGISTER (it MUST start with ";".


If the following two params are present, then the binding will be enforced
		to be unique (if two bindings are received in a 200ok, a complete binding
		removal will be performed before re-registering):


- *reg-id*
- *+sip.instance*


Example of params that will force unique binding:


```
;reg-id=1;+sip.instance="<urn:uuid:11111111-AABBCCDDEEFF>"
		
```


*Default value is "binding_params".*


**Example: Set "binding_params_column" parameter**


```opensips
...
modparam("uac_registrant", "binding_params_column", "contact_params")
...
```


#### `expiry_column` (string)


The column's name in the database storing the
		expiration time (not mandatory).


*Default value is "expiry".*


**Example: Set "expiry_column" parameter**


```opensips
...
modparam("uac_registrant", "expiry_column", "registration_timeout")
...
```


#### `forced_socket_column` (string)


The column's name in the database storing the
		socket for sending the REGISTER (not mandatory).
		If a forced socket is provided, the socket MUST be
		explicitely set as a global listening socket in the config
		(see "listen" core parameter).


*Default value is "forced_socket".*


**Example: Set "forced_socket_column" parameter**


```opensips
...
modparam("uac_registrant", "forced_socket_column", "fs")
...
```


#### `cluster_shtag_column` (string)


The column's name in the database storing the
		cluster sharing tag in [tag_name/cluster_id] format (not mandatory).
		If a cluster sharing tag is provided, the REGISTER requests will
		be fired out only when the tag is active.


*Default value is "cluster_shtag".*


**Example: Set "cluster_shtag_column" parameter**


```opensips
...
modparam("uac_registrant", "cluster_shtag_column", "sh")
...
```


#### `state_column` (string)


The column's name in the database storing the current state of the
		registrant. When a registrant is disabled, OpenSIPS will no longer send
		REGISTERs for it. A value of *0* for this column means
		enabled and *1* disabled.


*Default value is "state".*


**Example: Set "state_column" parameter**


```opensips
...
modparam("uac_registrant", "state_column", "status")
...
```


### Exported Functions


None to be used in configuration file.


### Exported MI Functions


#### `reg_list`


Lists the registrant records and their status.


Name: *reg_list*


Parameters:


- *aor* (optional) - URI defining the address
				of record. If provided, *contact* and
				*registrar* parameters are also required and
				only a specific record will be listed.
- *contact* (optional) - Contact URI. If 
				provided,
				*aor* and *registrar*
				parameters are also required and only a specific record will
				be listed.
- *registrar* (optional) - URI pointing to the
				remote registrar. If provided, *aor* and
				*contact* parameters are also required and
				only a specific record will be listed.


MI FIFO Command Format:


```
opensips-cli -x mi reg_list
...
opensips-cli -x mi reg_list sip:alice@opensips.org  sip:alice@127.0.0.1:5060 sip:opensips.org
		
```


#### `reg_reload`


Reloads the registrant records from the database.


Name: *reg_reload*


Parameters: *none*


- *aor* (optional) - URI defining the address
				of record. If provided, *contact* and
				*registrar* parameters are also required and
				only a specific record will be reloaded.
- *contact* (optional) - Contact URI. If 
				provided,
				*aor* and *registrar*
				parameters are also required and only a specific record will
				be reloaded.
- *registrar* (optional) - URI pointing to the
				remote registrar. If provided, *aor* and
				*contact* parameters are also required and
				only a specific record will be reloaded.


MI FIFO Command Format:


```
opensips-cli -x mi reg_reload
...
opensips-cli -x mi reg_leload sip:alice@opensips.org  sip:alice@127.0.0.1:5060 sip:opensips.org
		
```


#### `reg_enable`


Enables a specific registrant. OpenSIPS will immediately send
		a REGISTER if the registrant was previously disabled and will update
		the state in the database.


Name: *reg_enable*


Parameters: *none*


- *aor* - URI defining the address of record.
- *contact* - Contact URI.
- *registrar* - URI pointing to the remote registrar.


MI FIFO Command Format:


```
opensips-cli -x mi reg_enable sip:alice@opensips.org  sip:alice@127.0.0.1:5060 sip:opensips.org
		
```


#### `reg_disable`


Disables a specific registrant. OpenSIPS will immediately send
		an unREGISTER if the registrant was previously enabled and will update
		the state in the database.


Name: *reg_disable*


Parameters: *none*


- *aor* - URI defining the address
				of record. If provided, *contact* and
				*registrar* parameters are also required and
				only a specific record will be disabled.
- *contact* - Contact URI. If provided,
				*aor* and *registrar*
				parameters are also required and only a specific record will
				be disabled.
- *registrar* - URI pointing to the remote
				registrar. If provided, *aor* and
				*contact* parameters are also required and
				only a specific record will be disabled.


MI FIFO Command Format:


```
opensips-cli -x mi reg_disable sip:alice@opensips.org  sip:alice@127.0.0.1:5060 sip:opensips.org
		
```


#### `reg_force_register`


Forces the re-registration (or registation) of a specific 
		registrant (depending on its state). Note that the registrant must be
		enabled.


Name: *reg_force_register*


Parameters:


- *aor* - URI defining the address
				of record. If provided, *contact* and
				*registrar* parameters are also required and
				only a specific record will be forced to re-register.
- *contact* - Contact URI. If provided,
				*aor* and *registrar*
				parameters are also required and only a specific record will be
				forced to re-register.
- *registrar* - URI pointing to the remote
				registrar. If provided, *aor* and
				*contact* parameters are also required and
				only a specific record will be forced to re-register.


MI FIFO Command Format:


```
opensips-cli -x mi reg_force_register sip:alice@opensips.org  sip:alice@127.0.0.1:5060 sip:opensips.org
		
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 143 | 41 | 5688 | 3268 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 18 | 13 | 451 | 49 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 17 | 14 | 57 | 68 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 16 | 7 | 732 | 118 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 11 | 9 | 26 | 36 |
| 6. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 5 | 3 | 43 | 4 |
| 7. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 20 | 16 |
| 8. | James Stanley | 4 | 2 | 21 | 14 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 4 | 2 | 6 | 3 |
| 10. | sagarmalam | 3 | 1 | 15 | 1 |


**All remaining contributors**: Walter Doekes ([@wdoekes](https://github.com/wdoekes)), James Stanley, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), okhowang(王沛文).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | Nov 2015 - Mar 2025 |
| 2. | okhowang(王沛文) | Jul 2024 - Jul 2024 |
| 3. | James Stanley | Dec 2023 - Jun 2024 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Dec 2012 - May 2024 |
| 5. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Feb 2011 - Dec 2023 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Mar 2021 - Nov 2023 |
| 7. | James Stanley | Mar 2023 - Mar 2023 |
| 8. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Sep 2011 - Nov 2021 |
| 9. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Jul 2021 |
| 10. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2021 - Apr 2021 |


**All remaining contributors**: sagarmalam, Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)).


*Documentation Copyrights:*


Copyright © 2011-2014 [VoIP Embedded, Inc.](http://www.voipembedded.com)
