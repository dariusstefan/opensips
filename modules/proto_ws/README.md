---
title: "proto_ws Module"
description: "The WebSocket protocol ([RFC 6455](http://tools.ietf.org/html/rfc6455)) provides an end-to-end full-duplex communication channel between two web-based applications. This allows WebSocket enabled browsers to connect to a WebSocket server and exchange any type of data. [RFC 7118](http://tools...."
---

## Admin Guide


### Overview {#overview}


The WebSocket protocol ([RFC 6455](http://tools.ietf.org/html/rfc6455))
	 provides an end-to-end full-duplex communication channel between two web-based applications.
	This allows WebSocket enabled browsers to connect to a WebSocket server
	and exchange any type of data.
	[RFC 7118](http://tools.ietf.org/html/rfc7118)
	provides the specifications for transporting SIP messages over the WebSocket protocol.


The **proto_ws** module is transport module that provides
	communication over the WebSocket protocol. This module is fully compliant with the
	[RFC 7118](http://tools.ietf.org/html/rfc7118), thus allowing browsers
	to act as SIP clients for the OpenSIPS proxy.


The current implementation acts both as WebSocket server and client, thus it can
	accept connections from WebSocket clients and can also initiate connections to another
	WebSocket server. After the connection is established, messages can flow  in
	both directions.


OpenSIPS supports the following WebSocket operations:


- text and binary - can both send and receive WebSocket messages that contain text or binary body
- close - messages used to safely close the WebSocket communication using a 2-messages handshake
- ping - responds with pong messages. There is no mechanism to trigger ping messages.
- pong - sent when a ping message is received. OpenSIPS, absorbes the pong messages received.


Once loaded, you will be able to define WebSocket listeners in your script. To
		add a listener, you have to add its IP, and optionally the listening port,
		*after* the `mpath` parameter, similar to this
		example:
	```

...
mpath=/path/to/modules
...
socket=ws:127.0.0.1		# change with the listening IP
socket=ws:127.0.0.1:5060	# change with the listening IP and port
...
```


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *None*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### ws_port (integer) {#param_ws_port}


The default port to be used for all WS related operation. Be careful
		as the default port impacts both the SIP listening part (if no port is
		defined in the WS listeners) and the SIP sending part (if the 
		destination WS URI has no explicit port).


If you want to change only the listening port for WS, use the port
		option in the SIP listener defintion.


*Default value is 80.*


**Example: Set ws_port parameter**


```opensips
...
modparam("proto_ws", "ws_port", 8080)
...
```


#### ws_send_timeout (integer) {#param_ws_send_timeout}


Time in milliseconds after a WebSocket connection will be closed if it is
		not available for blocking writing in this interval (and OpenSIPS wants
		to send something on it).


*Default value is 100 ms.*


**Example: Set ws_send_timeout parameter**


```opensips
...
modparam("proto_ws", "ws_send_timeout", 200)
...
```


#### ws_max_msg_chunks (integer) {#param_ws_max_msg_chunks}


The maximum number of chunks in which a SIP message is expected to
			arrive via WebSocket. If a received packet is more fragmented than this,
			the connection is dropped (either the connection is very
			overloaded and this leads to high fragmentation - or we are the
			victim of an ongoing attack where the attacker is sending very
			fragmented traffic in order to decrease server performance).


*Default value is 4.*


**Example: Set ws_max_msg_chunks parameter**


```opensips
...
modparam("proto_ws", "ws_max_msg_chunks", 8)
...
```


#### trace_destination (string) {#param_trace_destination}


Trace destination as defined in the tracing module. Currently
		the only tracing module is **proto_hep**.
		Network events such as connect, accept and connection closed events
		shall be traced along with errors that could appear in the process.
		For each connection that is created an event containing information
		about http request and reply belonging to  web socket protocol
		handshake and network layer information shall be sent.


**WARNING:**A tracing module must be
			loaded in order for this parameter to work. (for example
			**proto_hep**).


*Default value is none(not defined).*


**Example: Set trace_destination parameter**


```opensips
...
modparam("proto_hep", "hep_id", "[hep_dest]10.0.0.2;transport=tcp;version=3")

modparam("proto_ws", "trace_destination", "hep_dest")
...
```


#### trace_on (int) {#param_trace_on}


This controls whether tracing for ws is on or not. You still need to define
			[trace destination](#param_trace_destination)in order to work, but this value will be
			controlled using mi function [mi ws trace](#mi_ws_trace).


**Example: Set trace_on parameter**


```opensips
...
modparam("proto_ws", "trace_on", 1)
...
```


#### trace_filter_route (string) {#param_trace_filter_route}


Define the name of a route in which you can filter which connections will
			be trace and which connections won't be. In this route you will have
			information regarding source and destination ips and ports for the current
			connection. To disable tracing for a specific connection the last call in
			this route must be **drop**, any other exit
			mode resulting in tracing the current connection ( of course you still
			have to define a [trace destination](#param_trace_destination) and trace must be
			on at the time this connection is opened.


**IMPORTANT**
			Filtering on ip addresses and ports can be made using **$si** and **$sp** for matching
			either the entity that is connecting to OpenSIPS or the entity to which
			OpenSIPS is connecting. The name might be misleading (**$si** meaning the source ip if you read the docs) but in reality
			it is simply the socket other than the OpenSIPS socket. In order to match
			OpenSIPS interface (either the one that accepted the connection or the one
			that initiated a connection) **$socket_in(ip)** (ip) and
			**$socket_in(port)** (port) can be used.


**WARNING:** IF [trace on](#param_trace_on) is
			set to 0 or tracing is deactived via the mi command [mi ws trace](#mi_ws_trace)
			this route won't be called.


**Example: Set trace_filter_route parameter**


```opensips
...
modparam("proto_ws", "trace_filter_route", "ws_filter")
...
/* all ws connections will go through this route if tracing is activated
 * and a trace destination is defined */
route[ws_filter] {
	...
	/* all connections opened from/by ip 1.1.1.1:8000 will be traced
	   on interface 1.1.1.10:5060(opensips listener)
	   all the other connections won't be */
	 if ( $si == "1.1.1.1" && $sp == 8000 &&
		$socket_in(ip) == "1.1.1.10"  && $socket_in(port) == 5060)
		exit;
	else
		drop;
}
...
```


#### require_origin (int) {#param_require_origin}


Controls whether the module should require the Origin header or not.


**Example: Set require_origin parameter**


```opensips
...
modparam("proto_ws", "require_origin", no)
...
```


### Exported MI Functions {#exported_mi_functions}


#### ws_trace {#mi_ws_trace}


Name: *ws_trace*


Parameters:


- trace_mode(optional): set ws tracing on and off. This parameter
						can be missing and the command will show the current tracing
						status for this module( on or off );
						Possible values:
						
							on
							off


MI FIFO Command Format:


```
			opensips-cli -x mi ws_trace on
			
```


## Frequently Asked Questions


**Q: Can OpenSIPS act as a WebSocket client?**


Yes, starting with OpenSIPS 2.2, it can act as a WebSocket client.


**Q: Does OpenSIPS support WebSocket message fragmentation?**


No, WebSocket fragmentation mechanism is not supported.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 97 | 48 | 4084 | 827 |
| 2. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 22 | 14 | 609 | 114 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 19 | 16 | 138 | 51 |
| 4. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 16 | 11 | 128 | 141 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 8 | 5 | 97 | 74 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 7 | 5 | 36 | 35 |
| 7. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | 5 | 3 | 5 | 3 |
| 8. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 3 | 1 | 4 | 4 |
| 9. | Dan Shields | 3 | 1 | 4 | 2 |
| 10. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 2 | 2 |


**All remaining contributors**: James Stanley, Julián Moreno Patiño.


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Mar 2017 - Apr 2024 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2017 - Nov 2023 |
| 3. | Vlad Paiu ([@vladpaiu](https://github.com/vladpaiu)) | Mar 2015 - Oct 2023 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Mar 2015 - Sep 2023 |
| 5. | James Stanley | Mar 2023 - Mar 2023 |
| 6. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2015 - Apr 2022 |
| 7. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Oct 2021 |
| 8. | Dan Shields | Aug 2021 - Aug 2021 |
| 9. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | May 2021 - May 2021 |
| 10. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |


**All remaining contributors**: Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)), Julián Moreno Patiño.


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)).


*Documentation Copyrights:*


Copyright © 2015 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
