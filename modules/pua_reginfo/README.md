---
title: "pua_reginfo Module"
description: "This module publishes information about \"reg\"-events according to to RFC 3680. This can be used distribute the registration-info status to the subscribed watchers."
---

## Admin Guide


### Overview


This module publishes information about "reg"-events according to
              to RFC 3680. This can be used distribute the registration-info
              status to the subscribed watchers.


This module "PUBLISH"es information when a new user registers
              at this server (e.g. when "save()" is called) to users, which have
              subscribed for the reg-info for this user.


This module can "SUBSCRIBE" for information at another server, so it
              will receive "NOTIFY"-requests, when the information about a user
              changes.


And finally, it can process received "NOTIFY" requests and it will 
              update the local registry accordingly.


Use cases for this might be:


- 
- 
- 
- 


### Dependencies


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *pua*.
- *usrloc*.


#### External Libraries or Applications


None.


### Parameters


#### `default_domain`(str)


The default domain for the registered users to be used when
		constructing the uri for the registrar callback.


*Default value is "NULL".*


**Example: Set `default_domain` parameter**


```opensips
...
modparam("pua_reginfo", "default_domain", "kamailio.org")
...
```


#### `publish_reginfo`(int)


Whether or not to generate PUBLISH requests.


*Default value is "1" (enabled).*


**Example: Set `publish_reginfo` parameter**


```opensips
...
modparam("pua_reginfo", "publish_reginfo", 0)
...
```


#### `outbound_proxy`(str)


The outbound_proxy uri to be used when sending Subscribe and Publish requests.


*Default value is "NULL".*


**Example: Set `outbound_proxy` parameter**


```opensips
...
modparam("pua_reginfo", "outbound_proxy", "sip:proxy@kamailio.org")
...
```


#### `server_address`(str)


The IP address of the server.


**Example: Set `server_address` parameter**


```opensips
...
modparam("pua_reginfo", "server_address", "sip:reginfo@160.34.23.12")
...
```


#### `ul_domain`(str)


The domain for for querying the usrloc-database.


*Default value is "NULL" (not set).*


**Example: Set `ul_domain` parameter**


```opensips
...
modparam("pua_reginfo", "ul_domain", "location")
...
```


#### `ul_identities_key`(str)


The Key, which may be used for retrieving multiple public identies
		for a user.


*Default value is "NULL" (not set).*


**Example: Set `ul_identities_key` parameter**


```opensips
...
modparam("pua_reginfo", "ul_identities_key", "identities")
...
onreply_route[register_reply] {
	if (t_check_status("200") && $hdr(P-Associated-URI)) {
        ul_add_key("location", "$tU@$td", "identities", "$hdr(P-Associated-URI)");
        reginfo_update("$tU@$td");
	}
}

...
		
```


### Functions


#### `reginfo_handle_notify(uldomain)`


This function processes received "NOTIFY"-requests and updates
				the local registry accordingly.


This method does not create any SIP-Response, this has to be done
				by the script-writer.


The parameter has to correspond to user location table (domain)
				where to store the record.


Return codes:


- *2* - contacts successfully updated,
				but no more contacts online now.
*1* - contacts successfully updated and at
				at least one contact still registered.
*-1* - Invalid NOTIFY or other error (see log-file)


**Example: `reginfo_handle_notify` usage**


```opensips
...
if(is_method("NOTIFY")) 
	if (reginfo_handle_notify("location"))
		send_reply("202", "Accepted");
...
				
```


#### `reginfo_subscribe(uri[, expires])`


This function will subscribe for reginfo-information at the given
				server URI.


Meaning of the parameters is as follows:


- *uri* - SIP-URI of the server, where to subscribe,
				may contain pseudo-variables.
*expires* - Expiration date for this subscription, in seconds (default 3600)


**Example: `reginfo_subscribe` usage**


```opensips
...
route {
	t_on_reply("1");
	t_relay();
}

reply_route[1] {
	if (t_check_status("200")) 
		reginfo_subscribe("$ru");		
}
...
				
```


#### `reginfo_update(aor)`


Explicitly update the presence status, e.g., when new information
				is learned. This may trigger a new NOTIFY towards subscribed
				entities; at least it will update the internal information for
				subsequent subscribe and notifies.


This is done implicitly, when a registration is updated. However,
				when a registration was just updated with additional information like
				identities, this is not triggered automatically.


Meaning of the parameters is as follows:


- *aor* - The AOR to be updated.


**Example: `reginfo_subscribe` usage**


```opensips
...
modparam("pua_reginfo", "ul_domain", "location")
modparam("pua_reginfo", "ul_identities_key", "identities")
...
onreply_route[register_reply] {
	if (t_check_status("200") && $hdr(P-Associated-URI)) {
        ul_add_key("location", "$tU@$td", "identities", "$hdr(P-Associated-URI)");
        reginfo_update("$tU@$td");
	}
}

...
				
```


## Contributors


### By Commit Statistics


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Carsten Bock | 18 | 1 | 1930 | 0 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 16 | 19 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 4 | 2 | 3 | 4 |
| 4. | Ken Rice | 3 | 1 | 2 | 2 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 2 | 1 | 1 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Apr 2024 - Apr 2026 |
| 2. | Ken Rice | Sep 2025 - Sep 2025 |
| 3. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Feb 2025 - Feb 2025 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jan 2025 - Jan 2025 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2024 - Apr 2024 |
| 6. | Carsten Bock | Mar 2024 - Mar 2024 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation


### Contributors


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Carsten Bock.


*Documentation Copyrights:*


Copyright © 2011-2023 Carsten Bock, carsten@ng-voice.com, http://www.ng-voice.com
