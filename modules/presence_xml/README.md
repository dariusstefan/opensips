---
title: "Presence_XML Module"
description: "The module does specific handling for notify-subscribe events using xml bodies. It is used with the general event handling module, presence. It constructs and adds 3 events to it: presence, presence.winfo, dialog;sla."
---

## Admin Guide


### Overview {#overview}


The module does specific handling for notify-subscribe events using xml bodies.
	It is used with the general event handling module, presence. It constructs and adds
	3 events to it: presence, presence.winfo, dialog;sla.


This module takes the xcap permission rule documents from xcap_table.

	The presence permission rules are interpreted according to the specifications
	in RFC 4745 and RFC 5025.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *a database module*.
- *presence*.
- *signaling*.
- *xcap*.
- *xcap_client*.
Only compulsory if not using an integrated xcap server 
			(if 'integrated_xcap_server' parameter is not set).


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libxml-dev*.


### Exported Parameters {#exported_parameters}


#### force_active (int) {#param_force_active}


This parameter is used for permissions when handling Subscribe messages.
		If set to 1, subscription state is considered active and the presentity
		is not queried for permissions(should be set to 1 if not using an xcap 
		server). 
		Otherwise,the xcap server is queried and the subscription states is
		according to user defined permission rules. If no rules are defined for
		a certain watcher, the subscriptions remains in pending state and the
		Notify sent will have no body.


Note: When switching from one value to another, the watchers table must
		be emptied.


*Default value is "0".*


```c title="Set force_active parameter"
...
modparam("presence_xml", "force_active", 1)
...
```


#### pidf_manipulation (int) {#param_pidf_manipulation}


Setting this parameter to 1 enables the features described in RFC 4827.
		It gives the possibility to have a permanent state notified to the users
		even in the case in which the phone is not online. The presence document
		is taken from the xcap server and aggregated together with the other
		presence information, if any exist, for each Notify that is sent to the
		watchers. It is also possible to have information notified even if not 
		issuing any Publish (useful for services such as email, SMS, MMS).


*Default value is "0".*


```c title="Set pidf_manipulation parameter"
...
modparam("presence_xml", "pidf_manipulation", 1)
...
```


#### xcap_server (str) {#param_xcap_server}


The address of the xcap servers used for storage.
		This parameter is compulsory if the integrated_xcap_server parameter
		is not set. It can be set more that once, to construct an address
		list of trusted XCAP servers.


```c title="Set xcap_server parameter"
...
modparam("presence_xml", "xcap_server", "xcap_server.example.org")
modparam("presence_xml", "xcap_server", "xcap_server.ag.org")
...
```


#### pres_rules_auid (str) {#param_pres_rules_auid}


This parameter should be configured if you are using the non integrated xcap
		mode and you need to use another pres-rules auid than the default 'pres-rules'.


```c title="Set pres_rules_auid parameter"
...
modparam("presence_xml", "pres_rules_auid", "org.openmobilealliance.pres-rules")
...
```


#### pres_rules_filename (str) {#param_pres_rules_filename}


This parameter should be configured if you are using the non integrated xcap
		mode and you need to configure another filename than the default 'index'.


```c title="Set pres_rules_filename parameter"
...
modparam("presence_xml", "pres_rules_filename", "pres-rules")
...
```


#### generate_offline_body (str) {#param_generate_offline_body}


This parameter should be set to 0 if you want to prevent OpenSIPS from automatically
                generating a PIDF body when a publication expires or is explicitly terminated
                (a PUBLISH request is received with Expires: 0).


```c title="Set generate_offline_body parameter"
...
modparam("presence_xml", "generate_offline_body", 0)
...
```


### Exported Functions {#exported_functions}


None to be used in configuration file.


### Installation


The module requires 1 table in OpenSIPS database: xcap. The SQL 
	syntax to create it can be found in presence-create.sql     
	script in the database directories in the opensips/scripts folder.
	You can also find the complete database documentation on the
	project webpage, https://opensips.org/docs/db/db-schema-devel.html.


## Developer Guide


The module exports no function to be used in other OpenSIPS modules.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Anca Vamanu | 124 | 54 | 4745 | 1776 |
| 2. | Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)) | 30 | 9 | 1499 | 471 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 28 | 24 | 80 | 130 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 16 | 14 | 40 | 37 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 14 | 11 | 45 | 71 |
| 6. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 8 | 6 | 15 | 14 |
| 7. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 6 | 4 | 45 | 50 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 4 | 4 |
| 9. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 4 | 2 | 3 | 3 |
| 10. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 3 | 2 | 6 | 0 |


**All remaining contributors**: Kennard White, Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Konstantin Bokarius, Alexandra Titoc, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), UnixDev, Zero King ([@l2dy](https://github.com/l2dy)), Edson Gellert Schubert, Denis Bilenko, Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - May 2026 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2007 - Nov 2025 |
| 3. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 6. | Zero King ([@l2dy](https://github.com/l2dy)) | Mar 2020 - Mar 2020 |
| 7. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | Oct 2007 - Nov 2018 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2017 |
| 10. | Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)) | May 2012 - Mar 2013 |


**All remaining contributors**: Anca Vamanu, Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Kennard White, Walter Doekes ([@wdoekes](https://github.com/wdoekes)), UnixDev, Denis Bilenko, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Saúl Ibarra Corretgé ([@saghul](https://github.com/saghul)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Anca Vamanu, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Dan Pascu ([@danpascu](https://github.com/danpascu)).


*Documentation Copyrights:*


Copyright © 2007 Voice Sistem SRL
