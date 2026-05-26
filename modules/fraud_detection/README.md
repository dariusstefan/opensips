---
title: "Fraud Detection Module"
description: "This module provides a way to prevent some basic fraud attacks. Alerts are provided through return codes and events."
---

## Admin Guide


### Overview {#overview}


This module provides a way to prevent some basic fraud attacks.
		Alerts are provided through return codes and events.


#### Monitored Stats


Basically, this module watches the following parameters:


- Total calls
- Calls per minute
- Concurrent calls
- Number of sequential calls
- Call duration


Each of the above parameters is monitored for every user and
			every called prefix separately. The stats are altered whenever
			the *check_fraud* function is called. The
			function assumes a new call is made, and checks the called
			number against all the rules from the supplied profile. The
			rule's prefix is considered to be the called prefix which along with
			the provided user will be used to monitor values for the 5
			parameters.


#### Fraud rules


A rule is a set of two thresholds (warning and critical thresholds) for each of the
			five parameters (as described above) and is only available for a specified prefix.
			Further more, a rule will only match between the indicated hours in the indicated days
			of the week (similarly to a dr rule). A fraud profile is simply a group of fraud rules
			and is used to only to limit the list of rules to match when calling the check_fraud
			function.


### Dependencies {#dependencies}


#### OpenSIPS modules


The following modules must be loaded before this module:


- drouting
- dialog


#### External libraries or applications


The following libraries or applications must be installed before
		running OpenSIPS with this module:


- *none*.


### Exported Parameters {#exported_parameters}


#### db_url (string) {#param_db_url}


Database where to load the rules from.


*Default value is "NULL". At least one db_url should
			be defined for the fraud_detection module to work.*


**Example: Set the "db_url" parameter**


```opensips
...
modparam("fraud_detection", "db_url", "mysql://user:passwb@localhost/database")
...
```


#### use_utc_time (integer) {#param_use_utc_time}


Set this parameter to non-zero in order to enable UTC-based interval
		matching and statistics resets, rather than local time-based.


*The default value is "0" (use local time).*


**Example: Set the "use_utc_time" parameter**


```opensips
...
modparam("fraud_detection", "use_utc_time", 1)
...
```


#### table_name (string) {#param_table_name}


If you want to load the rules from the database you must set
		this parameter as the database name.


*The default value is "fraud_detection".*


**Example: Set the "table_name" parameter**


```opensips
...
modparam("fraud_detection", "table_name", "my_fraud")
...
```


#### rid_col (string) {#param_rid_col}


The column's name in the database storing the
			fraud rule's id.


*Default value is "ruleid".*


**Example: Set "rid_col" parameter**


```opensips
...
modparam("fraud_detection", "rid_col", "theruleid")
...
```


#### pid_col (string) {#param_pid_col}


The column's name in the database storing the
			fraud profile's id.


Please keep in mind that a profile is merely
			a set of rules.


*Default value is "profileid".*


**Example: Set "pid_col" parameter**


```opensips
...
modparam("fraud_detection", "pid_col", "profile")
...
```


#### prefix_col (string) {#param_prefix_col}


The column's name in the database storing the
			prefix for which the fraud rule will match.


*Default value is "prefix".*


**Example: Set "prefix_col" parameter**


```opensips
...
modparam("fraud_detection", "prefix_col", "myprefix")
...
```


#### start_h (string) {#param_start_h}


The column's name in the database storing the
			the start time of the interval in which the
			rule will match.


The time needs to be specified as string using
			the format: "HH:MM"


*Default value is "start_hour".*


**Example: Set "start_h" parameter**


```opensips
...
modparam("fraud_detection", "start_h", "the_start_time")
...
```


#### end_h (string) {#param_end_h}


The column's name in the database storing the
			the end time of the interval in which the
			rule will match.


The time needs to be specified as string using
			the format: "HH:MM"


*Default value is "end_hour".*


**Example: Set "end_h" parameter**


```opensips
...
modparam("fraud_detection", "end_h", "the_end_time")
...
```


#### days_col (string) {#param_days_col}


The column's name in the database storing the
			week days in which the fraud rule's interval
			is available.


The daysoftheweek needs to be specified as a
			string containing a list of days or intervals.
			Each day must be specified using the first
			three letters of its name. A valid string
			would be: "Fri-Mon, Wed, Thu"


*Default value is "daysoftheweek".*


**Example: Set "days_col" parameter**


```opensips
...
modparam("fraud_detection", "days_col", "days")
...
```


#### cpm_thresh_warn_col (string) {#param_cpm_thresh_warn_col}


The column's name in the database storing the
			warning threshold value for calls per minute.


*Default value is "cpm_warning".*


**Example: Set "cpm_thresh_warn_col" parameter**


```opensips
...
modparam("fraud_detection", "cpm_thresh_warn_col", "cpm_warn_thresh")
...
```


#### cpm_thresh_crit_col (string) {#param_cpm_thresh_crit_col}


The column's name in the database storing the
			critical threshold value for calls per minute.


*Default value is "cpm_critical".*


**Example: Set "cpm_thresh_crit_col" parameter**


```opensips
...
modparam("fraud_detection", "cpm_thresh_crit_col", "cpm_crit_thresh")
...
```


#### calldur_thresh_warn_col (string) {#param_calldur_thresh_warn_col}


The column's name in the database storing the
			warning threshold value for call duration.


*Default value is "call_duration_warning".*


**Example: Set "calldur_thresh_warn_col" parameter**


```opensips
...
modparam("fraud_detection", "calldur_thresh_warn_col", "calldur_warn_thresh")
...
```


#### calldur_thresh_crit_col (string) {#param_calldur_thresh_crit_col}


The column's name in the database storing the
			critical threshold value for call duration.


*Default value is "call_duration_critical".*


**Example: Set "calldur_thresh_crit_col" parameter**


```opensips
...
modparam("fraud_detection", "calldur_thresh_crit_col", "calldur_crit_thresh")
...
```


#### totalc_thresh_warn_col (string) {#param_totalc_thresh_warn_col}


The column's name in the database storing the
			warning threshold value for the number of total calls.


*Default value is "total_calls_warning".*


**Example: Set "totalc_thresh_warn_col" parameter**


```opensips
...
modparam("fraud_detection", "totalc_thresh_warn_col", "totalc_warn_thresh")
...
```


#### totalc_thresh_crit_col (string) {#param_totalc_thresh_crit_col}


The column's name in the database storing the
			critical threshold value for the number of total calls.


*Default value is "total_calls_critical".*


**Example: Set "totalc_thresh_crit_col" parameter**


```opensips
...
modparam("fraud_detection", "totalc_thresh_crit_col", "totalc_crit_thresh")
...
```


#### concalls_thresh_warn_col (string) {#param_concalls_thresh_warn_col}


The column's name in the database storing the
			warning threshold value for the number of
			concurrent calls.


*Default value is "concurrent_calls_warning".*


**Example: Set "concalls_thresh_warn_col" parameter**


```opensips
...
modparam("fraud_detection", "concalls_thresh_warn_col", "concalls_warn_thresh")
...
```


#### concalls_thresh_crit_col (string) {#param_concalls_thresh_crit_col}


The column's name in the database storing the
			critical threshold value for the number of
			concurrent calls.


*Default value is "concurrent_calls_critical".*


**Example: Set "concalls_thresh_crit_col" parameter**


```opensips
...
modparam("fraud_detection", "concalls_thresh_crit_col", "concalls_crit_thresh")
...
```


#### seqcalls_thresh_warn_col (string) {#param_seqcalls_thresh_warn_col}


The column's name in the database storing the
			warning threshold value for the number of
			sequential calls.


*Default value is "sequential_calls_warning".*


**Example: Set "seqcalls_thresh_warn_col" parameter**


```opensips
...
modparam("fraud_detection", "seqcalls_thresh_warn_col", "seqcalls_warn_thresh")
...
```


#### seqcalls_thresh_crit_col (string) {#param_seqcalls_thresh_crit_col}


The column's name in the database storing the
			critical threshold value for the number of
			sequential calls.


*Default value is "sequential_calls_critical".*


**Example: Set "seqcalls_thresh_crit_col" parameter**


```opensips
...
modparam("fraud_detection", "seqcalls_thresh_crit_col", "seqcalls_crit_thresh")
...
```


### Exported Functions {#exported_functions}


#### check_fraud(user, number, profile_id) {#func_check_fraud}


This method should be called each time a given *user*
			calls a given *number*. It will try to match a fraud rule
			within the given fraud profile and update the stats (see above). Furthermore,
			the stats will be checked against the rule's thresholds. If any of the stats
			is above its threshold value, the appropriate event will also be raised
			(see further details below).


Designed to only work with initial INVITE messages!  If a dialog is
			not already present, one will be created (equivalent of
			create_dialog()).


Meaning of the parameters is as follows:


- *user* (string) - the user who is making the call. Please keep in mind that
				the user doesn't have to be registered. This string is only used to keep different stats
				for different registered users.
- *number* (string) - the number the user is calling to.
- *profile_id* (int) - the fraud profile id (i.e. the subset of fraud
				rules) in which to try and find a matching fraud rule.


The meaning of the return code is as follows:


- *2* - no matching fraud rule was found
- *1* - a matching rule was found, but there is no
					parameter above the rule's threshlod, i.e - everything is ok
- *-1* - there is a parameter above the warning threshold value.
					Check the raised event for more info
- *-2* - there is a parameter above the critical threshold value.
					Check the raised event for more info
- *-3* - something went wrong (internal mechanism failed)


This function can be used from REQUEST_ROUTE and ONREPLY_ROUTE.


### Exported MI Functions {#exported_mi_functions}


#### fraud_detection:show_stats {#mi_show_stats}


Replaces obsolete MI command: *show_fraud_stats*.


Show the current statistics for all dials of a
		*user* to a *prefix*.


NOTE: Since the fraud statistics are refreshed on-the-fly, as
		check_fraud() is called, **this function will
		return stale data** if check_fraud() has not been called at
		least once for the (user, prefix) pair within a newly matching time
		interval!


Name: *fraud_detection:show_stats*


Parameters:


- user
- prefix


#### fraud_detection:reload {#mi_reload}


Replaces obsolete MI command: *fraud_reload*.


Reload the all the fraud rules.


Name: *fraud_detection:reload*


Parameters: *none*


### Exported Events {#exported_events}


#### E_FRD_WARNING {#event_E_FRD_WARNING}


This event is raised whenever one of the 5 monitored parameters
			is above the warning threshold value


Parameters:


- *param* - the name of the parameter.
- *value* - the current value of the parameter.
- *threshold* - the warning threshold value.
- *user* - the user who initiated the call.
- *called_number* - the number that was called.
- *rule_id* - the id of the fraud rule that matched
					when the call was initiated
- *profile_id* - the profile id used


#### E_FRD_CRITICAL {#event_E_FRD_CRITICAL}


This event is raised whenever one of the 5 monitored parameters
			is above the warning threshold value


Parameters:


- *param* - the name of the parameter.
- *value* - the current value of the parameter.
- *threshold* - the warning threshold value.
- *user* - the user who initiated the call.
- *called_number* - the number that was called.
- *rule_id* - the id of the fraud rule that matched
					when the call was initiated
- *profile_id* - the profile id used


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 51 | 42 | 383 | 284 |
| 2. | Andrei Datcu ([@andrei-datcu](https://github.com/andrei-datcu)) | 38 | 11 | 2665 | 235 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 11 | 9 | 32 | 21 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 8 | 5 | 73 | 114 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 7 | 5 | 20 | 31 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 5 | 3 | 7 | 8 |
| 7. | Ahron Greenberg | 3 | 1 | 22 | 11 |
| 8. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 3 | 1 | 21 | 6 |
| 9. | Alexandra Titoc | 3 | 1 | 13 | 1 |
| 10. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2015 - Mar 2026 |
| 2. | Alexandra Titoc | Sep 2024 - Sep 2024 |
| 3. | Ahron Greenberg | Sep 2024 - Sep 2024 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Jan 2021 - Feb 2023 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2015 - Aug 2022 |
| 6. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Apr 2019 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Oct 2014 - Apr 2019 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 9. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Jan 2016 - Jan 2016 |
| 10. | Andrei Datcu ([@andrei-datcu](https://github.com/andrei-datcu)) | Aug 2014 - Sep 2014 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Ahron Greenberg, Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Andrei Datcu ([@andrei-datcu](https://github.com/andrei-datcu)).


*Documentation Copyrights:*


Copyright © 2014 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
