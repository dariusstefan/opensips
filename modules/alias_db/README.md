---
title: "ALIAS_DB Module"
description: "ALIAS_DB module can be used as an alternative for user aliases via usrloc. The main feature is that it does not store all adjacent data as for user location and always uses database for search (no memory caching)."
---

## Admin Guide


### Overview {#overview}


ALIAS_DB module can be used as an alternative for user aliases
	via usrloc. The main feature is that it does not store all adjacent
	data as for user location and always uses database for search (no
	memory caching).


Having no memory caching, search speed might decrease but 
	provisioning is easier. With very fast databases like MySQL, speed
	penalty can be lowered. Also, search can be performed on different
	tables in the same script.


### Dependencies {#dependenciesid}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *database module* (mysql, dbtext, ...).


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### db_url (str) {#param_db_url}


Database URL.


*Default value is "mysql://opensipsro:opensipsro@localhost/opensips".*


```opensips title="Set db_url parameter"
...
modparam("alias_db", "db_url", "dbdriver://username:password@dbhost/dbname")
...
```


#### user_column (str) {#param_user_column}


Name of the column storing username.


*Default value is "username".*


```opensips title="Set user_column parameter"
...
modparam("alias_db", "user_column", "susername")
...
```


#### domain_column (str) {#param_domain_column}


Name of the column storing user's domain.


*Default value is "domain".*


```opensips title="Set domain_column parameter"
...
modparam("alias_db", "domain_column", "sdomain")
...
```


#### alias_user_column (str) {#param_alias_user_column}


Name of the column storing alias username.


*Default value is "alias_username".*


```opensips title="Set alias_user_column parameter"
...
modparam("alias_db", "alias_user_column", "auser")
...
```


#### alias_domain_column (str) {#param_alias_domain_column}


Name of the column storing alias domain.


*Default value is "alias_domain".*


```opensips title="Set alias_domain_column parameter"
...
modparam("alias_db", "alias_domain_column", "adomain")
...
```


#### domain_prefix (str) {#param_domain_prefix}


Specifies the prefix to be stripped from the domain in R-URI before
		doing the search.


*Default value is "NULL".*


```opensips title="Set domain_prefix parameter"
...
modparam("alias_db", "domain_prefix", "sip.")
...
```


#### append_branches (int) {#param_append_branches}


If the alias resolves to many SIP IDs, the first is replacing
			the R-URI, the rest are added as branches.


*Default value is "0" (0 - don't add branches;
			1 - add branches).*


```opensips title="Set append_branches parameter"
...
modparam("alias_db", "append_branches", 1)
...
```


### Exported Functions {#exported_functions}


#### alias_db_lookup(table_name, [flags]) {#func_alias_db_lookup}


The function takes the R-URI and search to see whether it is an alias
		or not. If it is an alias for a local user, the R-URI is replaced with
		user's SIP uri.


The function returns TRUE if R-URI is alias and it was replaced by
		user's SIP uri.


Meaning of the parameters is as follows:


- *table_name (string)* - the name of the
				table to search for the alias
- *flags (string, optional)* - set of
			character flags to control the alias lookup process:

  - **d** - do not use domain URI part in
				the alias lookup query (use only a username-based lookup). By
				default, both username and domain are used.
  - **r** - do reverse alias lookup - lookup
				for the alias mapped to the current URI (URI 2 alias 
				translation); normally, the function looks up for the URI 
				mapped to the alias (alias 2 URI translation).


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE.


```c title="alias_db_lookup() usage"
...
alias_db_lookup("dbaliases", "rd");
alias_db_lookup("dba_$(rU{s.substr,0,1})");
...
```


#### alias_db_find(table_name, input_uri, output_var, [flags]) {#func_alias_db_find}


The function is very similar to `alias_db_lookup()`,
		but instead of using fixed input (RURI) and output (RURI) is able to
		get the input SIP URI from a pseudo-variable and place the result back
		also in a pseudo-variable.


The function is useful as the alias lookup does not affect the request
		itself (no RURI changes), can be used in a reply context (as it does 
		not work with RURI only) and can be used for others URI than the RURI
		(To URI, From URI, custom URI).


The function returns TRUE if any alias mapping was found and returned.


Meaning of the parameters is as follows:


- *table_name (string)* - the name of the table to
				search for the alias
- *input_uri (string)* - a SIP URI to look up
- *output_var (var)* - a variable to hold
				the SIP URI result
- *flags (string, optional)* (optional) - set of flags 
			(char based flags) to control the alias lookup process:

  - *d* - do not use domain URI part in
				the alias lookup query (use only a username-based lookup). By
				default, both username and domain are used.
  - *r* - do revers alias lookup - lookup
				for the alias mapped to the current URI (URI 2 alias 
				translation); normally, the function looks up for the URI 
				mapped to the alias (alias 2 URI translation).


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE,
			LOCAL_ROUTE, STARTUP_ROUTE, FAILURE_ROUTE and ONREPLY_ROUTE.


```c title="alias_db_find() usage"
...
# do revers alias lookup and find the alias for the FROM URI
alias_db_find("dbaliases", $fu, $avp(from_alias), "r");
...
```


## Frequently Asked Questions


**Q: What happened with old use_domain parameter**


The global parameter (affecting the entire module) was replaced 
			with a per lookup parameter (affecting only current lookup).
			See the "d" (do not used domain part) flag in the db_alias_lookup()
			and db_alias_find() functions.


**Q: How can I report a bug?**


Please follow the guidelines provided at:
			[https://github.com/OpenSIPS/opensips/issues](https://github.com/OpenSIPS/opensips/issues).


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 32 | 27 | 216 | 170 |
| 2. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 15 | 12 | 28 | 64 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 13 | 10 | 48 | 64 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 10 | 8 |
| 5. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 6 | 4 | 48 | 49 |
| 6. | Elena-Ramona Modroiu | 5 | 3 | 92 | 39 |
| 7. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | 5 | 3 | 7 | 7 |
| 8. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 5 | 2 | 38 | 88 |
| 9. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 5 |
| 10. | Vladimir Romanov | 4 | 1 | 227 | 51 |


**All remaining contributors**: Sergey Khripchenko ([@shripchenko](https://github.com/shripchenko)), Sergio Gutierrez, Konstantin Bokarius, Anca Vamanu, Dusan Klinec ([@ph4r05](https://github.com/ph4r05)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2010 - Apr 2021 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2005 - Mar 2020 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Sep 2019 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 8. | Dusan Klinec ([@ph4r05](https://github.com/ph4r05)) | Dec 2015 - Dec 2015 |
| 9. | Sergey Khripchenko ([@shripchenko](https://github.com/shripchenko)) | Sep 2015 - Sep 2015 |
| 10. | Anca Vamanu | Sep 2009 - Sep 2009 |


**All remaining contributors**: Vladimir Romanov, Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Elena-Ramona Modroiu, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Sergey Khripchenko ([@shripchenko](https://github.com/shripchenko)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Elena-Ramona Modroiu, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert.


*Documentation Copyrights:*


Copyright © 2005-2009 Voice Sistem SRL
