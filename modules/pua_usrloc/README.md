---
title: "PUA Usrloc"
description: "The pua_usrloc is the connector between usrloc and pua modules. It creates the environment to send PUBLISH requests for user location records, on specific events (e.g., when new record is added in usrloc, a PUBLISH with status open (online) is issued; when expires, it sends closed (of..."
---

## Admin Guide


### Overview {#overview}


The pua_usrloc is the connector between usrloc and pua modules.
		 It creates the environment to send PUBLISH requests for user
		 location records, on specific events (e.g., when new record is
		 added in usrloc, a PUBLISH with status open (online) is issued;
		 when expires, it sends closed (offline)).


Using this module, phones which have no support for presence can
		be seen as online/offline.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *usrloc*.
- *pua*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *libxml*.


### Exported Parameters {#exported_parameters}


#### default_domain (str) {#param_default_domain}


The default domain to use when constructing the presentity
		uri if it is missing from recorded aor.


*Default value is "NULL".*


```c title="Set default_domain parameter"
...
modparam("pua_usrloc", "default_domain", "opensips.org")
...
```


#### entity_prefix (str) {#param_entity_prefix}


The prefix when construstructing entity attribute to be added to
		presence node in xml pidf.
		(ex: pres:user@domain ).


*Default value is "NULL".*


```c title="Set presentity_prefix parameter"
...
modparam("pua_usrloc", "entity_prefix", "pres")
...
```


#### presence_server (str) {#param_presence_server}


The the address of the presence server. If set, it will be
		used as outbound proxy when sending PUBLISH requests.


```c title="Set presence_server parameter"
...
modparam("pua_usrloc", "presence_server", "sip:pa@opensips.org:5075")
...
	
```


### Exported Functions {#exported_functions}


#### pua_set_publish() {#func_pua_set_publish}


The function is used to mark REGISTER requests that have to
				issue a PUBLISH. The PUBLISH is issued when REGISTER is saved
				in location table.


```c title="pua_set_publish usage"
...
if(is_method("REGISTER") && $fu=~"john@opensips.org") 
	pua_set_publish();
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Anca Vamanu | 35 | 17 | 1245 | 365 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 16 | 14 | 54 | 57 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 15 | 13 | 46 | 60 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 13 | 11 | 14 | 22 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 10 | 8 | 25 | 19 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 5 | 3 | 14 | 7 |
| 7. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | 4 | 2 | 14 | 2 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 4 | 2 | 5 | 4 |
| 9. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 4 | 4 |
| 10. | Konstantin Bokarius | 3 | 1 | 2 | 5 |


**All remaining contributors**: Elena-Ramona Modroiu, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Ken Rice, Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Edson Gellert Schubert, Julien Blache.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ken Rice | Sep 2025 - Sep 2025 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Jan 2023 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Feb 2020 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Feb 2007 - Apr 2019 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 8. | Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)) | Jan 2013 - Mar 2014 |
| 9. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2010 - Apr 2010 |
| 10. | Anca Vamanu | Nov 2006 - Oct 2009 |


**All remaining contributors**: Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Julien Blache, Elena-Ramona Modroiu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Anca Vamanu, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Elena-Ramona Modroiu.
## License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
