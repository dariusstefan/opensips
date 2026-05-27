---
title: "event_stream Module"
description: "This module provides a TCP transport layer implementation for the Event Interface. The module can either send a JSON-RPC notification or a standard request and wait for the response (when used in *reliable_mode*)."
---

## Admin Guide


### Overview {#overview}


This module provides a TCP transport layer implementation for the Event
		Interface. The module can either send a JSON-RPC notification or a
		standard request and wait for the response (when used in
		*reliable_mode*).


As the JSON-RPC is sent directly over TCP, avoiding any application
		transport layer (such as HTTP), this module offers a very lightweight
		and reliable way of delivering events to an application server.


In order to be notified, a JSON-RPC server has to subscribe for a
		certain event provided by OpenSIPS. This can be done using the generic
		MI Interface (*event_subscribe* function) or from
		OpenSIPS script (*subscribe_event* core function).


### Stream socket syntax


*'tcp:' host ':' port ['/' method]*


Meaning:


- *'tcp:'* - specifies the
					transport protocol used by the Event Interface
					to send the command. the *tcp*
					token indicates that the subscriber's events should be
					notified using the
					*event_strea,* module.
- *host* - host name of the JSON-RPC server.
- *port* - port of the JSON-RPC server.
- *method* - method called remotely by the
					JSON-RPC client.
					NOTE: this parameter is optional - if it is missing,
						the method used is the actual event subscribed
						to (i.e. if *localhost:8080*
						subscribes to the *E_PIKE_BLOCKED*
						event, the RPC call will use the
						*E_PIKE_BLOCKED* method.


The JSON-RPC command is built as it follows:


- *id* - uniquly generated if
				*reliable_mode* is used, otherwise (for
				notifications) *null*.
- *method* - if no method is specified in the
				socket, the name of the event is set as method, otherwise
				the token specified is used.
- *params* - if the event sent contains
				named parameters, then this parameter contains a JSON object
				with an object for each parameter. If the event sent only
				contains values, the parameters will be sent as an array.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *none*.


#### External Libraries or Applications


The following libraries or applications must be installed before 
		running OpenSIPS with this module loaded:


- *none*


### Exported Parameters {#exported_parameters}


#### reliable_mode (integer) {#param_reliable_mode}


This parameter controls the way the
			*event_stream* module communicates
			with the JSON-RPC server. If enabled, (set to
			*1*), each event is translated to
			a JSON-RPC request. If disabled, each event will be sent
			as a JSON-RPC notification - there will be no reply
			expected by our client.


Note that if you need a reliable communication with
			the JSON-RPC server, where each event sent needs to be
			confirmed (by a JSON-RPC response), you must set this parameter
			to *1/yes*. If you are using this
			module in a failover setup (using the
			*event_virtual* module), it is recommended
			to set this parameter to *1/yes*.


*Default value is "0 (disabled)".*


```opensips title="Set reliable_mode parameter"
...
modparam("event_stream", "reliable_mode", yes)
...
```


#### timeout (integer) {#param_timeout}


Specified the amount of milliseconds the module
			waits for a command to complete. In
			*reliable_mode*, it specifies the time
			module waits the request to be sent and a reply received.
			In non-*reliable_mode*, it represents
			only the time opensips takes to send the JSON-RPC
			notification.


NOTE that if the event is not using names for its parameters,
			the event will be the first parameter in the JSON-RPC command.


*Default value is "1000 milliseconds = 1 second".*


```opensips title="Set timeout parameter"
...
# only wait for 200 milliseonds for a reply
modparam("event_stream", "timeout", 200)
...
```


#### event_param (string) {#param_event_param}


By default, the name of the event subscribed to is not
			send in the JSON-RPC command. If one needs to send the
			name of the event as well, you can use this parameter to
			specify the name of JSON object within the params that
			will contain the name of the event.


*Default value is "disabled" - event is not added.*


```opensips title="Set event_param parameter"
...
modparam("event_stream", "event_param", "opensips_event")
# json resulted will contain the "opensips_event": EVENT token
...
```


### Exported Functions {#exported_functions}


No function exported to be used from configuration file.


### Examples


```c title="Stream socket"
	# calls the 'block_ip' method
	tcp:127.0.0.1:8080/block_ip

	# calls the 'E_PIKE_BLOCKED' method, if subscribed to the E_PIKE_BLOCKED event
	tcp:127.0.0.1:8080
```


#### JSON-RPC notification


This is an example of an event raised when
			*reliable_mode* is disabled
			by the pike module when it decides an ip should be blocked:


```c title="E_PIKE_BLOCKED JSON-RPC notification"
{
	"jsonrpc": "2.0",
	"method": "E_PIKE_BLOCKED",
	"params": {
		"ip": "192.168.2.11"
	}
}
```


#### JSON-RPC Request


This is an example of an event raised in
			*reliable_mode* by the pike module
			when it decides an ip should be blocked:


```c title="E_PIKE_BLOCKED JSON-RPC request (reliable_mode)"
# request
{
	"id": 915243442,
	"jsonrpc": "2.0",
	"method": "E_PIKE_BLOCKED",
	"params": {
		"ip": "192.168.2.11"
	}
}

# reply
{
	"jsonrpc": "2.0",
	"result": 8,
	"id": 915243442
}
```


#### JSON-RPC Notification with Event's name


when having the *event_param* set to
			*opensips_event*, the event raised by
			the pike module will look like the following:


```opensips title="E_PIKE_BLOCKED notification with event name"
# module configuration
modparam("event_stream", "event_param", "opensips_event")

# Stream socket: tcp:HOST:PORT/handle_cmd

# JSON-RPC command sent
{
	"jsonrpc": "2.0",
	"method": "handle_cmd",
	"params": {
		"opensips_event": "E_PIKE_BLOCKED"
		"ip": "192.168.2.11"
	}
}
```


#### Custom JSON-RPC Notification from script


This example contains a snippet to send a custom
			event from the script using the
			*event_stream* module.


Note that we are only populating values for the
			event, we are not assinging names to those values.
			Therefore, the parameters will be sent as an array.


```opensips title="E_PIKE_BLOCKED event"
startup_route {
	subscribe_event("E_MY_EVENT", "tcp:127.0.0.1:8080");
}

route {
	...
	$avp(attr-val) = 3;
	$avp(attr-val) = 5;
	raise_event("E_MY_EVENT", $avp(attr-val));
	...
}

# JSON-RPC command sent
{
	"jsonrpc": "2.0",
	"method": "E_MY_EVENT",
	"params": [3, 5]
}
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 32 | 14 | 1802 | 100 |
| 2. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 10 | 6 | 105 | 145 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 8 | 6 | 14 | 42 |
| 4. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 7 | 9 |
| 5. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 3 | 4 |
| 6. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 4 | 2 | 2 | 2 |
| 7. | Ryan Bullock | 2 | 1 | 2 | 0 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Ryan Bullock | Apr 2025 - Apr 2025 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Apr 2018 - Mar 2025 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2020 - Jun 2023 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 5. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Aug 2020 |
| 6. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2018 - Jan 2020 |
| 7. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Feb 2019 - Apr 2019 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)).


*Documentation Copyrights:*


Copyright © 2018 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
