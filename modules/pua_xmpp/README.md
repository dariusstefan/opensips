---
title: "Presence User Agent for XMPP (Presence gateway between SIP and XMPP)"
description: "This module is a gateway for presence between SIP and XMPP."
---

## Admin Guide


### Overview


This module is a gateway for presence between SIP and XMPP.


It translates one format into another and uses xmpp, pua and presence
		modules to manage the transmition of presence state information.


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *presence*.
- *pua*.
- *xmpp*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libxml*.


### Exported Parameters


#### `server_address`(str)


The IP address of the server.


**Example: Set `server_address` parameter**


```opensips
...
modparam("pua_xmpp", "server_address", "sip:sa@opensips.org:5060")
...
```


#### `presence_server` (str)


The the address of the presence server. If set, it will be
		used as outbound proxy when sending PUBLISH requests.


**Example: Set `presence_server` parameter**


```opensips
...
modparam("pua_xmpp", "presence_server", "sip:pa@opensips.org:5075")
...
	
```


### Exported Functions


Functions exported to be used in configuration file.


#### `pua_xmpp_notify()`


Function that handles Notify messages addressed to a user from
		an xmpp domain. It requires filtering after method and domain in
		configuration file. If the function is successful, a 2xx reply must
		be sent.


This function can be used from REQUEST_ROUTE.


**Example: `Notify2Xmpp` usage**


```opensips
...
	if( is_method("NOTIFY") && $ru=~"sip:.+@sip-xmpp.siphub.ro")
	{
		if(Notify2Xmpp())
			t_reply(200, "OK");
		exit;
	}
...
```


#### `pua_xmpp_req_winfo(request_uri, expires)`


Function called when a Subscribe addressed to a user from a
		xmpp domain is received. It calls sending a Subscribe for 
		winfo for the user, and the following Notify with dialog-info
		is translated into a subscription in xmpp. 
		It also requires filtering in configuration file, after method, 
		domain and event(only for presence).


Parameters:


- *request_uri* (string)
- *expires* (int) - value of Expires header field 
				in received Subscribe.


This function can be used from REQUEST_ROUTE.


**Example: `xmpp_send_winfo` usage**


```opensips
...
	if( is_method("SUBSCRIBE"))
	{
		handle_subscribe();
		if($ru=~"sip:.+@sip-xmpp.siphub.ro" && $hdr(Event)== "presence")
		{
			pua_xmpp_req_winfo($ruri, $hdr(Expires));
		}
		t_release();
	}

...
		
```


### Filtering


Instead of "sip-xmpp.siphub.ro"  in the example you should use the value
	set for the xmpp module parameter named 'gateway_domain'.


## Developer Guide


The module provides no function to be used in other OpenSIPS modules.


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Anca Vamanu | 59 | 17 | 3287 | 806 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 16 | 13 | 59 | 81 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 14 | 12 | 24 | 41 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 14 | 11 | 40 | 63 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 10 | 8 | 27 | 21 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 4 | 34 | 69 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 3 | 1 | 14 | 2 |
| 8. | Sergio Gutierrez | 3 | 1 | 5 | 5 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 3 | 1 | 4 | 14 |
| 10. | Konstantin Bokarius | 3 | 1 | 3 | 5 |


**All remaining contributors**: Juha Heinanen ([@juha-h](https://github.com/juha-h)), Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Ken Rice, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Edson Gellert Schubert, Stanislaw Pitucha.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - May 2026 |
| 2. | Ken Rice | Sep 2025 - Sep 2025 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2007 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jan 2013 - Jan 2013 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Aug 2011 - Aug 2011 |
| 10. | Stanislaw Pitucha | Jul 2010 - Jul 2010 |


**All remaining contributors**: Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Anca Vamanu, Sergio Gutierrez, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Anca Vamanu, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert.


*Documentation Copyrights:*


Copyright © 2007 Voice Sistem SRL
