---
title: "PUA Bridged Line Appearances"
description: "The pua_bla module enables Bridged Line Appearances support according to the specifications in draft-anil-sipping-bla-03.txt."
---

## Admin Guide


### Overview


The pua_bla module enables Bridged Line Appearances support according to 
		 the specifications in draft-anil-sipping-bla-03.txt.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *usrloc*.
- *pua*.
- *presence*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libxml*.


### Exported Parameters


#### `default_domain`(str)


The default domain for the registered users to be used when
		constructing the uri for the registrar callback.


*Default value is "NULL".*


**Example: Set `default_domain` parameter**


```opensips
...
modparam("pua_bla", "default_domain", "opensips.org")
...
```


#### `header_name`(str)


The name of the header to be added to Publish requests.
		It will contain the uri of the user agent that sent the
		Notify that is transformed into Publish. It stops sending 
		a Notification with the same information to the sender.


*Default value is "NULL".*


**Example: Set `header_name` parameter**


```opensips
...
modparam("pua_bla", "header_name", "Sender")
...
```


#### `outbound_proxy`(str)


The outbound_proxy uri to be used when sending Subscribe requests.


*Default value is "NULL".*


**Example: Set `outbound_proxy` parameter**


```opensips
...
modparam("pua_bla", "outbound_proxy", "sip:proxy@opensips.org")
...
```


#### `server_address`(str)


The IP address of the server.


**Example: Set `server_address` parameter**


```opensips
...
modparam("pua_bla", "server_address", "sip:bla@160.34.23.12")
...
```


#### `presence_server`(str)


The address of the presence server - will be used as
			an outbound proxy when sending PUBLISH requests. 
			It is optional.


*Default value is "NULL".*


**Example: Set `presence_server` parameter**


```opensips
...
modparam("pua_bla", "presence_server", "sip:pa@opensips.org")
...
```


### Exported Functions


#### `bla_set_flag()`


The function is used to mark REGISTER requests made to a BLA AOR.
				The modules subscribes to the registered contacts for dialog;sla 
				event.


**Example: `bla_set_flag` usage**


```opensips
...
if(is_method("REGISTER") && $tu=~"bla_aor@opensips.org") 
	bla_set_flag();		
...
```


#### `bla_handle_notify()`


The function handles Notify requests sent from phones on the
				same BLA to the server. The message is transformed in Publish 
				request and passed to presence module for further handling.
				in case of a successful processing a 2xx reply should be sent.


**Example: `bla_handle_notify` usage**


```opensips
...
if(is_method("NOTIFY") && $tu=~"bla_aor@opensips.org") 
{
		if( bla_handle_notify() ) 
			t_reply(200, "OK");
}	
...
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Anca Vamanu | 57 | 27 | 1961 | 779 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 17 | 14 | 58 | 73 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 15 | 13 | 43 | 61 |
| 4. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 9 | 7 | 17 | 15 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 12 | 13 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 5 | 20 | 18 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 4 | 2 | 15 | 4 |
| 8. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 3 | 1 | 6 | 16 |
| 9. | Sergio Gutierrez | 3 | 1 | 4 | 4 |
| 10. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 3 | 3 |


**All remaining contributors**: Konstantin Bokarius, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Stanislaw Pitucha.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2007 - Nov 2025 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Jan 2023 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)) | Oct 2014 - Oct 2014 |
| 8. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Dec 2010 - Jan 2013 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Aug 2011 - Aug 2011 |
| 10. | Anca Vamanu | Apr 2007 - Apr 2011 |


**All remaining contributors**: Stanislaw Pitucha, Sergio Gutierrez, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Anca Vamanu, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert.


*Documentation Copyrights:*


Copyright © 2007 Voice Sistem SRL
