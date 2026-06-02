---
title: "UAC Module"
description: "UAC (User Agent Client) module provides some basic UAC functionalities like FROM / TO header manipulation (anonymization) or client authentication."
---

## Admin Guide


### Overview {#overview}


UAC (User Agent Client) module provides some basic UAC
		functionalities like FROM / TO header manipulation (anonymization)
		or client authentication.


If the dialog module is loaded and a dialog can be created, 
		then the auto mode can be done more efficiently.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *TM - Transaction Module*.
- *RR - Record-Route Module*, but only if
				restore mode for FROM URI is set to "auto".
- *UAC_AUTH - UAC Authentication Module*.
- *Dialog Module*, if "force_dialog"
				module parameter is enabled, or a dialog is created from the
				configuration script.


#### External Libraries or Applications


The following libraries or applications must be installed 
				before running OpenSIPS with this module loaded:


- *None*


### Exported Parameters {#exported_parameters}


#### restore_mode (string) {#param_restore_mode}


There are 3 mode of restoring the original headers (FROM/TO) URI:


- "none" - no information about original URI is 
				stored; restoration is not possible.
- "manual" - all following replies will be restored,
				except for the sequential requests - these must be manually 
				updated based on original URI.
- "auto" - all sequential requests and replies will 
				be automatically updated based on stored original URI.


*This parameter is optional, it's default value being 
					"auto".*


```c title="Set restore_mode parameter"
...
modparam("uac","restore_mode","auto")
...
				
```


#### restore_passwd (string) {#param_restore_passwd}


String password to be used to encrypt the RR storing parameter
			(when replacing the TO/FROM headers). If empty, no encryption 
			will be used.


*Default value of this parameter is empty.*


```c title="Set restore_passwd parameter"
...
modparam("uac","restore_passwd","my_secret_passwd")
...
				
```


#### rr_from_store_param (string) {#param_rr_from_store_param}


Name of Record-Route header parameter that will be used to store 
			(encoded) the original FROM URI.


*This parameter is optional, it's default value being 
					"vsf".*


```c title="Set rr_from_store_param parameter"
...
modparam("uac","rr_from_store_param","my_Fparam")
...
				
```


#### rr_to_store_param (string) {#param_rr_to_store_param}


Name of Record-Route header parameter that will be used to store 
			(encoded) the original TO URI.


*This parameter is optional, it's default value being 
					"vst".*


```c title="Set rr_to_store_param parameter"
...
modparam("uac","rr_to_store_param","my_Tparam")
...
				
```


#### force_dialog (int) {#param_force_dialog}


Force create dialog if it is not created from the configuration script.


Default value is no.


```c title="Set force_dialog parameter"
...
modparam("uac", "force_dialog", yes)
...
				
```


### Exported Functions {#exported_functions}


#### uac_replace_from([display],uri)
				uac_replace_to([display],uri) {#func_uac_replace_from}


Replace in FROM/TO header the *display* name or/and
			the *URI* part.


Both parameters are string. The *display* is optional.
			If missing, only the URI will be changed in the message.


IMPORTANT: calling the function more than once per branch will lead
			to inconsistent changes over the request.Be sure you do the change
			only ONCE per branch. Note that calling the function from REQUEST
			ROUTE affects all the branches!, so no other change will be 
			possible in the future. For per branch changes use BRANCH and 
			FAILURE route.


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE and
			FAILURE_ROUTE.


```c title="uac_replace_from/uac_replace_to usage"
...
# replace both display and uri
uac_replace_from($avp(display),$avp(uri));
# replace only display and do not touch uri
uac_replace_from("batman","");
# remove display and replace uri
uac_replace_from("","sip:robin@gotham.org");
# remove display and do not touch uri
uac_replace_from("","");
# replace the URI without touching the display
uac_replace_from( , "sip:batman@gotham.org");
...
				
```


#### uac_restore_from()
				uac_restore_to() {#func_uac_restore_from}


This function will check if the FROM/TO URI was modified and will
			use the information stored in header parameter to restore
			the original FROM/TO URI value.


NOTE - this function should be used only if you configured MANUAL
			restoring of the headers (see restore_mode param). For AUTO 
			and NONE, there is no need to use this function.


This function can be used from REQUEST_ROUTE.


```c title="uac_restore_from/uac_restore_to usage"
...
uac_restore_from();
...
				
```


#### uac_auth() {#func_uac_auth}


This function can be called only from failure route and will 
			build the authentication response header and insert it into the
			request without sending anything.
			Credentials for buiding the authentication response will be taken
			from the list of credentials provided by the uac_auth module (static
			or via AVPs).


As optional parameter, the function may receive a list of auth
			algorithms to be considered / supported during authentication:


- MD5, MD5-sess
- SHA-256, SHA-256-sess (may be missing, depends on lib support)
- SHA-512-256, SHA-512-256-sess (may be missing, depends on lib support)


Note that the CSeq is automatically increased during authentication.


This function can be used from FAILURE_ROUTE.


*NOTE:* when used without dialog support, the
				*uac_auth()* function cannot be used for authenticating
				in-dialog requests, as there is no mechanism to store the CSeq changes that
				are required for ensuring the correctness of the dialog. The only exception are
				*BYE* messages, which are the last messages within a call,
				hence no further adjustments are needed. The function can still be used for
				authenticating the initial INVITE though.


```c title="uac_auth usage"
...
uac_auth();
...
failure_route[check_auth] {
    ...
    if ($T_reply_code==407) {
        if (uac_auth("MD5,MD5-sess")) {
            # auth is succesful, just relay
            t_relay();
            exit;
        }
        # auth failed (no credentials maybe)
        # so continue handling the 407 reply
    }
    ...
}
...
				
```


## Frequently Asked Questions


**Q: What happened with auth_username_avp, auth_realm_avp and auth_password_avp parameters**


Due some restructuring of the UAC auth modules, these parameters were moved into the "uac_auth" module.
		This module is now responsible for handling all the credentials (static defined or dynamically defined 
		via AVPs). The UAC module will still see the credentials defined via the AVPs.


**Q: Where can I find more about OpenSIPS?**


Take a look at [https://opensips.org/](https://opensips.org/).


**Q: Where can I post a question about this module?**


First at all check if your question was already answered on one of
			our mailing lists:

E-mails regarding any stable OpenSIPS release should be sent to 
			users@lists.opensips.org and e-mails regarding development versions
			should be sent to devel@lists.opensips.org.

If you want to keep the mail private, send it to 
			users@lists.opensips.org.


**Q: How can I report a bug?**


Please follow the guidelines provided at:
			[https://github.com/OpenSIPS/opensips/issues](https://github.com/OpenSIPS/opensips/issues).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 128 | 75 | 4208 | 1076 |
| 2. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 35 | 8 | 403 | 1351 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 31 | 23 | 494 | 148 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 24 | 16 | 299 | 290 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 15 | 11 | 138 | 88 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 9 | 4 | 127 | 161 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 8 | 5 | 243 | 18 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 8 | 1 | 156 | 282 |
| 9. | Andreas Heise | 7 | 3 | 105 | 129 |
| 10. | Edson Gellert Schubert | 5 | 1 | 0 | 201 |


**All remaining contributors**: Elena-Ramona Modroiu, Henning Westerholt ([@henningw](https://github.com/henningw)), Konstantin Bokarius, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Dan Pascu ([@danpascu](https://github.com/danpascu)), Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Jesus Rodrigues, Sergio Gutierrez.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Mar 2011 - Jun 2023 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Aug 2010 - Apr 2023 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2005 - Apr 2023 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Feb 2023 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Mar 2021 - Mar 2021 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Jul 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Dusan Klinec ([@ph4r05](https://github.com/ph4r05)) | Dec 2015 - Dec 2015 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Aug 2011 - Sep 2015 |
| 10. | Sergio Gutierrez | Nov 2008 - Nov 2008 |


**All remaining contributors**: Dan Pascu ([@danpascu](https://github.com/danpascu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Jesus Rodrigues, Andreas Heise, Elena-Ramona Modroiu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Jesus Rodrigues, Elena-Ramona Modroiu.
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
