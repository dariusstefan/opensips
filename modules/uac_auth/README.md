---
title: "UAC AUTH Module"
description: "UAC AUTH (User Agent Client Authentication) module provides a common API for building authentication headers."
---

## Admin Guide


### Overview {#overview}


UAC AUTH (User Agent Client Authentication) module provides a
		common API for building authentication headers.


It also provides a common set of authentication credetials to
		be used by other modules.


Note that authentication provided by this module supports both
		qop "auth" and qop "auth-int" but if both values are presented
		by the server, "auth" will be prefered.


#### RFC 8760 Support (Strenghtened Authentication) {#rfc-8760-support}


Starting with OpenSIPS 3.2, the [auth](../auth),
			[auth_db](../auth_db) and
			[uac_auth](../uac_auth)
			modules include support for two new digest authentication algorithms
			("SHA-256" and "SHA-512-256"), according to the
	        [RFC 8760](https://datatracker.ietf.org/doc/html/rfc8760)
	        specs.


### Dependencies {#dependencies}


#### OpenSIPS Modules


- *None.*


#### External Libraries or Applications


The following libraries or applications must be installed 
				before running OpenSIPS with this module loaded:


- *None*


### Exported Parameters {#exported_parameters}


#### credential (string) {#param_credential}


Contains a multiple definition of credentials used to perform
			authentication.


NOTE that the password can be provided as a plain text password or
			as a precalculated HA1 as a hexa (lower case) string
			(of 32 chars) prefixed with "0x" (so a total of 34 chars).


*This parameter is required if UAC authentication is used.*


```c title="Set credential parameter"
...
modparam("uac_auth","credential","username:domain:password")
modparam("uac_auth","credential","username:domain:0xc17ba8157756f263d07e158504204629")
...
				
```


#### auth_realm_avp (string) {#param_auth_realm_avp}


The definition of an AVP that might contain the realm to be used
			to perform authentication.


*If you define it, you also need to define 
				"auth_username_avp" 
				([auth username avp](#param_auth_username_avp)) and 
				"auth_password_avp" 
				([auth password avp](#param_auth_password_avp)).*


```c title="Set auth_realm_avp parameter"
...
modparam("uac_auth","auth_realm_avp","$avp(10)")
...
				
```


#### auth_username_avp (string) {#param_auth_username_avp}


The definition of an AVP that might contain the username to be used
			to perform authentication.


*If you define it, you also need to define 
				"auth_realm_avp" 
				([auth realm avp](#param_auth_realm_avp)) and 
				"auth_password_avp" 
				([auth password avp](#param_auth_password_avp)).*


```c title="Set auth_username_avp parameter"
...
modparam("uac_auth","auth_username_avp","$avp(11)")
...
				
```


#### auth_password_avp (string) {#param_auth_password_avp}


The definition of an AVP that might contain the password to be used
			to perform authentication. The password can be provided as a plain
			text password or as a precalculated HA1 as a hexa (lower case) string
			(of 32 chars) prefixed with "0x" (so a total of 34 chars) (for example 
			"0xc17ba8157756f263d07e158504204629")


*If you define it, you also need to define 
				"auth_realm_avp" 
				([auth realm avp](#param_auth_realm_avp)) and 
				"auth_username_avp" 
				([auth username avp](#param_auth_username_avp)).*


```c title="Set auth_password_avp parameter"
...
modparam("uac_auth","auth_password_avp","$avp(12)")
...
				
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 16 | 7 | 869 | 15 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 11 | 8 | 236 | 10 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 9 | 7 | 40 | 61 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 9 | 3 | 121 | 258 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 8 | 11 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 6 | 4 | 44 | 30 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Mar 2012 - Apr 2023 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Aug 2021 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Sep 2020 - Mar 2021 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Sep 2011 - Jan 2021 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Jul 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jun 2011 - Oct 2013 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)).
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
