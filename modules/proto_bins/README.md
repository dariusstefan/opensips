---
title: "proto_bins Module"
description: "This module implements a secure Binary communication protocol over TLS, to be used by the OpenSIPS clustering engine provided by the clusterer module."
---

## Admin Guide


### Overview {#overview}


This module implements a secure Binary communication protocol
		over TLS, to be used by the OpenSIPS clustering engine provided
		by the clusterer module.


Once loaded, you will be able to define BINS listeners in your
		configuration file by adding their IP and, optionally, a
		listening port, similar to this example:
	```c

...
socket= bins:127.0.0.1 		# change the listening IP
socket= bins:127.0.0.1:5557	# change the listening IP and port
...
```


### Dependencies {#dependencies}


#### OpenSIPS Modules


The following modules must be loaded before this module:


- *tls_openssl* or *tls_wolfssl*,
				depending on the desired TLS library
- *tls_mgm*.


#### External Libraries or Applications


The following libraries or applications must be installed before
		running OpenSIPS with this module loaded:


- *None*.


### Exported Parameters {#exported_parameters}


#### bins_port (integer) {#param_bins_port}


The default port to be used by all BINS listeners.


*Default value is 5556.*


```c title="Set bins_port parameter"
...
modparam("proto_bins", "bins_port", 5557)
...
```


#### bins_handshake_timeout (integer) {#param_bins_handshake_timeout}


Sets the timeout (in milliseconds) for the SSL/TLS handshake
		sequence to complete. It may be necessary to increase this
		value when using a CPU intensive cipher for the connection to
		allow time for keys to be generated and processed.


The timeout is invoked during acceptance of a new connection
		(inbound) and during the wait period when a new session is
		being initiated (outbound).


*Default value is 100.*


```c title="Set bins_handshake_timeout variable"
param("proto_tls", "bins_handshake_timeout", 200) # number of milliseconds

			
```


#### bins_send_timeout (integer) {#param_bins_send_timeout}


Sets the timeout (in milliseconds) for blocking send operations
		to complete.


The send timeout is invoked for all TLS write operations,
		excluding the handshake process (see: bins_handshake_timeout)


*Default value is 100 ms.*


```c title="Set bins_send_timeout parameter"
...
modparam("proto_bins", "bins_send_timeout", 200)
...
```


#### bins_max_msg_chunks (integer) {#param_bins_max_msg_chunks}


The maximum number of chunks in which a BINS message is
			expected to arrive via TCP. If a received packet is more
			fragmented than this, the connection is dropped (either the
			connection is very overloaded and this leads to high
			fragmentation - or we are the victim of an ongoing attack where
			the attacker is sending very fragmented traffic in order to
			decrease server performance).


*Default value is 32.*


```c title="Set bins_max_msg_chunks parameter"
...
modparam("proto_bins", "bins_max_msg_chunks", 8)
...
```


#### bins_async (integer) {#param_bins_async}


Specifies whether the TCP/TLS connect and write operations
			should be done in an asynchronous mode (non-blocking connect
			and write) or not. If disabled, OpenSIPS will block and wait
			for TCP/TLS operations like connect and write.


*Default value is 1 (enabled).*


```c title="Set bins_async parameter"
...
modparam("proto_bins", "bins_async", 0)
...
```


#### bins_async_max_postponed_chunks (integer) {#param_bins_async_max_postponed_chunks}


If bins_async is enabled, this specifies the maximum number of
			BINS messages that can be stashed for later/async writing. If
			the connection pending writes exceed this number, the
			connection will be marked as broken and dropped.


*Default value is 32.*


```c title="Set bins_async_max_postponed_chunks parameter"
...
modparam("proto_bins", "bins_async_max_postponed_chunks", 16)
...
```


#### bins_async_local_connect_timeout (integer) {#param_bins_async_local_connect_timeout}


If bin_async is enabled, this specifies the number of
			milliseconds that a connect will be tried in blocking mode
			(optimization). If the connect operation lasts more than this,
			the connect will go to async mode and will be passed to TCP
			MAIN for polling.


*Default value is 100 ms.*


```c title="Set bins_async_local_connect_timeout parameter"
...
modparam("proto_bins", "bins_async_local_connect_timeout", 200)
...
```


#### bins_async_handshake_timeout (integer) {#param_bins_async_handshake_timeout}


If *tls_async* is enabled, this specifies the
				number of milliseconds that a TLS handshake should be tried in blocking
				mode (optimization). If the handshake operation lasts more than this,
				the write will go to async mode and will be passed to tls MAIN for
				polling.


*Default value is 10 ms.*


```c title="Set bins_async_handshake_timeout parameter"
	...
	modparam("proto_tls", "bins_async_handshake_timeout", 100)
	...
	
```


#### trace_destination (string) {#param_trace_destination}


Trace destination as defined in the tracing module. Currently
		the only tracing module is **proto_hep**.
		Network events such as connect, accept and connection closed events
		shall be traced along with errors that could appear in the process.
		For each connection that is created an event containing information
		about the client and server certificates, master key and network layer
		information shall be sent.


**WARNING:**A tracing module must be
			loaded in order for this parameter to work. (for example
			**proto_hep**).


*Default value is none(not defined).*


```c title="Set trace_destination parameter"
...
modparam("proto_hep", "hep_id", "[hep_dest]10.0.0.2;transport=tcp;version=3")

modparam("proto_bins", "trace_destination", "hep_dest")
...
```


#### trace_on (int) {#param_trace_on}


This controls whether tracing for tls is on or not. You still need to define
			[trace destination](#param_trace_destination)in order to work, but this value will be
			controlled using mi function [mi bins trace](#mi_bins_trace).


```c title="Set trace_on parameter"
...
modparam("proto_bins", "trace_on", 1)
...
```


### Exported MI Functions {#exported_mi_functions}


#### bins_trace {#mi_bins_trace}


Name: *bins_trace*


Parameters:


- trace_mode(optional): set bins tracing on and off. This parameter
						can be missing and the command will show the current tracing
						status for this module( on or off );
						Possible values:
						
							on
							off


MI FIFO Command Format:


```c
			opensips-cli -x mi bins_trace on
			
```


## Contributors {#contributors}


### By Commit Statistics {#contrib_commit_statistics}


**Top contributors by DevScore^(1)^, authored commits^(2)^ and lines added/removed^(3)^**


|  | Name | DevScore | Commits | Lines ++ | Lines -- |
| --- | --- | --- | --- | --- | --- |
| 1. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | 15 | 4 | 1118 | 24 |
| 2. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | 5 | 3 | 13 | 7 |
| 3. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | 4 | 2 | 5 | 3 |
| 4. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | 4 | 2 | 4 | 5 |
| 5. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | 3 | 1 | 2 | 2 |


*(1) DevScore = author_commits + author_lines_added / (project_lines_added / project_commits) + author_lines_deleted / (project_lines_deleted / project_commits)*


*(2) including any documentation-related commits, excluding merge commits. Regarding imported patches/code, we do our best to count the work on behalf of the proper owner, as per the "fix_authors" and "mod_renames" arrays in opensips/doc/build-contrib.sh. If you identify any patches/commits which do not get properly attributed to you, please [submit a pull request](https://github.com/OpenSIPS/opensips/pulls)* which extends "fix_authors" and/or "mod_renames".


*(3) ignoring whitespace edits, renamed files and auto-generated files*


### By Commit Activity {#contrib_commit_activity}


**Most recently active contributors^(1)^ to this module**


|  | Name | Commit Activity |
| --- | --- | --- |
| 1. | Bogdan-Andrei Iancu ([@bogdan-iancu](https://github.com/bogdan-iancu)) | Apr 2021 - May 2023 |
| 2. | Maksym Sobolyev ([@sobomax](https://github.com/sobomax)) | Feb 2023 - Feb 2023 |
| 3. | Liviu Chircu ([@liviuchircu](https://github.com/liviuchircu)) | Apr 2022 - Apr 2022 |
| 4. | Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)) | Feb 2021 - Oct 2021 |
| 5. | Nick Altmann ([@nikbyte](https://github.com/nikbyte)) | May 2021 - May 2021 |


*(1) including any documentation-related commits, excluding merge commits*


## Documentation {#documentation}


### Contributors {#documentation_contributors}


**Last edited by:** Vlad Patrascu ([@rvlad-patrascu](https://github.com/rvlad-patrascu)).


*Documentation Copyrights:*


Copyright © 2015 [www.opensips-solutions.com](http://www.opensips-solutions.com/)
