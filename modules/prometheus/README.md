---
title: "Prometheus Module"
description: "This module provides a HTTP interface for the [Prometheus](https://prometheus.io/) monitoring system, allowing it to fetch different statistics from OpenSIPS."
---

## Admin Guide


### Overview {#overview}


This module provides a HTTP interface for the
		[Prometheus](https://prometheus.io/)
		monitoring system, allowing it to fetch different
		statistics from OpenSIPS.


In order to use it, you have to explicitely define the
		statistics you want to provide by listing them in the
		[statistics](#param_statistics) parameter.


Currently only *counter* and *gauge*
		metrics types are supported by the module, and whether to choose
		one or the other for a specific statistic is dictated by the way that
		statistic was defined either internally, or explicitely through the
		*variable* parameter of the *statistics*
		module.


Each exported statistic comes with a *group* label that
		indicates the group it belongs to.


### Dependencies {#dependencies}


#### External Libraries or Applications


None


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *httpd* module.


### Exported Parameters {#exported_parameters}


#### root(string) {#param_root}


Specifies the root metrics path Promethus uses to query the stats:
		http://[opensips_IP]:[opensips_httpd_port]/[root]


*The default value is "metrics".*


```c title="Set root parameter"
...
modparam("prometheus", "root", "prometheus")
...
```


#### prefix(string) {#param_prefix}


Appends a prefix to each statistic exported.


*The default value is "opensips".*


```c title="Set prefix parameter"
...
modparam("prometheus", "prefix", "opensips_1")
...
```


#### group_prefix(string) {#param_group_prefix}


Appends a prefix to the name of the group the statistic belongs to.


*The default value is "" (no group prefix).*


```c title="Set group_prefix parameter"
...
modparam("prometheus", "group_prefix", "opensips")
...
```


#### delimiter(string) {#param_delimiter}


Specifies the delimiter to be used to separate *prefix*
		and *group_prefix*.


*The default value is "_".*


```c title="Set delimiter parameter"
...
modparam("prometheus", "delimiter", "-")
...
```


#### group_label(string) {#param_group_label}


Specifies the label used to store the group when *group_mode* is 2.


*The default value is "group".*


```c title="Set group_label parameter"
...
modparam("prometheus", "group_label", "grp")
...
```


#### group_mode(int) {#param_group_mode}


Specifies how the group of the statistic should be provisioned to
		Prometheus. Available modes are:


- *0* - do not send the statistics groups.
- *1* - send the group in the name of the statstic.
timestamp
core
opensips_core_timestamp
group_prefix
- *2* - send the group as a label of the statstic.
group_label


*The default value is 0 (do not specify the group).*


```c title="Set group_mode parameter"
...
modparam("prometheus", "group_mode", 1)
...
```


#### statistics(string) {#param_statistics}


The statistics that are being exported by OpenSIPS, separated by space.
			The list can also contain statistics groups's names - to do that, you shall
			add a colon (*:*) at the end of the groups's name.


If the *all* value is used, then the module will expose
			all available statistics - therefore any other settings of this parameter
			is useless;


This parameter can be defined multiple times.


*The default value is empty: no metric is exported.*


```c title="Set statistics parameter"
...
# export the number of active dialogs and the load statistics class
modparam("prometheus", "statistics", "active_dialogs load:")
...
```


#### labels(string) {#param_labels}


Rules that define how to convert the name of a statistic
			within a group to obtain the name and set of labels to be
			pushed in Prometheus.


The format is *group: regex*, where
			*group* represents the group of statistics
			for whom the regular expression should be applied for, and
			*regexp* is a regular expression used to
			match the statistic's name and convert it to the desired name
			and labels.


The *regex* format is
			*/matching_expression/substitution_expression/flags*.
			The *substitution_expression* resulted after
			the substituion should result in a string with the following
			format: *name:labels*, where
			*name* represents the name of the statistic
			as it will be pushed towards Prometheus, and *labels*
			the labels, expressed as *key=value* pairs
			separated by comma, as they are received by Prometheus.
			*Note* that the *labels*
			string resulted is concatenated to the other labeles as
			plain string - no other transformations are performed.


If a statistic's name within the declared group does not match the
			regular, or the resulted format does not comply with the
			*name:labels* format, the statistics transformations
			are ignored and it shall be printed as a regular statistic, as if
			the rule was not even used.


This parameter can be defined multiple times, even for a single group.
			However, if the statistic matches multiple regular expressions, only
			the first regular expression that matches is considered. The order
			they are checked is the order declared in the script.


*The default value is empty: statistic name is provided.*


```c title="Set statistics parameter"
...
# convert duration_gateway to stat duration with gateway as a label
modparam("prometheus", "labels", "group: /^(.*)_(.*)$/\1:gateway=\"\2\"/")
...
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Examples


In order to have Prometheus query OpenSIPS for statistics, you need to
			tell him where to get statistics from. To do that, you should define
			a scarpe job in Prometheus's *scrape_configs* config,
			indicating the IP and port you've configured the *httpd*
			module to listen on (default: *0.0.0.0:8888*).


```c title="Prometheus Scrape Config"
scrape_configs:
  - job_name: opensips

    static_configs:
    - targets: ['localhost:8888']
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 31 | 17 | 1283 | 153 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 2 | 3 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 3 | 1 | 1 | 1 |
| 4. | OpenSIPS | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Feb 2021 - Oct 2023 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Aug 2022 - Aug 2022 |
| 4. | OpenSIPS | Feb 2021 - Feb 2021 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), OpenSIPS.


*Documentation Copyrights:*


Copyright © 2021 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
