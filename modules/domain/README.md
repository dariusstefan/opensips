---
title: "domain Module"
description: "Domain module implements checks that based on domain table determine if a host part of an URI is \"local\" or not. A \"local\" domain is one that the proxy is responsible for."
---

## Admin Guide


### Overview {#overview}


Domain module implements checks that based on domain table determine 
		if a host part of an URI is "local" or 
		not.  A "local" domain is one that the proxy is responsible 
		for.


Domain module operates in caching or non-caching mode depending on 
		value of module parameter `db_mode`.
		In caching mode domain module reads the contents of domain table into 
		cache memory when the module is loaded.  After that domain table is 
		re-read only when module is given domain_reload fifo command.  Any
		changes in domain table must thus be followed by 
		"domain_reload" command in order to reflect them in 
		module behavior. In non-caching mode domain module always queries domain
		table in the database.


Caching is implemented using a hash table. The size of the hash table 
		is given by HASH_SIZE constant defined in domain_mod.h. 
		Its "factory default" value is 128.


### Dependencies {#dependencies}


The module depends on the following modules (in the other words the 
		listed modules must be loaded before this module):


- *database* -- Any database module


### Exported Parameters {#exported_parameters}


#### db_url (string)


This is URL of the database to be used.


Default value is 
			"mysql://opensipsro:opensipsro@localhost/opensips"


```c title="Setting db_url parameter"
modparam("domain", "db_url", "mysql://ser:pass@db_host/ser")
```


#### db_mode (integer)


Database mode: 0 means non-caching, 1 means caching.


Default value is 0 (non-caching).


```c title="db_mode example"
modparam("domain", "db_mode", 1)   # Use caching
```


#### domain_table (string)


Name of table containing names of local domains that the proxy is 
		responsible for. Local users must have in their sip uri a host part 
		that is equal to one of these domains.


Default value is "domain".


```c title="Setting domain_table parameter"
modparam("domain", "domain_table", "new_name")
```


#### domain_col (string)


Name of column containing domains in domain table.


Default value is "domain".


```c title="Setting domain_col parameter"
modparam("domain", "domain_col", "domain_name")
```


#### attrs_col (string)


Name of column containing attributes in domain table.


Default value is "attrs".


```c title="Setting attrs_col parameter"
modparam("domain", "attrs_col", "attributes")
```


### Exported Functions {#exported_functions}


#### is_from_local([attrs_var]) {#func_is_from_local}


Checks based on domain table if host part of From header uri is
		one of the local domains that the proxy is responsible for.
		The argument is optional and if present it should contain a writable
		variable that will be populated with the attributes from the
		database.


This function can be used from REQUEST_ROUTE.


```c title="is_from_local usage"
...
if (is_from_local()) {
	...
};
...
if (is_from_local($var(attrs))) {
	xlog("Domain attributes are $var(attrs)\n");
	...
};
...
		
```


#### is_uri_host_local([attrs_var]) {#func_is_uri_host_local}


If called from route or failure route block, checks
		based on domain table if host part of Request-URI is one
		of the local domains that the proxy is responsible for.
		If called from branch route, the test is made on host
		part of URI of first branch, which thus must have been
		appended to the transaction before is_uri_host_local()
		is called.
		The argument is optional and if present it should contain a writable
		variable that will be populated with the attributes from the
		database.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE,
		BRANCH_ROUTE.


```c title="is_uri_host_local usage"
...
if (is_uri_host_local()) {
	...
};
...
if (is_uri_host_local($var(attrs))) {
	xlog("Domain attributes are $var(attrs)\n");
	...
};
		
```


#### is_domain_local(domain, [attrs_var]) {#func_is_domain_local}


This function checks if the domain contained in the first parameter is local.


This function is a generalized form of the is_from_local()
		and is_uri_host_local() functions, being able to completely
		replace them and also extends them by allowing the domain to
		be taken from any of the above mentioned sources.
                The following equivalences exist:


- is_domain_local($rd) is same as is_uri_host_local()
- is_domain_local($fd) is same as is_from_local()


Parameters:


- *domain* (string)
- *attrs_var* (var, optional) - a writable
				variable that will be populated with the attributes from the
				database.


This function can be used from REQUEST_ROUTE, FAILURE_ROUTE,
		BRANCH_ROUTE.


```c title="is_domain_local usage"
...
if (is_domain_local($rd)) {
	...
};
if (is_domain_local($fd)) {
	...
};
if (is_domain_local($avp(some_avp_alias))) {
	...
};
if (is_domain_local($avp(850))) {
	...
};
if (is_domain_local($avp(some_avp))) {
	...
};
if (is_domain_local($avp(some_avp), $avp(attrs))) {
	xlog("Domain attributes are $avp(attrs)\n");
	...
};
...
		
```


### Exported MI Functions {#exported_mi_functions}


#### domain_reload {#mi_domain_reload}


Causes domain module to re-read the contents of domain table
		into cache memory.


Name: *domain_reload*


Parameters: *none*


MI FIFO Command Format:


```c
		opensips-cli -x mi domain_reload
		
```


#### domain_dump {#mi_domain_dump}


Causes domain module to dump hash indexes and domain names in
		its cache memory.


Name: *domain_dump*


Parameters: *none*


MI FIFO Command Format:


```c
		opensips-cli -x mi domain_dump
		
```


### Known Limitations


There is an unlikely race condition on domain list update.  If a 
		process uses a table, which is reloaded at the same time twice 
		through FIFO, the second reload will delete the 
		original table still in use by the process.


## Developer Guide


The module provides is_domain_local API
    function for use by other OpenSIPS modules.


### Available Functions


#### is_domain_local(domain)


Checks if domain given in str* parameter is local.


The function returns 1 if domain is local and -1 if
		domain is not local or if an error occurred.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 55 | 41 | 425 | 559 |
| 2. | Jan Janak ([@janakj](https://github.com/janakj)) | 32 | 21 | 999 | 113 |
| 3. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | 30 | 20 | 700 | 233 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 20 | 13 | 287 | 221 |
| 5. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 19 | 16 | 92 | 79 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 11 | 8 | 45 | 109 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 9 | 5 | 96 | 123 |
| 8. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 8 | 4 | 232 | 101 |
| 9. | Andrei Pelinescu-Onciul | 8 | 4 | 186 | 121 |
| 10. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 7 | 5 | 44 | 48 |


**All remaining contributors**: Edson Gellert Schubert, Elena-Ramona Modroiu, Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)), [@coxx](https://github.com/coxx), Konstantin Bokarius, Klaus Darilion, Anca Vamanu, Norman Brandinger ([@NormB](https://github.com/NormB)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), UnixDev, Andreas Granig, John Burke ([@john08burke](https://github.com/john08burke)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | John Burke ([@john08burke](https://github.com/john08burke)) | Jan 2022 - Jan 2022 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2005 - Mar 2020 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jun 2011 - Sep 2019 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Nov 2018 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | [@coxx](https://github.com/coxx) | Mar 2010 - Mar 2010 |
| 8. | Anca Vamanu | Sep 2009 - Sep 2009 |
| 9. | UnixDev | Feb 2009 - Feb 2009 |
| 10. | Juha Heinanen ([@juha-h](https://github.com/juha-h)) | Jan 2003 - May 2008 |


**All remaining contributors**: Henning Westerholt ([@henningw](https://github.com/henningw)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Elena-Ramona Modroiu, Dan Pascu ([@danpascu](https://github.com/danpascu)), Norman Brandinger ([@NormB](https://github.com/NormB)), Andreas Granig, Klaus Darilion, Jan Janak ([@janakj](https://github.com/janakj)), Andrei Pelinescu-Onciul, Jiri Kuthan ([@jiriatipteldotorg](https://github.com/jiriatipteldotorg)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Juha Heinanen ([@juha-h](https://github.com/juha-h)), Elena-Ramona Modroiu, Dan Pascu ([@danpascu](https://github.com/danpascu)), Klaus Darilion, Jan Janak ([@janakj](https://github.com/janakj)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
