---
title: "PUA Bridged Line Appearances"
description: "The pua_bla module enables Bridged Line Appearances support according to the specifications in draft-anil-sipping-bla-03.txt."
---

## Admin Guide


### Overview {#overview}


The pua_bla module enables Bridged Line Appearances support according to 
		 the specifications in draft-anil-sipping-bla-03.txt.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *usrloc*.
- *pua*.
- *presence*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libxml*.


### Exported Parameters {#exported_parameters}


#### default_domain(str) {#param_default_domain}


The default domain for the registered users to be used when
		constructing the uri for the registrar callback.


*Default value is "NULL".*


```c title="Set default_domain parameter"
...
modparam("pua_bla", "default_domain", "opensips.org")
...
```


#### header_name(str) {#param_header_name}


The name of the header to be added to Publish requests.
		It will contain the uri of the user agent that sent the
		Notify that is transformed into Publish. It stops sending 
		a Notification with the same information to the sender.


*Default value is "NULL".*


```c title="Set header_name parameter"
...
modparam("pua_bla", "header_name", "Sender")
...
```


#### outbound_proxy(str) {#param_outbound_proxy}


The outbound_proxy uri to be used when sending Subscribe requests.


*Default value is "NULL".*


```c title="Set outbound_proxy parameter"
...
modparam("pua_bla", "outbound_proxy", "sip:proxy@opensips.org")
...
```


#### server_address(str) {#param_server_address}


The IP address of the server.


```c title="Set server_address parameter"
...
modparam("pua_bla", "server_address", "sip:bla@160.34.23.12")
...
```


#### presence_server(str) {#param_presence_server}


The address of the presence server - will be used as
			an outbound proxy when sending PUBLISH requests. 
			It is optional.


*Default value is "NULL".*


```c title="Set presence_server parameter"
...
modparam("pua_bla", "presence_server", "sip:pa@opensips.org")
...
```


### Exported Functions {#exported_functions}


#### bla_set_flag() {#func_bla_set_flag}


The function is used to mark REGISTER requests made to a BLA AOR.
				The modules subscribes to the registered contacts for dialog;sla 
				event.


```c title="bla_set_flag usage"
...
if(is_method("REGISTER") && $tu=~"bla_aor@opensips.org") 
	bla_set_flag();		
...
```


#### bla_handle_notify() {#func_bla_handle_notify}


The function handles Notify requests sent from phones on the
				same BLA to the server. The message is transformed in Publish 
				request and passed to presence module for further handling.
				in case of a successful processing a 2xx reply should be sent.


```c title="bla_handle_notify usage"
...
if(is_method("NOTIFY") && $tu=~"bla_aor@opensips.org") 
{
		if( bla_handle_notify() ) 
			t_reply(200, "OK");
}	
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Anca Vamanu | 57 | 27 | 1961 | 779 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 17 | 14 | 58 | 73 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 14 | 12 | 42 | 60 |
| 4. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 9 | 7 | 17 | 15 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 12 | 13 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 7 | 5 | 20 | 18 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 4 | 2 | 15 | 4 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 4 | 2 | 2 | 2 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 3 | 1 | 6 | 16 |
| 10. | Sergio Gutierrez | 3 | 1 | 4 | 4 |


**All remaining contributors**: Maksym Sobolyev ([@sobomax](https://github.com/sobomax)), Konstantin Bokarius, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)), Ken Rice, Edson Gellert Schubert, Stanislaw Pitucha.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ken Rice | Sep 2025 - Sep 2025 |
| 2. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jul 2025 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Jan 2023 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2007 - Apr 2019 |
| 8. | Ezequiel Lovelle ([@lovelle](https://github.com/lovelle)) | Oct 2014 - Oct 2014 |
| 9. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Dec 2010 - Jan 2013 |
| 10. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Aug 2011 - Aug 2011 |


**All remaining contributors**: Anca Vamanu, Stanislaw Pitucha, Sergio Gutierrez, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Anca Vamanu, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert.
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
