---
title: "group Module"
description: "This module provides functionalities for different methods of group membership checking."
---

## Admin Guide


### Overview {#overview}


This module provides functionalities for different methods of group
		membership checking.


#### Strict membership checking


There is a database table that contains list of users and groups
			they belong to. The module provides the possibility to check if a
			specific user belongs to a specific group.


There is no DB caching support, each check involving a DB query.


#### Regular Expression based checking


Another database table contains list of regular expressions and
			group IDs. A matching occurs if the user URI match the regular
			expression. This type of matching may be used to fetch the
			group ID(s) the user belongs to (via RE matching) .


Due performance reasons (regular expression evaluation), DB cache
			support is available: the table content is loaded into memory at
			startup and all regular expressions are compiled.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- A database module, like mysql, postgres or dbtext.
- An AAA module, like radius or diameter.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### db_url (string) {#param_db_url}


URL of the database table to be used.


```c title="Set db_url parameter"
...
modparam("group", "db_url", "mysql://username:password@dbhost/opensips")
...
```


#### table (string) {#param_table}


Name of the table holding strict definitions of groups and
		their members.


*Default value is "grp".*


```c title="Set table parameter"
...
modparam("group", "table", "grp_table")
...
```


#### user_column (string) {#param_user_column}


Name of the "table" column holding usernames.


*Default value is "username".*


```c title="Set user_column parameter"
...
modparam("group", "user_column", "user")
...
```


#### domain_column (string) {#param_domain_column}


Name of the "table" column holding domains.


*Default value is "domain".*


```c title="Set domain_column parameter"
...
modparam("group", "domain_column", "realm")
...
```


#### group_column (string) {#param_group_column}


Name of the "table" column holding groups.


*Default value is "grp".*


```c title="Set group_column parameter"
...
modparam("group", "group_column", "grp")
...
```


#### use_domain (integer) {#param_use_domain}


If enabled (set to non zero value) then domain will be used also used
		for strict group matching; otherwise only the username part will be
		used.


*Default value is 0 (no).*


```c title="Set use_domain parameter"
...
modparam("group", "use_domain", 1)
...
```


#### re_table (string) {#param_re_table}


Name of the table holding definitions for regular-expression
		based groups. If no table is defined, the regular-expression
		support is disabled.


*Default value is "NULL".*


```c title="Set re_table parameter"
...
modparam("group", "re_table", "re_grp")
...
```


#### re_exp_column (string) {#param_re_exp_column}


Name of the "re_table" column holding the regular
		expression used for user matching.


*Default value is "reg_exp".*


```c title="Set re_exp_column parameter"
...
modparam("group", "re_exp_column", "re")
...
```


#### re_gid_column (string) {#param_re_gid_column}


Name of the "re_table" column holding the group IDs.


*Default value is "group_id".*


```c title="Set re_gid_column parameter"
...
modparam("group", "re_gid_column", "grp_id")
...
```


#### multiple_gid (integer) {#param_multiple_gid}


If enabled (non zero value) the regular-expression matching will
		return all group IDs that match the user; otherwise only the first
		will be returned.


*Default value is "1".*


```c title="Set multiple_gid parameter"
...
modparam("group", "multiple_gid", 0)
...
```


#### aaa_url (string) {#param_aaa_url}


This is the url representing the AAA protocol used and the location of the configuration file of this protocol.


```c title="Set aaa_url parameter"
...
modparam("group", "aaa_url", "radius:/etc/radiusclient-ng/radiusclient.conf")
...
```


### Exported Functions {#exported_functions}


#### db_is_user_in(uri, group) {#func_db_is_user_in}


This function is to be used for script group membership. The function
		returns true if username in the given URI is member of the given
		group and false if not.


Meaning of the parameters is as follows:


- *uri (string)* - a SIP URI whose
				username and optionally domain to be used.  Possible values:
			
				
				"Request-URI" - Use Request-URI username and
				(optionally) domain.
				
				
				"To" - Use To username and (optionally) domain.
				
				
				"From" - Use From username and (optionally) domain.
				
				
				"Credentials" - Use digest credentials username.
				
				
				(default) - parse the given input as a SIP URI
- *group (string)* - the group to check


This function can be used from REQUEST_ROUTE and FAILURE_ROUTE.


```c title="db_is_user_in usage"
...
if (db_is_user_in("Request-URI", "ld")) {
	...
}
...
$avp(grouptocheck)="offline";

if (db_is_user_in("Credentials", $avp(grouptocheck))) {
	...
}
...
```


#### db_get_user_group(uri, output_avp) {#func_db_get_user_group}


This function is to be used for regular expression based group
		membership, using DB support.  The function returns true if the username in
		the given "uri" belongs to at least one group.


All matching group IDs
		shall be returned in "output_avp" if [multiple gid](#param_multiple_gid)
		is enabled, otherwise only the first one to match (the records are
		attempted in reversed order of the results returned by the RDBMS).


Meaning of the parameters is as follows:


- *uri (string)* - a SIP URI to be matched
				against the regular expressions:
			
				
				"Request-URI" - Use Request-URI
				
				
				"To" - Use To URI.
				
				
				"From" - Use From URI
				
				
				"Credentials" - Use digest credentials username
				and realm.
				
				
				(default) - parse the given input as a SIP URI
- *output_avp (var)* - a list of matched
				group IDs


This function can be used from REQUEST_ROUTE and FAILURE_ROUTE.


```c title="db_get_user_group usage"
...
if (db_get_user_group("Request-URI", $avp(10))) {
    xdbg("User $ru belongs to the following groups: $(avp(10)[*])\n");
    ....
};
...
```


#### aaa_is_user_in(uri, group) {#func_aaa_is_user_in}


This function checks group membership, using AAA support.
		The function returns true if username in the given "uri" is member of
		the given group and false if not.


Meaning of the parameters is as follows:


- *uri (string)* - a SIP URI whose
				username and optionally domain to be used, this can be one of:
			
				
				"Request-URI" - Use Request-URI username and
				(optionally) domain.
				
				
				"To" - Use To username and (optionally) domain.
				
				
				"From" - Use From username and (optionally) domain.
				
				
				"Credentials" - Use digest credentials username.
- *group (string)* - Name of the group to check.


This function can be used from REQUEST_ROUTE.


```c title="aaa_is_user_in usage"
...
if (aaa_is_user_in("Request-URI", "ld")) {
	...
};
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 45 | 31 | 848 | 397 |
| 2. | Jan Janak ([@janakj](https://github.com/janakj)) | 35 | 20 | 1370 | 148 |
| 3. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 24 | 18 | 176 | 226 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 15 | 11 | 87 | 152 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 9 | 7 | 16 | 39 |
| 6. | Irina-Maria Stanescu | 9 | 4 | 466 | 59 |
| 7. | Andrei Pelinescu-Onciul | 7 | 5 | 101 | 40 |
| 8. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 7 | 5 | 27 | 46 |
| 9. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 5 | 2 | 78 | 106 |
| 10. | Edson Gellert Schubert | 5 | 1 | 0 | 206 |


**All remaining contributors**: Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Walter Doekes ([@wdoekes](https://github.com/wdoekes)), Dan Pascu ([@danpascu](https://github.com/danpascu)), Sergio Gutierrez, Konstantin Bokarius, Norman Brandinger ([@NormB](https://github.com/NormB)), UnixDev, Anca Vamanu.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Jun 2021 |
| 2. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | Apr 2021 - Apr 2021 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2020 |
| 4. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - May 2020 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jul 2005 - Mar 2020 |
| 6. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | Oct 2007 - Apr 2019 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 8. | Irina-Maria Stanescu | Aug 2009 - Dec 2009 |
| 9. | Anca Vamanu | Sep 2009 - Sep 2009 |
| 10. | UnixDev | Feb 2009 - Feb 2009 |


**All remaining contributors**: Sergio Gutierrez, Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Norman Brandinger ([@NormB](https://github.com/NormB)), Jan Janak ([@janakj](https://github.com/janakj)), Andrei Pelinescu-Onciul, Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Irina-Maria Stanescu, Sergio Gutierrez, Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Dan Pascu ([@danpascu](https://github.com/danpascu)), Jan Janak ([@janakj](https://github.com/janakj)).


*Documentation Copyrights:*


Copyright © 2009 Voice Sistem SRL


Copyright © 2003 FhG FOKUS
