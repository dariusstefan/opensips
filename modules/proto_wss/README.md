---
title: "proto_wss Module"
description: "The WSS (Secure WebSocket) module provides the ability to communicate with a WebSocket ([RFC 6455](http://tools.ietf.org/html/rfc6455)) client or server over a secure (TLS encrypted) channel. As part of the [WebRTC](https://webrtc.org/) specifications, this protocol can be used to provide se..."
---

## Admin Guide


### Overview {#overview}


The WSS (Secure WebSocket) module provides the ability to communicate with
	a WebSocket ([RFC
		6455](http://tools.ietf.org/html/rfc6455)) client or server over a secure (TLS encrypted) channel.
	As part of the [WebRTC](https://webrtc.org/)
	specifications, this protocol can be used to provide secure VoIP calls to
	HTTPS enabled browsers.


This module behaves as any other transport protocol module: in order to
	use it, you must define one or more listeners that will handle the secure
	WebSocket traffic, *after* the `mpath`
	parameter:
	```c

...
mpath=/path/to/modules
...
socket=wss:10.0.0.1			# change with the listening IP
socket=wss:10.0.0.1:5060	# change with the listening IP and port
...
```
	Besides that, you need to define the TLS parameters for securing the connection. This is done through the *tls_mgm* module interface, similar to the *proto_tls* module:
	```c

modparam("tls_mgm", "certificate", "/certs/biloxy.com/cert.pem")
modparam("tls_mgm", "private_key", "/certs/biloxy.com/privkey.pem")
modparam("tls_mgm", "ca_list", "/certs/wellknownCAs")
modparam("tls_mgm", "tls_method", "tlsv1")
modparam("tls_mgm", "verify_cert", "1")
modparam("tls_mgm", "require_cert", "1")
```
	Check the *tls_mgm* module documentation for more info.


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *tls_openssl* or *tls_wolfssl*,
				depending on the desired TLS library
- *tls_mgm*.


#### Dependencies of external libraries


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


All these parameters can be used from the opensips.cfg file,
		to configure the behavior of OpenSIPS-WSS.


#### listen=interface {#param_listen}


This is a global parameter that specifies what interface/IP and
			port should handle WSS traffic.


```c title="Set listen variable"
...
socket= wss:1.2.3.4:44344
...
				
```


#### wss_port (integer) {#param_wss_port}


The default port to be used for all WSS related operation. Be 
			careful as the default port impacts both the SIP listening part 
			(if no port is defined in the WSS listeners) and the SIP sending 
			part (if the destination WSS URI has no explicit port).


If you want to change only the listening port for WSS, use the port
			option in the SIP listener defintion.


*Default value is 443.*


```c title="Set wss_port variable"
...
modparam("proto_wss", "wss_port", 44344)
...
				
```


#### wss_max_msg_chunks (integer) {#param_wss_max_msg_chunks}


The maximum number of chunks in which a SIP message is expected to
			arrive via WSS. If a received packet is more fragmented than this,
			the connection is dropped (either the connection is very
			overloaded and this leads to high fragmentation - or we are the
			victim of an ongoing attack where the attacker is sending very
			fragmented traffic in order to decrease server performance).


*Default value is 4.*


```c title="Set wss_max_msg_chunks parameter"
...
modparam("proto_wss", "wss_max_msg_chunks", 8)
...
```


#### wss_resource (string) {#param_wss_resource}


The resource queried for when a WebSocket handshake is initiated.


*Default value is "/".*


```c title="Set wss_resource parameter"
...
modparam("proto_wss", "wss_resource", "/wss")
...
```


#### wss_handshake_timeout (integer) {#param_wss_handshake_timeout}


This parameter specifies the time in milliseconds the proto_wss module
			waits for a WebSocket handshake reply from a WebSocket server.


*Default value is 100.*


```c title="Set wss_handshake_timeout parameter"
...
modparam("proto_wss", "wss_handshake_timeout", 300)
...
```


#### cert_check_on_conn_reusage (integer) {#param_tls_cert_check_on_conn_reusage}


This parameter turns on or off the extra checking/matching of the
		TLS domain (SSL certificate) when comes to reusing an existing TLS
		connection. Without this extra check, only IP and port of the
		connections will be check (in order to re-use an existing connection).
		With this extra check, the connection to be reused must have the same
		SSL certificate as the one set for the current signaling operation.


This checking is done only when comes to send SIP traffic via TLS and
		it is applied only against connections that were created / initiated 
		by OpenSIPS (as TLS client). Any accepte connection (as TLS server) will
		automatically match (the extra test will be skipped).


*Default value is 0 (disabled).*


```c title="Set cert_check_on_conn_reusage parameter"
...
modparam("proto_wss", "cert_check_on_conn_reusage", 1)
...
```


#### trace_destination (string) {#param_trace_destination}


Trace destination as defined in the tracing module. Currently
		the only tracing module is **proto_hep**.
		Network events such as connect, accept and connection closed events
		shall be traced along with errors that could appear in the process.
		For each connection that is created an event containing information
		about the client and server certificate, master key, http request and
		reply belonging to  web socket protocol handshake and network layer
		information shall be sent.


**WARNING:**A tracing module must be
			loaded in order for this parameter to work. (for example
			**proto_hep**).


*Default value is none(not defined).*


```c title="Set trace_destination parameter"
...
modparam("proto_hep", "hep_id", "[hep_dest]10.0.0.2;transport=tcp;version=3")

modparam("proto_wss", "trace_destination", "hep_dest")
...
```


#### trace_on (int) {#param_trace_on}


This controls whether tracing for wss is on or not. You still need to define
			[trace destination](#param_trace_destination)in order to work, but this value will be
			controlled using mi function [mi wss trace](#mi_wss_trace).


```c title="Set trace_on parameter"
...
modparam("proto_wss", "trace_on", 1)
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
			set to 0 or tracing is deactived via the mi command [mi wss trace](#mi_wss_trace)
			this route won't be called.


```c title="Set trace_filter_route parameter"
...
modparam("proto_wss", "trace_filter_route", "wss_filter")
...
/* all wss connections will go through this route if tracing is activated
 * and a trace destination is defined */
route[wss_filter] {
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


#### wss_tls_handshake_timeout (integer) {#param_wss_tls_handshake_timeout}


Sets the timeout (in milliseconds) for the SSL handshake sequence to complete.
			It may be necessary to increase this value when using a CPU intensive cipher
			for the connection to allow time for keys to be generated and processed.


The timeout is invoked during acceptance of a new connection (inbound) and
			during the wait period when a new session is being initiated (outbound).


*Default value is 100.*


```c title="Set wss_tls_handshake_timeout variable"
param("proto_wss", "wss_tls_handshake_timeout", 200) # number of milliseconds

			
```


#### wss_send_timeout (integer) {#param_wss_send_timeout}


Sets the timeout (in milliseconds) for the send operations to complete


The send timeout is invoked for all TLS write operations, excluding
			the handshake process (see: wss_tls_handshake_timeout)


*Default value is 100.*


```c title="Set wss_send_timeout variable"
modparam("proto_wss", "wss_send_timeout", 200) # number of milliseconds

			
```


#### require_origin (int) {#param_require_origin}


Controls whether the module should require the Origin header or not.


```c title="Set require_origin parameter"
modparam("proto_wss", "require_origin", no)

		
```


### Exported MI Functions {#exported_mi_functions}


#### wss_trace {#mi_wss_trace}


Name: *wss_trace*


Parameters:


- trace_mode(optional): set wss tracing on and off. This parameter
						can be missing and the command will show the current tracing
						status for this module( on or off );
						Possible values:
						
							on
							off


MI FIFO Command Format:


```c
			opensips-cli -x mi wss_trace on
			
```


## Frequently Asked Questions


**Q: Does OpenSIPS support fragmented Secure WebSocket messages?**


No, the WebSocket fragmentation mechanism is not supported.


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | 33 | 23 | 785 | 126 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 19 | 15 | 243 | 27 |
| 3. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 17 | 13 | 131 | 113 |
| 4. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | 15 | 10 | 362 | 19 |
| 5. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 10 | 8 | 48 | 59 |
| 6. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 6 | 4 | 17 | 33 |
| 7. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | 3 | 1 | 3 | 5 |
| 8. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 3 | 1 | 2 | 2 |
| 9. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | 3 | 1 | 1 | 1 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2017 - Nov 2023 |
| 2. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Jan 2016 - May 2023 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Mar 2016 - Apr 2022 |
| 4. | Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)) | Jan 2016 - Jan 2022 |
| 5. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | May 2017 - Oct 2021 |
| 6. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | May 2021 - May 2021 |
| 7. | Dan Pascu ([@danpascu](https://github.com/danpascu)) | Jan 2020 - Jan 2020 |
| 8. | Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)) | Jun 2018 - Jun 2018 |
| 9. | Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)) | Mar 2017 - Apr 2017 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)), Razvan Crainea ([@razvancrainea](https://github.com/razvancrainea)), Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)), Peter Lemenkov ([@lemenkov](https://github.com/lemenkov)), Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)), Ionut Ionita ([@ionutrazvanionita](https://github.com/ionutrazvanionita)).


*Documentation Copyrights:*


Copyright © 2015 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
