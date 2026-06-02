---
title: "UAC_REDIRECT Module"
description: "UAC REDIRECT - User Agent Client redirection - module enhance OpenSIPS with the functionality of being able to handle (interpret, filter, log and follow) redirect responses ( 3xx replies class)."
---

## Admin Guide


### Overview {#overview}


UAC REDIRECT - User Agent Client redirection - module enhance OpenSIPS
		with the functionality of being able to handle (interpret, filter,
		log and follow) redirect responses ( 3xx replies class).


UAC REDIRECT module offer stateful processing, gathering the
		contacts from all 3xx branches of a call.


The module provide a powerful mechanism for selecting and filtering 
		the contacts to be used for the new redirect:


- *number based* - limits like the 
			number of total contacts to be used or the maximum number of 
			contacts per branch to be selected.
- *Regular Expression based* - combinations
			of deny and accept filters allow a strict control of the 
			contacts to be used for redirection.


When selecting from a 3xx branch the contacts to be used, the contacts
		will be ordered and prioritized based on the "q" value.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *TM* - Transaction Module, for accessing
				replies.
- *ACC* - Accounting Module, but only if the
				logging feature is used.


#### External Libraries or Applications


The following libraries or applications must be installed 
				before running OpenSIPS with this module loaded:


- *None*


### Exported Parameters {#exported_parameters}


#### default_filter (string) {#param_default_filter}


The default behavior in filtering contacts. It may be 
			"accept" or "deny".


*The default value is "accept".*


```c title="Set default_filter module parameter"
...
modparam("uac_redirect","default_filter","deny")
...
				
```


#### deny_filter (string) {#param_deny_filter}


The regular expression for default deny filtering. It make sens
			to be defined on only if the `default_filter`
			parameter is set to "accept". All contacts matching
			the `deny_filter` will be rejected; the rest 
			of them will be accepted for redirection.


The parameter may be defined only one - multiple definition will
			overwrite the previous definitions. If more regular expression 
			need to be defined, use the 
			`set_deny_filter()` scripting
			function.


*This parameter is optional, it's default 
					value being NULL.*


```c title="Set deny_filter module parameter"
...
modparam("uac_redirect","deny_filter",".*@siphub\.net")
...
				
```


#### accept_filter (string) {#param_accept_filter}


The regular expression for default accept filtering. It make sens
			to be defined on only if the `default_filter`
			parameter is set to "deny". All contacts matching
			the `accept_filter` will be accepted; the rest 
			of them will be rejected for redirection.


The parameter may be defined only one - multiple definition will
			overwrite the previous definitions. If more regular expression 
			need to be defined, use the 
			`set_accept_filter()` scripting
			function.


*This parameter is optional, it's default 
					value being NULL.*


```c title="Set accept_filter module parameter"
...
modparam("uac_redirect","accept_filter",".*@siphub\.net")
...
				
```


### Exported Functions {#exported_functions}


#### set_deny_filter(filter,flags)
					 {#func_set_deny_filter}


Sets additional deny filters. Maximum 6 may be combined. This
			additional filter will apply only to the current message - it
			will not have a global effect.


Parameters:


- *filter* (string) - regular expression
- *flags* (string)
Default or previous added deny filter may be reset depending of
					the parameter value:

  - *reset_all* - reset both default
						and previous added deny filters;
  - *reset_default* - reset only the
						default deny filter;
  - *reset_added* - reset only the 
						previous added deny filters;
  - *empty* - no reset, just add the
						filter.


This function can be used from FAILURE_ROUTE.


```c title="set_deny_filter usage"
...
set_deny_filter(".*@domain2.net","reset_all");
set_deny_filter(".*@domain1.net","");
...
				
```


#### set_accept_filter(filter,flags)
					 {#func_set_accept_filter}


Sets additional accept filters. Maximum 6 may be combined. This
			additional filter will apply only to the current message - it
			will not have a global effect.


Parameters:


- *filter* (string) - regular expression
- *flags* (string)
Default or previous added deny filter may be reset depending of
					the parameter value:

  - *reset_all* - reset both default
						and previous added accept filters;
  - *reset_default* - reset only the
						default accept filter;
  - *reset_added* - reset only the 
						previous added accept filters;
  - *empty* - no reset, just add
						the filter.


This function can be used from FAILURE_ROUTE.


```c title="set_accept_filter usage"
...
set_accept_filter(".*@domain2.net","reset_added");
set_accept_filter(".*@domain1.net","");
...
				
```


#### get_redirects([max_total], [max_branch]) {#func_get_redirects}


The function may be called only from failure routes. It will
				extract the contacts from all 3xx branches and append them
				as new branches. Note that the function will not forward the
				new branches, this must be done explicitly from script.


How many contacts (in total and per branch) are selected 
				depends on the *max_total* and
				*max_branch* parameters:


- max_total (int, optional) - max overall number of contacts to be selected
- max_branch (int, optional) - max number of contacts per branch to be selected


Both "max_total" and "max_branch"
				default to 0 (unlimited).


NOTE that during the selection process, each set of contacts 
				from a specific branch are ordered based on "q" 
				value.


This function can be used from FAILURE_ROUTE.


```c title="get_redirects usage"
...
# no restrictions
get_redirects();
...
# no limits per branch, but not more than 6 overall contacts
get_redirects(6);
...
# max 2 contacts per branch, but no overall limit
get_redirects(, 2);
...
				
```


### Script Example


```c title="Redirection script example"
loadmodule "modules/sl/sl.so"
loadmodule "modules/usrloc/usrloc.so"
loadmodule "modules/registrar/registrar.so"
loadmodule "modules/tm/tm.so"
loadmodule "modules/acc/acc.so"
loadmodule "modules/uac_redirect/uac_redirect.so"

modparam("usrloc", "db_mode",   0)

route{
	if (is_myself("$rd")) {

		if ($rm=="REGISTER") {
			save("location");
			exit;
		};

		if (!lookup("location")) {
			sl_send_reply(404, "Not Found");
			exit;
		};
	}

	t_on_failure("do_redirect");

	if (!t_relay()) {
		sl_reply_error();
	};
}

failure_route[do_redirect] {
	if (get_redirects(3, 1))
		t_relay();
}

				
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 48 | 30 | 1716 | 144 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 18 | 10 | 106 | 372 |
| 3. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 12 | 10 | 24 | 20 |
| 4. | Rob Gagnon ([@rgagnon24](https://github.com/rgagnon24)) | 8 | 6 | 50 | 48 |
| 5. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 11 | 10 |
| 6. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 7 | 5 | 12 | 11 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 5 | 3 | 25 | 6 |
| 8. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 4 |
| 9. | Anca Vamanu | 3 | 1 | 30 | 55 |
| 10. | Konstantin Bokarius | 3 | 1 | 2 | 5 |


**All remaining contributors**: Andreas Granig, Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Edson Gellert Schubert, Elena-Ramona Modroiu, Walter Doekes ([@wdoekes](https://github.com/wdoekes)).


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jun 2005 - May 2025 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Sep 2019 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 7. | Rob Gagnon ([@rgagnon24](https://github.com/rgagnon24)) | Mar 2015 - Mar 2015 |
| 8. | Walter Doekes ([@wdoekes](https://github.com/wdoekes)) | May 2014 - May 2014 |
| 9. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | Nov 2006 - Mar 2008 |
| 10. | Konstantin Bokarius | Mar 2008 - Mar 2008 |


**All remaining contributors**: Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)), Anca Vamanu, Andreas Granig, Elena-Ramona Modroiu.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Rob Gagnon ([@rgagnon24](https://github.com/rgagnon24)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)).
