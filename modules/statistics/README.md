---
title: "Statistics Module"
description: "The Statistics module is a wrapper over the internal statistics manager, allowing the script writer to dynamically define and use of statistic variables."
---

## Admin Guide


### Overview {#overview}


The Statistics module is a wrapper over the internal
		statistics manager, allowing the script writer to dynamically define and
		use of statistic variables.


By bringing the statistics support into the script, it takes advantage
		of the script flexibility in defining logics, making possible 
		implementation of any kind of statistic scenario.


### Statistic Groups


Starting with OpenSIPS 2.3, statistics may be grouped by prefixing
		their names with the name of the desired group, along with a colon
		separator (e.g. **$stat(method:invite)** or
**update_stat("packets:$var(ptype)", "+1")**).
		In order for this to work, the groups must be defined prior to OpenSIPS startup
		using the **[stat groups](#param_stat_groups)**
		module parameter.


The module allows easy iteration over the statistics of a group using
		the **[stat iter init](#func_stat_iter_init)**
		and **[stat iter next](#func_stat_iter_next)**
		functions.


By default, all statistics belong to the
		**"dynamic"** group.


### Statistic Series {#section_stat_series}


Statistic series provide the ability to accumulate statistical data
		over a pre-defined time window. Data is stored in a circular buffer, pushing
		new data on top, and removing stale values (values outside the timeframe) from
		the bottom. These statistics can be used to provide per-time stats, such as
		ACD, ASR, AST, etc, that can be read using the classic statistics interface,
		through the *$stat()* variable.


Statistic series profile describe the timeframe used to store the data, as
		well as how the data is be accumulated and interpreted. There are several
		types a statistic series can be used, depending on the provisioned algorithm:


- *accumulate* - accumulates the specified values in a
			counter; works similar to clasical statistics, except that they reset
			after the specified timeframe
- *average* - returns an average of all the data fed
			within the timeframe; can be useful when computing PDD, AST, ACD stats.
- *percentage* - indicates the percentage of a set of
			values out of the total amount of values fed; can be useful when computing
			ASR, NER, CCR stats.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *No dependencies on other OpenSIPS modules*.


#### External Libraries or Applications


The following libraries or applications must be installed before running
		OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### variable (string) {#param_variable}


Name of a new statistic variable. The name may be followed by additional
		flag which describe the variable behavior:


- *no_reset* : variable cannot be reset.


```c title="variable example"
modparam("statistics", "variable", "register_counter")
modparam("statistics", "variable", "active_calls/no_reset")
```


#### stat_groups (string) {#param_stat_groups}


A comma-separated values string, specifying the statistic groups that
		may be used throughout the OpenSIPS script. Groups cannot contain leading or
		trailing whitespace characters.


```c title="setting the stat_groups parameter"
modparam("statistics", "stat_groups", "method, packet, response")
```


#### stat_series_profile (string) {#param_stat_series_profile}


Used to define a statistic series profile. Has the following format:
		*name: [attr=value]**, where *name*
		represents the name of the profile, and *attr=value*
		contains multiple settings of the defined profile. Possible attributes
		and their values are:


- *algorithm* - indicates the way data should be
			stored and accumulated over the specified timeframe. Possible values are:
			*accumulate*, *average* and
			*percentage*, as described in the
			**[section stat series](#section_stat_series)**
			paragraph (default is *accumulate*)
- *hash_size* - each statistic defined/used is stored in
			a hash map attached to the profile; this setting tunes the size of the hash
			(default is: 8)
- *group* - indicates the group where the statistics
			beloging to this profile are grouped (as described in
			**[stat groups](#param_stat_groups)**
			(default is to use the same group as the profile)
- *window* - the number of seconds a timeframe has;
			all older values (out of the specified window) are discarded
			(default is *60* seconds)
- *slots* - the number of slots per window; used to tune
			the granularity of the circular buffer; the higher the number of slots is,
			the more accurate the resulted statistic;
			(default is the same value of the *window* parameter)
- *percentage_factor* - used for
			*percentage* algorithm profiles to specify the
			percentage factor to be used (defaults to *100*)


This parameter can be set multiple times, for each profile needed.


```c title="setting the stat_series_profile parameter"
...
# define a statistic that accumulates average values in the last minute
modparam("statistics", "stat_series_profile", "avg: algorithm=average")
...
# define a statistic that accumulates average values in the 10 minutes
# with 1 minute granularity (10 slots out of the 600s window)
modparam("statistics", "stat_series_profile", "avg_10m: algorithm=average window=600 slots=10")
...
# define a statistic that computes the percentage of values in the last hour
# with 10 minutes granularity (6 slots out of the 3600s window)
modparam("statistics", "stat_series_profile", "perc_1h: algorithm=percentage window=3600 slots=6")
...
```


### Exported Functions {#exported_functions}


#### update_stat(variable, value) {#func_update_stat}


Updates the value of the statistic variable with the new value.


Meaning of the parameters is as follows:


- *variable* (string) - variable to be updated;
- *value* (int) - value to update with; it may be
			also negative.


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE, 
		FAILURE_ROUTE and ONREPLY_ROUTE.


```c title="update_stat usage"
...
update_stat("register_counter", 1);
...
$var(a_calls) = "active_calls";
update_stat($var(a_calls), -1);
...
```


#### reset_stat(variable) {#func_reset_stat}


Resets to zero the value of the statistic variable.


Meaning of the parameters is as follows:


- *variable* (string) - variable to be reset-ed


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE, 
		FAILURE_ROUTE and ONREPLY_ROUTE.


```c title="reset_stat usage"
...
reset_stat("register_counter");
...
$var(reg_counter) = "register_counter";
update_stat($var(reg_counter));
...
```


#### stat_iter_init(group, iter) {#func_stat_iter_init}


Re-initializes "iter" in order to begin iterating through all
		statistics belonging to the given "group".


Meaning of the parameters is as follows:


- *group* (string)
- *iter* (string) - internally matched
				to a corresponding iterator


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE, 
		FAILURE_ROUTE and ONREPLY_ROUTE.


```c title="stat_iter_init usage"
...
stat_iter_init("packet", "iter");
...
```


#### stat_iter_next(name, val, iter) {#func_stat_iter_next}


Attempts to fetch the current statistic to which "iter" points.
		If successful, the relevant data will be written to "name" and "val",
		while also advancing "iter". Returns negative when reaching the end of iteration.


Meaning of the parameters is as follows:


- *name* (var)
- *val* (var)
- *iter* (string) - internally matched
				to a corresponding iterator


This function can be used from REQUEST_ROUTE, BRANCH_ROUTE, 
		FAILURE_ROUTE and ONREPLY_ROUTE.


```c title="stat_iter_next usage"
...
# periodically clear packet-related data
timer_route [clear_packet_stats, 7200] {
	stat_iter_init("packet", "iter");
	while (stat_iter_next($var(stat), $var(val), "iter"))
		reset_stat("packet:$var(stat)");
}
...
```


#### update_stat_series(profile, variable, value) {#func_update_stat_series}


Updates the value of a series statistic.


Meaning of the parameters is as follows:


- *profile* (string) - the profile as defined in
			**[stat series profile](#param_stat_series_profile)**
- *variable* (string) - variable to be updated;
- *value* (int) - value to update with; it may be
			also negative; when using *percentage* algorithm, the
			resulted value represents the percentage of positive values out of the
			total number of values (positive + negative)


This function can be used from any route.


```c title="update_stat_series usage"
...
# account failed calls
update_stat_series("perc_1h", "ASR_1h", -1);

# account successful calls
update_stat_series("perc_1h", "ASR_1h", 1);

# compute average PDD
update_stat_series("avg", "PDD", $var(pdd_ms));
...
```


### Exported Pseudo-Variables {#exported_pseudo_variables}


#### $stat {#pv_stat}


Allows "get" or "reset" operations on the given statistics.


The name of a statistic may be optionally prefixed with a searching
			group, along with a colon separator.


If a searching group is not provided, the statistic is first
			searched for in the core groups. If not found, search continues with
			the "dynamic" group which, by default, holds all non-explicitly
			grouped statistics which are not exported by the OpenSIPS core.


```c title="$stat usage"
...
xlog("SHM used size = $stat(used_size), no_invites = $stat(method:invite)\n");
...
$stat(err_requests) = 0;
...
			
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 33 | 20 | 1037 | 227 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 29 | 21 | 519 | 164 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 20 | 13 | 691 | 19 |
| 4. | Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)) | 11 | 9 | 22 | 18 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 9 | 4 | 97 | 201 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 6 | 4 | 6 | 7 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 5 | 2 | 144 | 1 |
| 8. | Anca Vamanu | 4 | 2 | 14 | 16 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 4 | 2 | 11 | 11 |
| 10. | Henning Westerholt ([@henningw](https://github.com/henningw)) | 4 | 2 | 4 | 4 |


**All remaining contributors**: Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Konstantin Bokarius, Julián Moreno Patiño, Edson Gellert Schubert.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2014 - May 2025 |
| 2. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Feb 2025 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Jan 2021 - Nov 2023 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2012 - Oct 2023 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - May 2019 |
| 6. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Mar 2006 - Apr 2019 |
| 7. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Apr 2017 - Apr 2017 |
| 8. | Julián Moreno Patiño | Feb 2016 - Feb 2016 |
| 9. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Jul 2010 - Feb 2011 |
| 10. | Anca Vamanu | Oct 2007 - Sep 2009 |


**All remaining contributors**: Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert, Henning Westerholt ([@henningw](https://github.com/henningw)).


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)), Ovidiu Sas ([@ovidiusas](https://github.com/ovidiusas)), Daniel-Constantin Mierla ([@miconda](https://github.com/miconda)), Konstantin Bokarius, Edson Gellert Schubert.
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
