---
title: "freeswitch_scripting  Module"
description: "*freeswitch_scripting* is a helper module that exposes full control over the FreeSWITCH ESL interface to the OpenSIPS script."
---

## Admin Guide {#sec-admin-guide}


### Overview {#overview}


*freeswitch_scripting* is a helper module that
		exposes full control over the FreeSWITCH ESL interface to the OpenSIPS
		script.


It allows the OpenSIPS script writer to subscribe
		to generic FreeSWITCH ESL events as well as to run arbitrary
		FreeSWITCH ESL commands and interpret their results.
		It makes use of the [freeswitch](/modules/freeswitch)
		module for the management of ESL connections and event subscriptions.


Credits for the initial idea and working code samples providing
		both ESL events and commands go to Giovanni Maruzzelli
		<gmaruzz@opentelecom.it>.


### Dependencies {#dependencies}


#### OpenSIPS Modules {#sec-module-dependencies}


The following modules must be loaded together with this module:


- *freeswitch*
- *(optional) an SQL DB module*


#### External Libraries or Applications {#sec-external-dependencies}


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*


### Exported Parameters {#exported_parameters}


#### db_url (string) {#param_db_url}


An SQL database URL which the module will use in order to
		load a set of FreeSWITCH ESL sockets and their event subscriptions.


*Default value is "NULL" (DB support disabled).*


**Example: Setting the db_url parameter**


```opensips
...
modparam("freeswitch_scripting", "db_url", "dbdriver://username:password@dbhost/dbname")
...
```


#### db_table (string) {#param_db_table}


The SQL table name for this module.


*Default value is "freeswitch".*


**Example: Setting the db_table parameter**


```opensips
...
modparam("freeswitch_scripting", "db_table", "freeswitch_sockets")
...
```


#### db_col_username (string) {#param_db_col_username}


The SQL column name for the "username" ESL connect information.


*Default value is "username".*


**Example: Setting the db_col_username parameter**


```opensips
...
modparam("freeswitch_scripting", "db_col_username", "user")
...
```


#### db_col_password (string) {#param_db_col_password}


The SQL column name for the "password" ESL connect information.


*Default value is "password".*


**Example: Setting the db_col_password parameter**


```opensips
...
modparam("freeswitch_scripting", "db_col_password", "pass")
...
```


#### db_col_ip (string) {#param_db_col_ip}


The SQL column name for the "ip" ESL connect information.


*Default value is "ip".*


**Example: Setting the db_col_ip parameter**


```opensips
...
modparam("freeswitch_scripting", "db_col_ip", "ip_addr")
...
```


#### db_col_port (string) {#param_db_col_port}


The SQL column name for the "port" ESL connect information.


*Default value is "port".*


**Example: Setting the db_col_port parameter**


```opensips
...
modparam("freeswitch_scripting", "db_col_port", "tcp_port")
...
```


#### db_col_events (string) {#param_db_col_events}


The SQL column name for the comma-separated, case-sensitive FreeSWITCH
		event names which OpenSIPS will subscribe to.


*Default value is "events_csv".*


**Example: Setting the db_col_events parameter**


```opensips
...
modparam("freeswitch_scripting", "db_col_events", "fs_events")
...
```


#### fs_subscribe (string) {#param_fs_subscribe}


Add a FreeSWITCH ESL URL to which OpenSIPS will connect at startup.
		The URL syntax includes support for specifying a list of events to
		subscribe to and follows this pattern:
		**[fs://][[username]:password@]host[:port][?event1[,event2]...]**


*This parameter can be set multiple times.*


**Example: Setting the fs_subscribe parameter**


```opensips
...
modparam("freeswitch_scripting", "fs_subscribe", ":ClueCon@10.0.0.10?CHANNEL_STATE")
modparam("freeswitch_scripting", "fs_subscribe", ":ClueCon@10.0.0.11:8021?DTMF,BACKGROUND_JOB")
...
```


### Exported Functions {#exported_functions}


#### freeswitch_esl(command, freeswitch_url[, response_var]) {#func_freeswitch_esl}


Run an arbitrary command on an arbitrary FreeSWITCH ESL socket. The
		socket need not necessarily be defined in the database or through
		**[fs subscribe](#param_fs_subscribe)**.
		However, if this is the case, then the "password" part of the URL
		becomes mandatory.


The current OpenSIPS worker will block until an answer from FreeSWITCH
		arrives. The timeout for this operation can be controlled via the
		**esl_cmd_timeout** parameter of the
		freeswitch connection manager module.


Meaning of the parameters is as follows:


- *command* (string) - the ESL command string to
			execute.
- *freeswitch_url* (string) - the ESL interface to
			connect to. The syntax is:
			[fs://][[username]:password@]host[:port][?event1[,event2]...].
			The "?events" part of the URL will be silently discarded.
- *response_var (var, optional)* - a
			variable which will hold the text result of the ESL command.


**Return value**


- 1 (success) - the ESL command executed successfully and any
				output variables were successfully written to. Note that this
				does not say anything about the nature of the ESL answer (it
				may well be a "-ERR" type of response)
- -1 (failure) - internal error or the ESL command failed to
				execute


This function can be used from any route.


**Example: 
				*freeswitch_esl()* usage**


```opensips
...
	# ESL socket 10.0.0.10 is defined in the database (password "ClueCon")
	$var(rc) = freeswitch_esl("bgapi originate {origination_uuid=123456789}user/1010 9386\njob-uuid: foobar", "10.0.0.10", "$var(response)");
	if ($var(rc) < 0) {
		xlog("failed to execute ESL command ($var(rc))\n");
		return -1;
	}
...
	# ESL socket 10.0.0.10 is new, we must specify a password
	$var(rc) = freeswitch_esl("bgapi originate {origination_uuid=123456789}user/1010 9386\njob-uuid: foobar", ":ClueCon@10.0.0.10", $var(response));
	if ($var(rc) < 0) {
		xlog("failed to execute ESL command ($var(rc))\n");
		return -1;
	}
...
```


### Exported MI Commands {#sec-mi-commands}


#### freeswitch_scripting:subscribe {#mi_subscribe}


Replaces obsolete MI command: *fs_subscribe*.


Ensures that the given FreeSWITCH ESL socket is subscribed to the given
		list of events. In case an event cannot be subscribed to, the freeswitch
		driver will periodically retry to subscribe to it until an
		freeswitch_scripting:unsubscribe
		MI command for the respective event is issued.


Parameters:


- *freeswitch_url* - the ESL interface to
				connect to. The syntax is:
				[fs://][[username]:password@]host[:port][?event1[,event2]...].
				The "?events" part of the URL will be silently discarded.
- *event* - the name of the event to subscribe to
- *...* - (other events)


#### freeswitch_scripting:unsubscribe {#mi_unsubscribe}


Replaces obsolete MI command: *fs_unsubscribe*.


Ensures that the given FreeSWITCH ESL socket is unsubscribed from the given
		list of events.


Parameters:


- *freeswitch_url* - the ESL interface to
				search for. The syntax is:
				[fs://][[username]:password@]host[:port][?event1[,event2]...].
				The "?events" part of the URL will be silently discarded.
- *event* - the name of the event to unsubscribe from
- *...* - (other events)


#### freeswitch_scripting:list {#mi_list}


Replaces obsolete MI command: *fs_list*.


Displays the current set of FreeSWITCH ESL sockets and the list of events
		that the module is subscribed to for each socket.


#### freeswitch_scripting:reload {#mi_reload}


Replaces obsolete MI command: *fs_reload*.


Replaces the current set* of FreeSWITCH ESL sockets along with their respective
		events with the current data (ESL sockets and their events) found in the
		"freeswitch" table.


* this includes any sockets/events provisioned through
			[fs subscribe](#param_fs_subscribe), MI
			[mi subscribe](#mi_subscribe) commands or previous DB data set.


### Exported Events {#exported_events}


#### E_FREESWITCH {#event-freeswitch}


This event is raised when OpenSIPS receives an ESL event notification from
		a socket that the "freeswitch_scripting" module is subscribed to.


Parameters:


- *name* - the name of the event
- *sender* - the FreeSWITCH sender IP address
- *body* - the full JSON-encoded body of the event,
					as sent by FreeSWITCH. Use the json module ($json variable)
					to easily interpret it.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 49 | 27 | 1935 | 277 |
| 2. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 8 | 6 | 42 | 23 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 8 | 3 | 137 | 124 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 4 | 2 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 3 | 1 | 4 | 4 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | May 2019 - Mar 2026 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Dec 2017 - Feb 2024 |
| 3. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2019 - Mar 2020 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Jan 2019 - Apr 2019 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)).


*Documentation Copyrights:*


Copyright © 2017 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
