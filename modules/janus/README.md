---
title: "JANUS Module"
description: "The *\"janus\"* module is a C driver for the Janus websocket protocol. It can interact with one or more Janus servers either by issuing commands to them, or by receiving events from them."
---

## Admin Guide {#sec-admin-guide}


### Overview {#overview}


The *"janus"* module is a C driver for the
	Janus websocket protocol. It can interact with one or more
	Janus servers either by issuing commands to them, or by receiving
	events from them.


This driver can be seen as a centralized Janus connection manager.
	It will connect to each Janus server, establish the connection hanler ID and the clients can be transparent from the connection handler ID point of view, simply passing the desired Janus commands that they want to run.


### External Libraries or Applications {#sec-external-dependencies}


#### OpenSIPS Modules {#sec-module-dependencies}


The following modules must be loaded together with this module:


- *an SQL DB module*


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*


### Exported Parameters {#exported_parameters}


#### janus_send_timeout (integer) {#param_janus_send_timeout}


Time in milliseconds after a Janus WebSocket connection will be closed if it is not available for blocking writing in this interval (and OpenSIPS wants to send something on it).


*Default value is "1000" (milliseconds).*


```c title="Setting the janus_send_timeout parameter"
...
modparam("janus", "janus_send_timeout", 2000)
...
```


#### janus_max_msg_chunks (integer) {#param_janus_max_msg_chunks}


The maximum number of chunks in which a Janus message is expected to arrive via WebSocket. If a received packet is more fragmented than this, the connection is dropped


*Default value is "4"*


```c title="Setting the janus_max_msg_chunks parameter"
...
modparam("janus", "janus_max_msg_chunks", 8)
...
```


#### janus_cmd_timeout (integer) {#param_janus_cmd_timeout}


The maximally allowed duration for the execution of an Janus command.
		This interval does not include the connect duration.


*Default value is "5000" (milliseconds).*


```c title="Setting the janus_cmd_timeout parameter"
...
modparam("janus", "janus_cmd_timeout", 3000)
...
```


#### janus_cmd_polling_itv (integer) {#param_janus_cmd_polling_itv}


The sleep interval used when polling for an Janus command response. Since the
		value of this parameter imposes a minimal duration for any Janus command,
		you should run OpenSIPS in debug mode in order to first determine an expected
		response time for an arbitrary Janus command, then tune this parameter accordingly.


*Default value is "1000" (microseconds).*


```c title="Setting the janus_cmd_polling_itv parameter"
...
modparam("janus", "janus_cmd_polling_itv", 3000)
...
```


#### janus_ping_interval (integer) {#param_janus_ping_interval}


The time interval at which OpenSIPS will do keepalive pinging on the Janus connect


*Default value is "5" (seconds).*


```c title="Setting the janus_ping_interval parameter"
...
modparam("janus", "janus_ping_interval", 10)
...
```


#### janus_db_url (string) {#param_janus_db_url}


The DB URL from where OpenSIPS will load the list of Janus connection


*Default value is ""none"" (needs to be set for the module to start).*


```c title="Setting the janus_db_url parameter"
...
modparam("janus", "janus_db_url", "mysql://root@localhost/opensips")
...
```


#### janus_db_table (string) {#param_janus_db_table}


The DB Table from where OpenSIPS will load the list of Janus connection


*Default value is "janus"*


```c title="Setting the janus_db_table parameter"
...
modparam("janus", "janus_db_table", "my_janus_table")
...
```


### Exported Functions {#exported_functions}


#### janus_send_requeest(janus_id, janus_command[, response_var]) {#func_janus_send_request}


Run an arbitrary command on an arbitrary Janus socket. The
		janus_id must be defined in the database


The current OpenSIPS worker will block until an answer from Janus
		arrives. The timeout for this operation can be controlled via the
		**janus_cmd_timeout** param.


Meaning of the parameters is as follows:


- *janus_id* (string) - the ID of the janus connection as defined in the databsae.
- *janus_command* (string) - the JANUS command to run.
- *response_var (var, optional)* - a
			variable which will hold the text result of the Janus command.


**Return value**


- 1 (success) - the Janus command executed successfully and any
				output variables were successfully written to. Note that this
				does not say anything about the nature of the Janus answer (it
				may well be a "-ERR" type of response)
- -1 (failure) - internal error or the Janus command failed to
				execute


This function can be used from any route.


```c title="*janus_send_request()* usage"
...
# if the DB contains: 
#       id: 1
# janus_id: test_janus
# janus_url: janusws://my_janus_host:80/janus?room=abcd

	$var(rc) = janus_send_request("test_janus", "{
  "janus": "attach",
  "plugin": "janus.plugin.videoroom",
  "transaction": "abcdef123456",
  "session_id": 987654321
}", $var(response));
	if (!$var(rc)) {
		xlog("failed to execute Janus command ($var(rc))\n");
		return -1;
	}
	xlog("Janus response is $var(response) \n");
...
...
```


#### Exported Events {#exported_events}


##### E_JANUS_EVENT {#event_E_JANUS_EVENT}


This event is raised when a notification is received from a Janus server.


Parameters represent the janus_id and the janus_url that originated the notification, and the full janus_body of the event received


- *janus_id* - the janus id as defined in the database
- *janus_url* - the janus url as defined in the database
- *janus_body* - full body of the notification received from janus


```c title="*E_JANUS_EVENT* example"
...
# if the DB contains: 
#       id: 1
# janus_id: test_janus
# janus_url: janusws://my_janus_host:80/janus?room=abcd

event_route[E_JANUS_EVENT] {
	xlog("Received janus event from $param(janus_id) - $param(janus_url) - $param(janus_body) \n");
	$json(janus_body) := $param(janus_body);
	$avp(janus_sender) =  $json(janus_body/sender);
	if ($avp(janus_sender) != NULL) {
		xlog("Received event from sender $avp(janus_sender) \n");
	}
}
...
...
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 45 | 7 | 4390 | 5 |
| 2. | Norman Brandinger ([@NormB](https://github.com/NormB)) | 6 | 2 | 280 | 13 |
| 3. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 5 | 3 | 3 | 5 |
| 4. | rdondeti | 3 | 1 | 20 | 4 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 3 | 1 | 4 | 12 |
| 6. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 3 | 1 | 1 | 1 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 2 | 1 | 7 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2025 - May 2026 |
| 2. | Norman Brandinger ([@NormB](https://github.com/NormB)) | Mar 2026 - Apr 2026 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Apr 2026 - Apr 2026 |
| 4. | rdondeti | Mar 2026 - Mar 2026 |
| 5. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Mar 2026 - Mar 2026 |
| 6. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Dec 2024 - May 2025 |
| 7. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | Feb 2025 - Feb 2025 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)).
### License

All documentation files (i.e. .md extension) are licensed under the Creative Common License 4.0
